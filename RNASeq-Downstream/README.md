# RNASeq-Downstream

R environment for downstream bulk RNA-seq analysis: exploratory data analysis, differential expression, apeglm shrinkage, functional enrichment, and plotting.

> [!IMPORTANT]
> This environment is for your **LOCAL** machine (Apple silicon Mac, `osx-arm64`) only. Do **NOT** install it on the cluster or on a cluster volume that is mounted on your Mac. Pixi installs the environment next to `pixi.toml`, so an install on a mounted volume writes macOS files into cluster storage. Keep this folder on your local disk.

## Quick Start

From a local folder:

```
pixi install && pixi run install-rgeneda 
```

## Setup

1. Copy `pixi.toml` and `pixi.lock` to a folder on your local disk.
2. Install the environment and run the post-install task:

```bash
cd ~/path/to/RNASeq-Downstream
pixi install --locked
pixi run install-rgeneda
```

3. Make sure that the environment loads:

```bash
pixi run check
```

`check` loads the main packages and RGenEDA, and prints `sessionInfo()`. If RGenEDA is not installed, `check` runs `install-rgeneda` first.

## Tasks

| Task | When to run | What it does |
|------|-------------|--------------|
| `install-rgeneda` | One time, after `pixi install` | Installs RGenEDA from GitHub. It does not install or upgrade other packages, because conda supplies all of them. |
| `check` | After setup, or to test the environment | Loads the packages and prints `sessionInfo()`. |

RGenEDA is not recorded in `pixi.lock`. The task installs the version that is on GitHub when you run it.

## Use in Positron or RStudio

Select the R interpreter at:

```
<this folder>/.pixi/envs/default/bin/R
```

You can create a new pixi task to open the project using the installed environment. 

If using Positron, add a task:

```
[task.positron]
cmd = "positron /Volumes/GMBSR_bioinfo/client/project_folder/"
```

then open the project by running

```
pixi run positron
```

If using RStudio

```
[task.rstudio]
cmd = "open -n -a Rstudio /Volumes/GMBSR_bioinfo/client/project_folder/project.Rproj"

```

then open the project by running

```
pixi run rstudio
```
