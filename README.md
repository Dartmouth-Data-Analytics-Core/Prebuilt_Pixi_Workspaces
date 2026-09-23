# GDSC Prebuilt Pixi Workspaces

![Status](https://img.shields.io/badge/status-in%20progress-yellow)

Prebuilt [Pixi](https://pixi.sh) workspaces for common genomics and single-cell analyses. Each workspace has a `pixi.toml` (the packages we ask for) and a `pixi.lock` (the exact versions that were solved and tested). Installing from the lock file gives you the same environment we use, so you can start an analysis without resolving package conflicts yourself.

New to Pixi? Start with the [GDSC Pixi SOP](https://github.com/Dartmouth-Data-Analytics-Core/GDSC-Pixi-SOP/wiki).

## Workspaces

| Workspace | Language | Platforms | Use it for |
|-----------|----------|-----------|------------|
| [Seurat_v5](Seurat_v5) | R | osx-arm64 | scRNA-seq analysis with Seurat v5 |
| [CellBender](CellBender) | Python | linux-64 (GPU) | Ambient RNA removal with CellBender 0.4.0 |
| [CellRank2](CellRank2) | Python | linux-64 | Fate mapping and joint-RNA/ATAC velocity with CellRank 2, moscot, and MultiVelo |
| [MELD](MELD) | Python | linux-64 | Condition-associated likelihoods with MELD and PHATE |
| [SpatialData](SpatialData) | Python | osx-arm64, linux-64 | Spatial transcriptomics with SpatialData, Squidpy, and Tangram |
| [Bulk-ATAC-Seq/hg38](Bulk-ATAC-Seq/hg38) | R + CLI | osx-arm64, linux-64 | Downstream bulk ATAC-seq analysis on hg38 |

A workspace only installs on the platforms listed in its `pixi.toml`. These environments can be easily modified and installed on your system / lab volume / personal cluster account. 

## Quick start

Ensure you have pixi installed and added to your path. Instructions for how to do this can be found [here](https://pixi.prefix.dev/latest/installation/)

To keep the environment with your analysis, copy the two files into your project folder, place them in a folder of your choosing, and install there:

```bash
cd CellRank2_env

pixi install --locked   # install exactly what is in pixi.lock
pixi shell              # activate the environment
```

`--locked` stops with an error if `pixi.toml` and `pixi.lock` disagree, instead of quietly re-solving. Use it so you get the tested versions.


`pixi shell` will start an interactive session within the environment (similar to `conda activate`). If you have to run a script within the environment, run a single command inside the environment without activating it by using `pixi run`.

```bash
pixi run python my_script.py
pixi run Rscript my_script.R
```


### Jupyter

The Python workspaces include `ipykernel`. Register the environment as a kernel once, then pick it in Jupyter or VS Code:

```bash
pixi run python -m ipykernel install --user --name cellrank2 --display-name "CellRank2 (pixi)"
```

### Post-install tasks

Some R packages are not on conda, so they are installed from GitHub with a Pixi task. Run these once after `pixi install`:

| Workspace | Task | Installs |
|-----------|------|----------|
| Seurat_v5 | `pixi run install-monocle3` | Monocle3 |
| Seurat_v5 | `pixi run install-scplotter` | scplotter |
| Bulk-ATAC-Seq/hg38 | `pixi run install-git` | ComplexHeatmap, RGenEDA |

GitHub installs are not recorded in `pixi.lock`. They install the current version of each package at the time you run the task.

## Workspace details

### Seurat_v5

R environment for scRNA-seq analysis.

- Core: Seurat (>= 5.4), SeuratObject, SeuratDisk
- QC and doublets: scater, scran, scDblFinder, SoupX
- Integration: Harmony
- Plotting: ggplot2, patchwork, ggalluvial, ggrepel, viridis, RColorBrewer
- Utilities: dplyr, reshape2, FNN, cluster, openxlsx
- From GitHub (post-install task): Monocle3, scplotter

### CellBender

Python environment for `cellbender remove-background`. It uses the GPU build of PyTorch and needs an NVIDIA driver that supports CUDA 12 where you run it.

- CellBender 0.4.0 (PyPI), PyTorch (GPU), Pyro, AnnData, PyTables, loompy
- `nbconvert` and `ipython` are included so CellBender can write its HTML report.

Login nodes usually have no GPU, so Pixi cannot detect CUDA there. To install on a login node, tell Pixi which CUDA version to assume:

```bash
CONDA_OVERRIDE_CUDA=12.0 pixi install --locked
```

Then run CellBender on a GPU node. For a CPU-only environment, remove the `[system-requirements]` block and replace `pytorch-gpu` with `pytorch`. CPU runs are much slower.

### CellRank2

Python environment for cell fate and trajectory analysis.

- CellRank (>= 2.0), moscot, MultiVelo, loompy, python-igraph, plotnine
- JAX and ott-jax are pinned to versions that work with moscot 0.3.x. Do not loosen these pins unless you also update moscot.

### MELD

Python environment for MELD sample-associated likelihoods.

- scanpy, numpy, pandas, scikit-learn, matplotlib
- meld, phate, and scprep come from PyPI because the conda-forge builds are not reliable.

### SpatialData

Python environment for spatial transcriptomics.

- SpatialData, spatialdata-io, spatialdata-plot, napari-spatialdata
- Squidpy, scanpy, Tangram, PyDESeq2, harmonypy, omnipath
- Spatial statistics: esda, libpysal
- RASP (Randomized Spatial PCA) from the maintained fork (`randomized-spatial-pca`). The R `mclust` backend is not installed. Use `RASP.clustering(method="gmm")` instead.

Almost every package is pinned from a working environment. On linux-64, PyTorch comes from the CPU wheel index to avoid about 3 GB of CUDA wheels. Only Tangram uses PyTorch, and it runs on CPU. The notes at the top of `pixi.toml` explain the pins that were changed.

### Bulk-ATAC-Seq/hg38

R environment for downstream bulk ATAC-seq analysis. It currently supports hg38 only.

- Differential accessibility and normalization: edgeR, qsmooth
- Annotation: ChIPseeker, TxDb.Hsapiens.UCSC.hg38.knownGene, org.Hs.eg.db, rtracklayer, GenomicRanges, BSgenome, Rsamtools
- Plotting: ggplot2, ggpubr, ggh4x, ggrepel, pheatmap, circlize, RColorBrewer
- Data handling: tidyverse, data.table
- deepTools for coverage tracks and heatmaps
- From GitHub (post-install task): ComplexHeatmap, RGenEDA

## Running on Discovery

- **Home directory quota.** Environments and the Pixi package cache can be several GB. Install workspaces on lab storage (for example under `/dartfs-hpc/rc/lab/...`), not in your home directory. Move the package cache out of your home directory too:

  ```bash
  export PIXI_CACHE_DIR=/dartfs-hpc/rc/lab/<your_lab>/.pixi_cache
  ```

  Add this line to your `~/.bashrc` so it applies to every session.

- **Platforms.** Discovery is `linux-64`. Seurat_v5 is currently built for `osx-arm64` only, so it will not install on Discovery.

## Troubleshooting

**`GenomeInfoDbData` fails to load (Bulk-ATAC-Seq).** The Bioconductor data package downloads its data in a post-link script. Pixi does not run post-link scripts by default. Either allow post-link scripts and reinstall, or run the script by hand:

```bash
PREFIX=$CONDA_PREFIX bash $CONDA_PREFIX/bin/.bioconductor-genomeinfodbdata-post-link.sh
```

Run this command inside `pixi shell`.

**`pixi install --locked` fails because the lock file is out of date.** Someone changed `pixi.toml` without updating `pixi.lock`. Please open an issue. Running `pixi install` without `--locked` re-solves the environment, but the versions may then differ from the tested ones.

## Adding or updating a workspace

1. Create a folder named after the tool or analysis.
2. Add `pixi.toml` with every platform you tested in `platforms`.
3. Run `pixi install` on each platform and commit the updated `pixi.lock`.
4. Put any GitHub-only packages in a `[tasks]` entry.
5. Add the workspace to the tables in this README.

## Related

- [GDSC Pixi SOP](https://github.com/Dartmouth-Data-Analytics-Core/GDSC-Pixi-SOP/wiki)
- [GDSC GitHub organization](https://github.com/Dartmouth-Data-Analytics-Core)

Questions or problems: open an issue in this repository.
