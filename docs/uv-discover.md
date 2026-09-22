# Using uv on NCCS Discover and NAS

On NCCS Discover and NAS, configure `uv` to keep its executable, cache, managed Python installations, and tools on `$NOBACKUP` storage rather than in your backed-up home directory. Home-directory quotas on both systems are intentionally small, and `uv` caches, managed Python installations, tools, and virtual environments can consume substantial space.

## Configure uv

Add the following to `~/.bashrc`:

```bash
export UV_ROOT="$NOBACKUP/uv"

export UV_CACHE_DIR="$UV_ROOT/cache"
export UV_PYTHON_CACHE_DIR="$UV_ROOT/cache/python"
export UV_TOOL_DIR="$UV_ROOT/tools"
export UV_PYTHON_INSTALL_DIR="$UV_ROOT/python"
export UV_TOOL_BIN_DIR="$UV_ROOT/bin"
export UV_PYTHON_BIN_DIR="$UV_ROOT/bin"
export UV_INSTALL_DIR="$UV_ROOT/bin"

# Use copies instead of hard links across filesystems.
export UV_LINK_MODE=copy

export PATH="$UV_ROOT/bin:$PATH"
```

Create the directories and reload the shell configuration:

```bash
mkdir -p "$UV_ROOT"/{bin,cache,tools,python}
source ~/.bashrc
```

## Install uv

Install `uv` with its standalone installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Verify that the configured location is being used:

```bash
command -v uv
uv --version
```

The path reported by `command -v uv` should be inside `$UV_ROOT/bin`, for example `$NOBACKUP/uv/bin/uv`.

To update an installation made with the standalone installer:

```bash
uv self update
```

## Create an environment

Choose a working directory on project or `$NOBACKUP` storage. Do not create environments under `$HOME`, because installed packages live in the virtual environment as well as in the cache.

```bash
mkdir -p "$NOBACKUP/uvtest"
cd "$NOBACKUP/uvtest"
uv venv --python 3.12
```

This creates `.venv`. Activation is optional because `uv` detects a `.venv` in the current directory:

```bash
uv pip install PACKAGE_NAME
```

To activate the environment for commands outside `uv`:

```bash
source .venv/bin/activate
```

## Manage the cache

The cache can grow over time. Check its location and remove unused entries with:

```bash
uv cache dir
uv cache prune
```

To remove all cached data:

```bash
uv cache clean
```

For project-oriented workflows, see the [general uv guide](uv.md).
