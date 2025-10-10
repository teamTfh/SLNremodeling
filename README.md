# Spatially-Resolved Immune Atlas of the Sentinel Lymph Node Microenvironment

This repository contains the analytical pipeline for the investigation of the tumor-immune microenvironment in sentinel lymph nodes (SLNs) using highly multiplexed immunofluorescence imaging data. The project leverages the Giotto framework for spatial transcriptomics and proteomics to perform in-depth characterization of cellular phenotypes, their spatial organization, and intercellular communication networks.

## Project Overview

The infiltration of immune cells into tumors and associated lymphoid structures is a critical determinant of patient outcomes and response to immunotherapy. This project aims to construct a comprehensive spatial atlas of the SLN ecosystem in the context of malignancy (likely melanoma, based on the marker panel). By mapping the identity and location of various immune and non-immune cell populations, we can elucidate the complex cellular dynamics and structural motifs that govern anti-tumor immunity.

This analysis identifies distinct cell populations through an iterative and hierarchical clustering approach, annotates them based on canonical protein marker expression, and sets the stage for downstream analysis of cellular neighborhoods and spatial interactions.

## Analytical Workflow

The analysis is performed through a series of R Markdown scripts, each representing a logical step in the pipeline. The core workflow is as follows:

1.  **Data Loading and Integration (`DataLoadandIntegration.Rmd`):**
    *   Raw single-cell data, exported from Indica Labs' HALO image analysis platform, is loaded.
    *   Data from multiple tissue samples (control and tumor) are parsed and formatted into individual `Giotto` objects.
    *   All sample objects are integrated into a single dataset, and batch effects between samples are corrected using the `Harmony` algorithm to ensure comparable measurements across the cohort.

2.  **Iterative Clustering and Refinement (`Clustering.1.Rmd`, `Clustering.2.Rmd`):**
    *   An initial round of unsupervised Leiden clustering is performed on the integrated data to partition cells into groups based on their marker expression profiles.
    *   A critical quality control step is performed to identify and remove clusters corresponding to artifacts or debris (e.g., pan-negative signal).
    *   The clustering process is iteratively refined on the filtered dataset to achieve robust and biologically meaningful cell populations. Tiny clusters (<100 cells) are removed.

3.  **Hierarchical Annotation and Sub-clustering (`Clustering.3.dim5k15.Rmd`, `Clustering.4.*.Rmd`):**
    *   Clusters are assigned a "Level 1" biological identity (e.g., B cell, T cell, Tumor cell) based on the expression of canonical markers, visualized via heatmaps and UMAP plots.
    *   This annotation enables a hierarchical analysis strategy, where broad cell lineages are subsetted for more granular investigation. For example, all cells annotated as T cells can be isolated and re-clustered to identify nuanced subtypes (e.g., CD4+ helper, CD8+ cytotoxic, regulatory T cells).

4.  **Downstream Spatial Analysis (`NeighborhoodAnalyses.Rmd`, `STdeconvolve.Rmd`):**
    *   With all cells robustly annotated, their spatial context is explored. The `NeighborhoodAnalyses.Rmd` script investigates the enrichment or depletion of specific cell-cell interactions and identifies recurring multicellular spatial motifs.
    *   The `STdeconvolve.Rmd` script applies spatial deconvolution algorithms to estimate the fractional abundance of cell types within defined spatial regions, providing a higher-level view of the tissue's cellular composition.

## Marker Panel

The cell populations were characterized using the following panel of protein markers. This panel was designed to identify major immune lineages, their activation and differentiation states, and tumor cells.

*   **T-Cell Markers:**
    *   `CD3E`: Pan-T cell marker
    *   `CD4`: Helper T cell marker
    *   `CD8`: Cytotoxic T cell marker
    *   `TCF1`: Marker for naive, memory, and stem-like T cells
    *   `TOX`: Exhaustion marker, often co-expressed with inhibitory receptors
    *   `CD45RO`: Memory T cell marker
*   **B-Cell Markers:**
    *   `CD19`, `CD20`: Pan-B cell markers
    *   `IGD`: Naive B cell marker
    *   `CD21`: Mature B cell and follicular dendritic cell marker
    *   `CXCR5`: Follicular helper T cell and B cell homing receptor
*   **Activation & Proliferation Markers:**
    *   `HLA-DR`: Antigen presentation marker, expressed on APCs and activated T cells
    *   `CD38`: Plasmablast/plasma cell marker, also an activation marker
    *   `KI67`: Proliferation marker
*   **General Immune & Myeloid Markers:**
    *   `CD45`: Pan-leukocyte marker
    *   `MAC2/GAL3`: Macrophage/monocyte marker (Galectin-3)
*   **Cytokine Marker:**
    *   `IFNG`: Interferon-gamma, a key pro-inflammatory cytokine
*   **Tumor Markers (Melanoma):**
    *   `MART1`, `GP100`, `S100B`: Melanoma-associated antigens
*   **Structural Marker:**
    *   `PAN.CYTO`: Pan-cytokeratin, likely used as a general cell segmentation marker or to identify epithelial/stromal cells.

## Repository Structure

*   `*.Rmd`: R Markdown files containing the code for each step of the analysis. They are numbered to indicate the intended order of execution.
*   `*.html`: HTML reports generated from the Rmd files, showing the code, outputs, and visualizations for each step.
*   `halo_export/`: Directory containing the raw data exported from the HALO software (not included in this repository).
*   `output/`: Directory where plots and other generated files are saved.
*   `*.RDS`: R Data Serialization files. These are binary files storing the `Giotto` object at various stages of the analysis (e.g., after integration, after each clustering step). They allow for an efficient re-loading of the project state without re-running computationally intensive steps.
*   `sln.metadata.csv`: A CSV file containing metadata for the samples.
*   `SLNremodeling.Rproj`: RStudio project file.
*   `LICENSE`: Project license.
*   `README.md`: This file.
