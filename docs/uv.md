# Using uv

[`uv`](https://docs.astral.sh/uv/) is a Python package and project manager. For a local macOS or Linux workstation, its defaults are normally appropriate.

## How to use uv

Use `uv` primarily on a [per-project basis](https://docs.astral.sh/uv/concepts/projects/): each project has its own `.venv` environment and records its dependencies in `pyproject.toml` and `uv.lock`. This keeps unrelated projects from changing one another's dependencies and lets another user recreate the same environment.

This differs from [GEOSpyD](https://github.com/GMAO-SI-Team/GEOSpyD), which is a broad, curated stack intended to make many scientific packages available from one installation. If you need a package not included in your own GEOSpyD installation, it can be added with `pip`, but doing so changes that environment and can introduce version conflicts. Use a `uv` project when you want isolation or a dependency set specific to your work.

## NCCS Discover and NAS

On NCCS Discover and NAS, configure `uv` to keep its executable, cache, managed Python installations, and tools on `$NOBACKUP` storage rather than in your backed-up home directory. Home-directory quotas on both systems are intentionally small, and `uv` caches, managed Python installations, tools, and virtual environments can consume substantial space.

For Bash, add the following to `~/.bashrc`:

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

For `tcsh`, add the following to `~/.tcshrc`:

```tcsh
setenv UV_ROOT "$NOBACKUP/uv"

setenv UV_CACHE_DIR "$UV_ROOT/cache"
setenv UV_PYTHON_CACHE_DIR "$UV_ROOT/cache/python"
setenv UV_TOOL_DIR "$UV_ROOT/tools"
setenv UV_PYTHON_INSTALL_DIR "$UV_ROOT/python"
setenv UV_TOOL_BIN_DIR "$UV_ROOT/bin"
setenv UV_PYTHON_BIN_DIR "$UV_ROOT/bin"
setenv UV_INSTALL_DIR "$UV_ROOT/bin"

# Use copies instead of hard links across filesystems.
setenv UV_LINK_MODE copy

setenv PATH "$UV_ROOT/bin:$PATH"
```

Create the directories, then reload the appropriate shell configuration:

```bash
mkdir -p "$UV_ROOT"/{bin,cache,tools,python}
```

```bash
# Bash
source ~/.bashrc
```

```tcsh
# tcsh
source ~/.tcshrc
```

After reloading the shell configuration, install `uv` with its standalone installer. `UV_INSTALL_DIR` directs the installer to place the executable in `$NOBACKUP/uv/bin`:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
command -v uv
uv --version
```

The path reported by `command -v uv` should be inside `$UV_ROOT/bin`, for example `$NOBACKUP/uv/bin/uv`. On these systems, create project directories and direct virtual environments on project or `$NOBACKUP` storage, not under `$HOME`.

## Install uv on a local workstation

On a local macOS or Linux workstation, install `uv` using its standalone installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Open a new terminal, then confirm the installation:

```bash
uv --version
```

The standalone installer normally adds its installation directory to `PATH`. On macOS and Linux, its default installation directory is `$HOME/.local/bin`. If `uv --version` is not found after opening a new terminal, confirm that `$HOME/.local/bin` is on `PATH` and follow the installer output to update the appropriate shell startup file.

For other installation methods and installer options, see the [uv installation documentation](https://docs.astral.sh/uv/getting-started/installation/) and [installer reference](https://docs.astral.sh/uv/reference/installer/).

## Start a project

Create a project directory and initialize it. On Discover or NAS, use project or `$NOBACKUP` storage for the project directory:

```bash
mkdir my-project
cd my-project
uv init
```

Add dependencies to the project:

```bash
uv add numpy xarray
```

Run Python within the project's managed environment:

```bash
uv run python
```

`uv` records direct dependencies in `pyproject.toml` and resolves exact versions in `uv.lock`. Commit both files to version control for reproducible environments. For project structure, dependency management, and locking details, see the [uv project documentation](https://docs.astral.sh/uv/concepts/projects/).

## Activate a project environment

`uv run` automatically uses the project's `.venv`, so activation is optional. To use the environment's `python`, `python3`, and installed command-line programs directly, activate it from the project directory instead. See the upstream guide to [using Python environments](https://docs.astral.sh/uv/pip/environments/) for more detail.

For Bash or similar shells:

```bash
source .venv/bin/activate
python3
```

For `tcsh`:

```tcsh
source .venv/bin/activate.csh
python3
```

The shell prompt normally indicates that the environment is active. Run `deactivate` when finished. Do not activate or modify another project's `.venv`; use that project's directory and environment instead.

## Use an existing project

From a project directory containing `pyproject.toml` and, when available, `uv.lock`:

```bash
uv sync
uv run python
```

`uv sync` creates or updates the project's `.venv` environment. It does not need to be activated to use `uv run`.

## Quick or legacy environments

For a short-lived experiment or a project that does not yet use `pyproject.toml`, create an environment directly:

```bash
uv venv --python 3.12
uv pip install numpy xarray
```

Use this workflow sparingly. When the work needs to be repeated, shared, or committed to a repository, initialize a `uv` project and add the dependencies with `uv add` instead. That records the requested dependencies and creates a lock file.

For command-line applications that should be available outside a particular project, use [`uv tool`](https://docs.astral.sh/uv/concepts/tools/) rather than adding them to a project's environment. For example:

```bash
uv tool install ruff
```

`uv` manages [Python interpreters](https://docs.astral.sh/uv/concepts/python-versions/) separately from project environments. A managed Python installation is not a shared package environment.

## Manage the cache

The [cache](https://docs.astral.sh/uv/concepts/cache/) can grow over time. Check its location and remove unused entries with:

```bash
uv cache dir
uv cache prune
```

To remove all cached data:

```bash
uv cache clean
```

## Update uv

For installations made with the standalone installer:

```bash
uv self update
```

See the [uv documentation](https://docs.astral.sh/uv/) for workflows beyond this basic project setup.
