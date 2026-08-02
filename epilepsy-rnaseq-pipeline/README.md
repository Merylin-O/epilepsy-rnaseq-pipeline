# Bulk RNA-seq Analysis of Mesial Temporal Lobe Epilepsy (GSE134697)

A Python-based bulk RNA-seq pipeline, built from scratch, identifying differentially expressed genes and enriched pathways in mesial temporal lobe epilepsy (MTLE), using paired hippocampus/neocortex samples from dataset GSE134697.

## Overview

This pipeline takes raw count data from a public GEO dataset through to biological interpretation:

- Data retrieval and cleaning (GEOparse, manual metadata construction)
- Quality control (library size checks, outlier detection)
- Normalization and variance-stabilizing transformation
- PCA-based outlier removal
- Differential expression analysis with **PyDESeq2**
- Gene ID mapping (Ensembl → gene symbol via MyGene.info)
- Visualization (volcano plot, clustered heatmap)
- Pathway enrichment analysis across **GO Biological Process**, **KEGG**, and **WikiPathways** (via GSEApy/Enrichr)

## Key results

- 1,739 differentially expressed genes identified between hippocampus and neocortex samples
- Notable genes include *GABRE*, *DRD1*, *CAMKK2*, *NTS*, and *CEBPD*
- Pathway enrichment run across three databases to characterize the biological processes affected

## Figures

All figures are in `images/`, extracted directly from the notebook outputs:

- `01_library_size_barplot.png`: sequencing depth per sample
- `02_pca_pc2_vs_pc3.png` and `03_scree_plot.png`: initial PCA checks
- `04_pca_pc1_vs_pc2.png`: initial PCA before outlier removal
- `05_pca_after_outlier_removal.png`: PCA after removing outlier samples
- `06_volcano_plot.png`: all genes by fold change and significance
- `07_deg_heatmap.png`: top 50 significant genes, clustered
- `08_pathway_enrichment_barplots.png`: top enriched terms across GO, KEGG, and WikiPathways

See `docs/methods.md` for a full step-by-step writeup of the pipeline.

## Repository structure

```
.
├── notebooks/    # Jupyter notebook(s) containing the full analysis
├── docs/         # Notes, writeups, abstracts, or supporting documents
├── images/       # Plots and figures generated from the analysis (PCA, volcano plot, heatmap, etc.)
├── results/      # Output tables (e.g. DESeq2 results, significant gene lists) - not committed if large
└── README.md
```

## Data source

Raw data: [GSE134697](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE134697) on NCBI GEO.

Data files are not stored in this repository (see `.gitignore`); the notebook downloads them directly from GEO.

## Tools and libraries

Python, pandas, numpy, matplotlib, seaborn, GEOparse, PyDESeq2, scikit-learn (PCA), mygene, gseapy

## Author

Merylin Wuraola Ogunlola
