# BF528 Final Project: Single-Cell RNA-seq Reanalysis of Hepatoblastoma

## Project Overview

This project reanalyzes a published **single-cell RNA sequencing (scRNA-seq)** dataset from human hepatoblastoma using **Python and Scanpy**. The goal was to examine cell-state heterogeneity across **background liver, primary tumor, and patient-derived xenograft (PDX)** samples and to practice a reproducible single-cell analysis workflow.

The analysis starts from a **published processed `.h5ad` object**, rather than raw FASTQ files. The input object had already undergone normalization, Harmony-based integration/batch correction, and preliminary annotation before this reanalysis. The processed object used in the notebook contains **67,110 cells and 33,538 genes**.

## Data Source

**Publication**  
Bondoc A, Glaser K, Jin K, et al. *Identification of distinct tumor cell populations and key genetic mechanisms through single cell sequencing in hepatoblastoma.* **Communications Biology**. 2021;4:1049.  
DOI: https://doi.org/10.1038/s42003-021-02562-8

**GEO accession:** GSE180665  
https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE180665

The GEO series contains seven human samples spanning background liver, hepatoblastoma tumor, and PDX conditions and provides the processed Harmony-integrated `.h5ad` file used here.

## Analyses Performed

### 1. Quality-control assessment and filtering
- Calculated the number of detected genes per cell, total counts, and mitochondrial transcript percentage.
- Inspected QC distributions with violin plots.
- Applied exploratory filters based on gene count and mitochondrial percentage.
- Removed genes detected in fewer than three cells.

### 2. Feature selection and dimensionality reduction
- Selected **2,000 highly variable genes** for downstream analysis.
- Performed principal component analysis (PCA).
- Constructed a neighborhood graph and generated a UMAP embedding.

### 3. Leiden clustering
- Applied Leiden clustering at a resolution of 0.5.
- Visualized transcriptional clusters and sample distributions on UMAP.
- Examined sample-level imbalance in cell counts as a potential source of downstream bias.

### 4. Marker-gene analysis
- Used Scanpy `rank_genes_groups` with the Wilcoxon method to identify cluster-associated marker genes.
- Compared cluster-specific expression patterns with known liver, stromal, immune, and hepatoblastoma-associated markers.

### 5. Cell-type annotation and biological interpretation
- Used **CellTypist** as an exploratory automated annotation tool.
- Treated automated labels as preliminary because the selected reference model did not provide biologically complete coverage of this hepatoblastoma dataset.
- Used literature-guided marker inspection to interpret selected clusters, including markers associated with hepatoblast-like cells, stromal/fibroblast-like cells, and macrophages.

### 6. Exploratory trajectory analysis
- Applied **PAGA** and **diffusion pseudotime (DPT)** to explore possible relationships among transcriptional states.
- A root cluster was selected manually for this exploratory analysis, so the resulting pseudotime should be interpreted as a hypothesis-generating trajectory rather than direct evidence of lineage progression.

### 7. Exploratory cell-composition analysis
- Summarized predicted cell-type proportions across samples and sample groups.
- Because this analysis depends on automated cell-type labels, composition results were treated cautiously and were not used as definitive biological conclusions.

### 8. Doublet scoring
- Explored doublet detection with **Scrublet** and stored doublet scores/predictions for quality assessment.
- Doublet calls were not treated as a fully validated removal step because the analysis began from a previously processed expression object.

## Relationship to the Original Study

The original study characterized cellular heterogeneity in hepatoblastoma, background liver, and PDX samples and used canonical markers to define major liver, tumor, stromal, endothelial, and immune populations. It also investigated tumor-cell progression using pseudotime-related analyses and RNA velocity.

This course project **reanalyzes the published processed dataset with Scanpy** and explores related questions through clustering, marker analysis, literature-guided annotation, PAGA/DPT trajectory analysis, and sample-level composition summaries. It does **not** claim to reproduce the original study's RNA-velocity analysis.

## Key Takeaways

A major lesson from this project was that computational output must be checked against biological context. Automated annotation can produce incomplete or implausible labels when the reference model does not match the tissue or disease system. For that reason, cell identities were interpreted using cluster-specific marker genes and literature evidence rather than relying on automated labels alone.

The project strengthened my experience with:
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
├── BF528_single_cell_analysis.ipynb
└── environment.yml
```

- `BF528_single_cell_analysis.ipynb`: main analysis notebook with code, figures, and interpretation.
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
- Automated CellTypist annotations were used only as preliminary guidance and require biological validation.
- Pseudotime results are exploratory because the root state was manually specified and no experimental lineage tracing was available.
- RNA velocity is not included in this repository.

