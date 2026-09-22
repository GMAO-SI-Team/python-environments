# Python Environments

Guidance for creating Python environments used by the GMAO SI Team without relying on Anaconda's `defaults` channel.

## Choose an environment

| If you need... | Use... |
| --- | --- |
| A complete, curated scientific Python stack aligned with supported cluster installations | [GEOSpyD](https://github.com/GMAO-SI-Team/GEOSpyD) |
| A lightweight, project-specific Python environment | [`uv`](docs/uv.md) |
| `uv` on NCCS Discover or NAS | [`uv` on NCCS Discover and NAS](docs/uv.md#nccs-discover-and-nas) |

## GEOSpyD

[GEOSpyD](https://github.com/GMAO-SI-Team/GEOSpyD) provides a large, curated scientific Python stack. Its installer uses [Miniforge](https://github.com/conda-forge/miniforge) and conda-forge with strict channel priority and `nodefaults`, then checks that the resulting Conda environment contains no packages from the `defaults` channel.

Follow the installation instructions in the [GEOSpyD repository](https://github.com/GMAO-SI-Team/GEOSpyD#readme).

## uv

[`uv`](https://docs.astral.sh/uv/) is a fast Python package and project manager. Prefer one isolated environment per project, recorded in that project's `pyproject.toml` and `uv.lock`, rather than extending one global environment. Start with the [`uv` guide](docs/uv.md).

`uv` installs packages from Python package indexes such as PyPI. Review the licenses of dependencies used by your project.

## Scope

This repository describes environment-management approaches, not legal advice or approval of every dependency. Follow applicable organizational guidance and the licenses of the software you install.
