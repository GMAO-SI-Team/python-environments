# Using Miniforge and mamba

For most new projects, [use `uv` instead](uv.md). If you need packages from conda-forge or Conda-managed native libraries, [Miniforge](https://github.com/conda-forge/miniforge) is a smaller alternative to [GEOSpyD](https://github.com/GMAO-SI-Team/GEOSpyD): Miniforge provides `conda` and `mamba`, and you choose what goes into each environment. Use GEOSpyD when you need to match a particular GEOS Python stack.

The examples below use Bash and `mamba` for package installation. They explicitly select **only conda-forge** for every package operation, even if an existing `~/.condarc` or `~/.mambarc` adds other channels. `nodefaults` in a shared `environment.yml` provides the same intent for recreating the environment. Do not add `defaults` or `repo.anaconda.com` to these environments.

## Install on a local macOS or Linux workstation

Choose the [upstream installer](https://github.com/conda-forge/miniforge#unix-like-platforms-macos-linux--wsl) for your OS and architecture. Use batch mode so the installer does not change shell startup files:

```bash
export MINIFORGE_ROOT="$HOME/miniforge3"
curl -fLsS -o "Miniforge3-$(uname)-$(uname -m).sh" \
  "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
bash "Miniforge3-$(uname)-$(uname -m).sh" -b -p "$MINIFORGE_ROOT"
```

Use a new installation path; the installer will not overwrite an existing directory. The base environment is for managing environments, not for installing your project's packages. You can remove the downloaded installer after a successful installation. If you use the interactive installer instead, decline its offer to run `conda init`.

## Install on NCCS Discover or NAS

On Discover and NAS, `$NOBACKUP` is already defined and `$HOME` quotas are small. Keep the installer, Miniforge installation, package cache, and environments on `$NOBACKUP` (or suitable project storage):

```bash
export MINIFORGE_ROOT="$NOBACKUP/miniforge3"
curl -fLsS -o "$NOBACKUP/Miniforge3-$(uname)-$(uname -m).sh" \
  "https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh"
bash "$NOBACKUP/Miniforge3-$(uname)-$(uname -m).sh" -b -p "$MINIFORGE_ROOT"
```

The installer URL selects the Linux architecture of the machine running the command; check the [upstream platform requirements](https://github.com/conda-forge/miniforge#requirements-and-installers) if the installer fails. Do not reuse a pre-existing installation path. The downloaded installer can be removed after installation.

An existing `MAMBA_ROOT_PREFIX` (for example, one pointing to a separate `mamba-cache`) is not a portable setting for the Miniforge package cache. Set it to this installation's root while using this Miniforge, and specify the package cache and environment directories separately as below.

## Set up a Bash session when you need Miniforge

In a terminal where you want to use Miniforge, set `MINIFORGE_ROOT` to the path used above (`$HOME/miniforge3` on a workstation or `$NOBACKUP/miniforge3` on Discover/NAS), then run:

```bash
export MAMBA_ROOT_PREFIX="$MINIFORGE_ROOT"
export CONDA_PKGS_DIRS="$MINIFORGE_ROOT/pkgs"
export CONDA_ENVS_PATH="$MINIFORGE_ROOT/envs"
export CONDA_CHANNEL_PRIORITY=strict
source "$MINIFORGE_ROOT/etc/profile.d/conda.sh"
```

**Do not run `conda init` or put `conda.sh`, `conda activate`, or a Miniforge `PATH` change in `~/.bashrc` or another shell startup file.** Even inside the interactive-shell block in the [suggested NCCS shell configuration](https://github.com/GEOS-ESM/GEOSgcm/wiki/Suggested-NCCS-Resources#311-shell-configuration), automatic Conda setup can interfere with the GEOSpyD environment supplied through `g5_modules` for GEOS models. In a GEOS model session that uses `module load GEOSpyD`, let that module's Python take precedence: do not run this Miniforge setup block or activate a personal Conda environment in the same session. Use a separate shell for your own Miniforge environments. Run the block above only when you want this Miniforge in the current shell; in batch jobs using Miniforge, run it explicitly in the job script. The installer and all large package files stay outside `$HOME` on Discover/NAS.

Use `"$MINIFORGE_ROOT/bin/mamba"` in the commands below so that an existing `mamba` elsewhere on `PATH` is not selected. `CONDA_PKGS_DIRS` and `CONDA_ENVS_PATH` make cache and environment placement explicit for both mamba and conda; keeping them on the same filesystem lets packages be hard-linked where possible. Strict channel priority is an additional safeguard, but it does not exclude `defaults` on its own.

## Create and use an environment

Choose a distinct environment path for each project. The full conda-forge URL and `--override-channels` prevent configured channels (including `defaults`) from being used for these commands, even if other Conda installations have modified your configuration:

```bash
export ENV_PREFIX="$MINIFORGE_ROOT/envs/my-project"
"$MINIFORGE_ROOT/bin/mamba" create -p "$ENV_PREFIX" \
  --override-channels -c https://conda.anaconda.org/conda-forge \
  python=3.12 numpy xarray
conda activate "$ENV_PREFIX"
python --version
```

The `conda.anaconda.org/conda-forge` hostname is the conda-forge package channel; it is **not** Anaconda's commercial `defaults` channel at `repo.anaconda.com`.

To add packages later, target this environment explicitly:

```bash
"$MINIFORGE_ROOT/bin/mamba" install -p "$ENV_PREFIX" \
  --override-channels -c https://conda.anaconda.org/conda-forge \
  scipy cartopy
```

Replace these example packages with what you actually need. Group related additions when possible to let the solver choose compatible versions. Run `conda deactivate` when finished. Avoid installing project packages into `base`.

## Share an environment

Keep a small `environment.yml` with the project, listing the packages you intentionally requested. For example:

```yaml
channels:
  - https://conda.anaconda.org/conda-forge
  - nodefaults
dependencies:
  - python=3.12
  - numpy
  - xarray
  - scipy
  - cartopy
```

`nodefaults` tells Conda not to append default channels when resolving this file. On Discover/NAS, use a project directory outside `$HOME` for the file. To create another environment from it, explicitly choose the prefix and channel again:

```bash
export ENV_PREFIX="$MINIFORGE_ROOT/envs/my-project-copy"
"$MINIFORGE_ROOT/bin/mamba" create -p "$ENV_PREFIX" -f environment.yml \
  --override-channels -c https://conda.anaconda.org/conda-forge
```

Continue using the explicit conda-forge-only flags for future installs; a YAML file does not control ad hoc `mamba install` commands. This dependency list is a shareable recipe, not an exact lock of every transitive package version.

## Check storage and channels

Before installing large stacks, check the active installation and configuration:

```bash
"$MINIFORGE_ROOT/bin/conda" info
"$MINIFORGE_ROOT/bin/conda" config --show-sources
"$MINIFORGE_ROOT/bin/conda" config --show channels channel_priority pkgs_dirs envs_dirs
```

Confirm the base, package cache, and environment directories are under the intended location. Existing configuration sources may list `defaults`; the explicit `--override-channels -c https://conda.anaconda.org/conda-forge` flags in this guide exclude them for package operations. Inspect the proposed channel and packages before accepting each transaction. After creating an environment, check the installed package origins:

```bash
"$MINIFORGE_ROOT/bin/conda" list -p "$ENV_PREFIX" --show-channel-urls
"$MINIFORGE_ROOT/bin/conda" list -p "$ENV_PREFIX" --explicit
```

Conda-installed packages should come from conda-forge; the explicit list should not contain URLs under `repo.anaconda.com`. If it does, stop using that environment and recreate it with the channel-only commands above. `pip` packages, if you choose to add any, come from Python package indexes and are not covered by this conda-forge channel check. Review their licenses separately.

See the [Miniforge documentation](https://github.com/conda-forge/miniforge#readme) and [mamba guide](https://mamba.readthedocs.io/en/latest/user_guide/mamba.html) for more options.
