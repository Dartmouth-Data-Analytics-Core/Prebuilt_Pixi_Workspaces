# GDSC Prebuilt Pixi Workspaces

![In Progress](https://img.shields.io/badge/status-in%20progress-yellow)

This repository contains **prebuilt Pixi workspaces** for common genomic and single-cell analysis workflows. Each workspace includes a `pixi.toml` and `pixi.lock` file to enable reproducible installation of R, Python, and Bioconductor packages across platforms. These environments are intended to make it easier to launch analyses without manually resolving package versions or dependencies.

---

## Workspaces

### [Seurat_v5](Seurat_v5)

**Purpose:** Skeleton environment for **single-cell RNA-Sequencing analysis** using **Seurat v5**.  

**Description:**
- Includes all major R packages for single-cell workflows:
  - `Seurat` (v5.4+)
  - `SeuratObject`
  - Bioconductor packages: `scater`, `scran`, `SingleCellExperiment`, `scDblFinder`
  - Visualization: `ggplot2`, `patchwork`, `ggalluvial`, `viridis`, `RColorBrewer`, `ggrepel`
  - Data manipulation: `dplyr`, `reshape2`, `Matrix`
  - **Monocle3**: Trajectory inference for single-cell RNA-seq  
  - **scplotter**: Visualization utilities for single-cell data  
