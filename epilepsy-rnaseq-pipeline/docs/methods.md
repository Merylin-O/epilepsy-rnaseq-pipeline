# Methods and Pipeline Documentation

## Project summary

This project analyzes bulk RNA-seq data from dataset GSE134697, comparing hippocampus and neocortex tissue from patients with mesial temporal lobe epilepsy (MTLE). The goal is to identify genes that behave differently between the two tissue types and to understand what biological processes those genes are involved in.

## 1. Data acquisition

- Data was pulled directly from NCBI GEO using the `GEOparse` library, accession GSE134697.
- Sample metadata (tissue source, patient ID) was extracted from the GEO records.
- The raw count matrix was not attached to the GEO SOFT file, so it was downloaded separately from the NCBI FTP server.

## 2. Data cleaning

- Column names in the raw count matrix were long file paths (e.g. `/home/.../CHKJ-C10-Brain-RNA_D00140_0313.bam`). These were cleaned down to sample IDs (e.g. `CHKJ-C10`).
- Gene annotation columns were separated from the actual count columns.
- A metadata table was built manually, recording each sample's condition (Hippocampus, Neocortex, or Control) and patient ID.
- Only paired hippocampus/neocortex samples were kept for the main comparison. Control-only samples were excluded from this analysis.
- An assertion check confirmed that the sample order in the count matrix matched the sample order in the metadata table before proceeding.

## 3. Quality control

- Library sizes (total reads per sample) were calculated and visualized as a bar chart.
- Samples with fewer than 10 million reads were flagged as potentially low quality.
- One clear outlier pair (patient 5, hippocampus and neocortex) was removed based on unusually low library size.
- Genes with zero counts across all samples were identified and excluded from downstream analysis.
- A minimum expression filter was applied: a gene was kept only if it had at least 10 counts in at least half of the samples in the smallest group.

## 4. Normalization

- PyDESeq2's `DeseqDataSet` was used to build a model with `patient` and `condition` as design factors.
- Size factors were estimated to correct for differences in sequencing depth between samples.
- Normalized counts were extracted and log2-transformed (with a pseudocount of 1) for visualization purposes.

## 5. PCA and outlier detection

- Principal component analysis (10 components) was run on the log2-normalized expression matrix.
- Samples were plotted on PC1 vs PC2 and PC2 vs PC3 to check for batch effects and outliers.
- Outliers were defined objectively as samples falling more than 2 standard deviations from the mean on either PC1 or PC2.
- Two further patient pairs (patient 7 and patient 13) were identified as outliers this way and removed, along with their matched pairs, to keep the dataset paired.
- PCA was re-run on the cleaned dataset (28 samples remaining) to confirm the outliers were resolved.
- The DESeq2 dataset object was rebuilt from scratch on this final, cleaned set of samples and re-fitted.

## 6. Differential expression analysis

- `DeseqStats` was used to test Hippocampus vs Neocortex, using patient as a paired/blocking factor.
- Genes were ranked by adjusted p-value (padj).
- Significance thresholds: padj < 0.05 and absolute log2 fold change > 1 (at least a 2-fold difference).
- This yielded 1,739 differentially expressed genes.

## 7. Gene ID mapping

- Ensembl gene IDs were converted to gene symbols, full names, and Entrez IDs using the `mygene` Python client, which queries the MyGene.info database.

## 8. Visualization

- **Volcano plot**: all genes plotted by log2 fold change vs -log10(padj), colored by upregulated, downregulated, or not significant.
- **Clustered heatmap**: the top 50 most significant genes, with a color bar showing which samples are Hippocampus vs Neocortex.

## 9. Pathway enrichment analysis

- The list of significant gene symbols was tested for enrichment using `gseapy`, which connects to the Enrichr web service.
- Three databases were queried: GO Biological Process (2023), KEGG (2021, Human), and WikiPathway (2023, Human).
- Top enriched terms from each database were plotted as horizontal bar charts.

## Key findings

- 1,739 genes were significantly differentially expressed between hippocampus and neocortex in MTLE patients.
- Genes of interest include GABRE, DRD1, CAMKK2, NTS, and CEBPD.
- Enrichment results point to specific biological processes and pathways distinguishing the two tissue types (see the pathway enrichment plot for details).

## Tools and versions used

pandas, numpy, matplotlib, seaborn, scikit-learn, GEOparse, PyDESeq2, mygene, gseapy

## Next steps

This pipeline is being extended toward computational neural modelling work, connecting the differentially expressed genes identified here to conductance-based neural models, protein-protein interaction networks (via STRING), and cross-referencing with Allen Brain Atlas and GTEx expression data.
