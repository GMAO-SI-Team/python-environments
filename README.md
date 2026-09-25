# Python Environments

Guidance for creating Python environments used by the GMAO SI Team without relying on Anaconda's `defaults` channel.

## Choose an environment

| If you need... | Use... |
| --- | --- |
| A project-specific Python environment (recommended for most new work) | [`uv`](docs/uv.md) |
| A familiar Conda workflow when moving from Anaconda, or conda-forge packages and Conda-managed native dependencies | [Miniforge + `mamba`](docs/miniforge.md) |
| The same Python stack as a particular GEOS installation | [GEOSpyD](https://github.com/GMAO-SI-Team/GEOSpyD) |

## uv

[`uv`](https://docs.astral.sh/uv/) is a fast Python package and project manager and our recommended starting point for most new work. Prefer one isolated environment per project, recorded in that project's `pyproject.toml` and `uv.lock`, rather than extending one global environment. Start with the [`uv` guide](docs/uv.md).

For NCCS Discover or NAS, follow the guide's [cluster-specific setup](docs/uv.md#nccs-discover-and-nas) before installing `uv`.

`uv` installs packages from Python package indexes such as PyPI. Review the licenses of dependencies used by your project.

## Miniforge + mamba

[Miniforge](https://github.com/conda-forge/miniforge) is the closest match to an Anaconda-style Conda workflow: it provides `conda` and `mamba`, but starts with conda-forge rather than Anaconda's `defaults` channel. Unlike GEOSpyD, it does not come with a large preselected Python stack; create small environments and add only the packages you need. When moving from Anaconda, create fresh conda-forge environments rather than reusing environments that contain `defaults` packages. The [Miniforge guide](docs/miniforge.md) covers workstations and keeping the installation, package cache, and environments off `$HOME` on Discover and NAS, with explicit conda-forge-only commands and package-origin checks.

On a personal workstation, `conda init` is optional; on Discover and NAS, use Miniforge on demand rather than initializing Conda in shell startup files, so it does not interfere with module-provided environments. In GEOS model sessions, `module load GEOSpyD` should provide the Python environment; use personal Miniforge environments in separate shells.

## GEOSpyD

[GEOSpyD](https://github.com/GMAO-SI-Team/GEOSpyD) is useful when you need to match the Python stack used by a particular GEOS installation; choose the corresponding GEOSpyD version. It includes many packages you may not need. Its installer uses [Miniforge](https://github.com/conda-forge/miniforge) and conda-forge with strict channel priority and `nodefaults`, then checks that the resulting Conda environment contains no packages from the `defaults` channel.

Follow the installation instructions in the [GEOSpyD repository](https://github.com/GMAO-SI-Team/GEOSpyD#readme).

## Scope

This repository describes environment-management approaches, not legal advice or approval of every dependency. Follow applicable organizational guidance and the licenses of the software you install.
