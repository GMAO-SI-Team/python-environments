# Using uv

[`uv`](https://docs.astral.sh/uv/) is a Python package and project manager. For a local macOS or Linux workstation, its defaults are normally appropriate.

## Install uv

Install `uv` using its standalone installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Open a new terminal, then confirm the installation:

```bash
uv --version
```

For other installation methods, see the [uv installation documentation](https://docs.astral.sh/uv/getting-started/installation/).

## Start a project

Create a project directory and initialize it:

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

`uv` records direct dependencies in `pyproject.toml` and resolves exact versions in `uv.lock`. Commit both files to version control for reproducible environments.

## Use an existing project

From a project directory containing `pyproject.toml` and, when available, `uv.lock`:

```bash
uv sync
uv run python
```

`uv sync` creates or updates the project's `.venv` environment. It does not need to be activated to use `uv run`.

## Update uv

For installations made with the standalone installer:

```bash
uv self update
```

See the [uv documentation](https://docs.astral.sh/uv/) for workflows beyond this basic project setup.
