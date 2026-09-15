# BF528 Final Project: Single-Cell RNA-seq Reanalysis of Hepatoblastoma

## Project Overview

This project reanalyzes a published **single-cell RNA sequencing (scRNA-seq)** dataset from human hepatoblastoma using **Python and Scanpy**. The goal was to examine cell-state heterogeneity across **background liver, primary tumor, and patient-derived xenograft (PDX)** samples and to practice a reproducible single-cell analysis workflow.

The analysis starts from a **published, processed `.h5ad` object** (`GSE180665_hb_integrated_normalized_annotated_harmony.h5ad`), rather than raw FASTQ files. The input object had already undergone normalization, Harmony-based integration/batch correction, and preliminary annotation before this reanalysis. The processed object used in the notebook contains **67,110 cells and 33,538 genes**.

## Data Source

**Publication**
Bondoc A, Glaser K, Jin K, et al. *Identification of distinct tumor cell populations and key genetic mechanisms through single cell sequencing in hepatoblastoma.* **Communications Biology**. 2021;4:1049.
DOI: https://doi.org/10.1038/s42003-021-02562-8

**GEO accession:** GSE180665
https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE180665

The GEO series contains seven human samples spanning background liver, hepatoblastoma tumor, and PDX conditions and provides the processed Harmony-integrated `.h5ad` file used here.

## Analyses Performed

### 1. Quality-control assessment and filtering
- Computed `n_genes_by_counts`, `total_counts`, and `pct_counts_mt` (mitochondrial genes identified via the `MT-` prefix) with `sc.pp.calculate_qc_metrics`.
- Inspected QC distributions with violin plots.
- Filtered cells with `min_genes=200` and genes with `min_cells=3`.
- Removed cells with `n_genes_by_counts >= 7000` (putative doublets/outliers) and `pct_counts_mt >= 5%`.

### 2. Normalization, feature selection, and dimensionality reduction
- Normalized total counts per cell to 10,000 (`sc.pp.normalize_total`) and applied `log1p`.
- Selected the top **2,000 highly variable genes** (Seurat flavor, `sc.pp.highly_variable_genes`).
- Performed principal component analysis (PCA), constructed a neighborhood graph, and generated a UMAP embedding.

### 3. Leiden clustering
- Applied Leiden clustering at **resolution = 0.5**.
- Visualized transcriptional clusters and sample distributions on UMAP.
- Examined sample-level imbalance in cell counts as a potential source of downstream bias.

### 4. Marker-gene analysis
- Used Scanpy `rank_genes_groups` with the **Wilcoxon** method to identify cluster-associated marker genes.
- Compared cluster-specific expression patterns against a literature-curated marker dictionary spanning hepatocyte, endothelial, stellate, Kupffer/immune, and multiple tumor-cell states (stem-like, proliferative, metabolic, vascular-like, mesenchymal, metastatic — e.g. `ALB`, `FLT1`, `COL3A1`, `CD68`, `GPC3`, `EZH2`, `CD44`, `CXCR4`).

### 5. Cell-type annotation and biological interpretation
- Used **CellTypist** as an exploratory automated annotation tool.
- Treated automated labels as preliminary because the selected reference model did not provide biologically complete coverage of this hepatoblastoma dataset.
- Cross-checked automated labels against the marker dictionary above and literature evidence before interpreting clusters.

### 6. Exploratory trajectory analysis
- Applied **PAGA** and **diffusion pseudotime (DPT)** to explore possible relationships among transcriptional states.
- A root cluster was selected manually (`root_cluster`, set from the Leiden clustering above) for this exploratory analysis, so the resulting pseudotime should be interpreted as a hypothesis-generating trajectory rather than direct evidence of lineage progression.

### 7. Exploratory cell-composition analysis
- Summarized predicted cell-type proportions across samples and sample groups.
- Because this analysis depends on automated cell-type labels, composition results were treated cautiously and were not used as definitive biological conclusions.

### 8. Doublet scoring
- Ran doublet detection with **Scrublet** on the raw count matrix and stored doublet scores/predictions (`doublet_score`, `predicted_doublet`) for quality assessment.
- Doublet calls were not treated as a fully validated removal step because the analysis began from a previously processed expression object.

## Relationship to the Original Study

The original study characterized cellular heterogeneity in hepatoblastoma, background liver, and PDX samples and used canonical markers to define major liver, tumor, stromal, endothelial, and immune populations. It also investigated tumor-cell progression using pseudotime-related analyses and RNA velocity.

This course project **reanalyzes the published processed dataset with Scanpy** and explores related questions through clustering, marker analysis, literature-guided annotation, PAGA/DPT trajectory analysis, and sample-level composition summaries. It does **not** claim to reproduce the original study's Harmony integration or RNA-velocity analysis.

## Key Takeaways

A major lesson from this project was that computational output must be checked against biological context. Automated annotation can produce incomplete or implausible labels when the reference model does not match the tissue or disease system. For that reason, cell identities were interpreted using cluster-specific marker genes and literature evidence rather than relying on automated labels alone.

The project strengthened experience with:
- Python-based single-cell analysis
- Scanpy and AnnData objects
- QC assessment and feature selection
- PCA, UMAP, and Leiden clustering
- Marker-gene analysis
- Cell-type annotation and validation
- PAGA and diffusion pseudotime
- Reproducible computational workflows and critical interpretation of automated tools

## Repository Structure

```text
BF528-Single-Cell-RNAseq/
├── README.md
├── BF528_final_project.ipynb
└── environment.yml
```

- `BF528_final_project.ipynb`: main analysis notebook with code, figures, and interpretation.
- `environment.yml`: Conda environment containing the main Python dependencies used in the project.

## Environment

The repository environment includes:
- Python 3.10
- NumPy
- Pandas
- SciPy
- Matplotlib
- Seaborn
- Scanpy
- Scrublet
- CellTypist

To recreate the environment:

```bash
conda env create -f environment.yml
conda activate bf528_project_env
```

## Notes and Limitations

- The analysis begins from the processed GEO `.h5ad` object rather than raw sequencing reads.
- Harmony integration was already present in the published input object; this repository does not claim to have performed the original Harmony integration from raw samples.
- QC thresholds (`n_genes_by_counts < 7000`, `pct_counts_mt < 5%`) were chosen exploratively from the observed distributions rather than from a fixed rule, and may need re-tuning on other datasets.
- Automated CellTypist annotations were used only as preliminary guidance and require biological validation.
- Pseudotime results are exploratory because the root state was manually specified and no experimental lineage tracing was available.
- RNA velocity is not included in this repository.
