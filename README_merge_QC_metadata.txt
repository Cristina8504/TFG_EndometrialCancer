# README - Merge QC metadata and exploratory modelling

## Project

**Evaluation of DNA Quality and Case-Cohort Analysis of Endometrial Cancer-Associated SNPs**

This README explains the R script used to merge sample-level genotyping QC statistics with DNA extraction/metadata variables and to explore factors associated with QC outcome and genotyping call rate.

## Files

### 1. `merge_QC_metadata_codigo_comentado.txt`

This file contains the complete cleaned and commented R code.

### 2. `README_merge_QC_metadata.txt`

This file explains the purpose of the analysis, the input files, the main steps and the output files.

## Purpose of the analysis

The purpose of this analysis is to combine genotyping QC data with extraction and sample metadata in order to evaluate whether technical or pre-analytical variables are associated with genotyping performance.

The analysis focuses on two main outcomes:

1. **QC outcome**  
   Whether a sample passed or failed genotyping quality control.

2. **Call rate**  
   A continuous measure of genotyping performance, representing the proportion of successfully called variants.

## Input files

### QC statistics file

`WK-3864_241205_QCstats.tsv`

This file contains sample-level genotyping QC statistics.

Expected variables include:

- `LABEL_ID`
- `QC_pass`
- `totdna_ng`
- `call_rate_raw`

### Metadata/extraction file

`WK-3864_240821_Conc_TotalDNA_KI.xlsx`

This file contains DNA extraction or metadata variables.

Expected variables include:

- `Sample`
- DNA concentration variables
- DNA volume variables
- total DNA variables

## Main steps in the script

### 1. Load packages

The script loads:

- `dplyr` for data manipulation
- `ggplot2` for plots
- `readxl` for Excel files
- `scales` for percentage axes
- `corrplot` for heatmaps
- `pROC` for ROC/AUC analysis
- `writexl` for Excel export

### 2. Load the QC dataset

The QC file is read as a tab-separated file.

The script checks:

- number of rows
- number of columns
- column names
- first rows of the dataset

### 3. Load metadata/extraction dataset

The metadata file is read from Excel.

The script checks:

- number of rows
- number of columns
- column names
- first rows of the dataset

### 4. Clean sample IDs

The script creates a cleaned ID column in both datasets.

This is important because both datasets need a common identifier for merging.

In the cleaned script:

- QC ID column: `LABEL_ID`
- metadata ID column: `Sample`

These can be changed if the real column names differ.

### 5. Check overlap between datasets

Before merging, the script checks how many sample IDs are present in both datasets.

This helps determine whether the QC and metadata files correspond to the same samples.

### 6. Merge datasets

The script performs a left join.

This keeps all QC samples and adds metadata columns when a matching sample ID exists.

### 7. Create year variable

The script extracts the suffix from the sample ID.

For example, if an ID ends in `-18`, this is interpreted as 2018.

Values lower than or equal to 30 are interpreted as 2000s. Values above 30 are interpreted as 1900s.

### 8. Clean and rename variables

Variables are renamed to clearer names:

- `totdna_ng` becomes `dna_total_ng`
- `call_rate_raw` becomes `call_rate`
- `QC_pass` becomes `qc_pass`

DNA concentration, volume and total DNA variables are also renamed using cleaner labels.

### 9. Create collector type

The script creates a new variable called `collector_type`.

This is inferred from the sample ID prefix:

- IDs starting with `C` or `GYN` are classified as `doctor`
- IDs starting with `Y` are classified as `midwife`
- other prefixes are classified as `unknown`

A temporary prefix variable is created to audit the classification and then removed.

### 10. Save merged dataset

The cleaned dataset is saved as:

- `merged_qc_metadata_clean.tsv`
- `merged_qc_metadata_clean.csv`
- `merged_qc_metadata_clean.xlsx`

### 11. Create audit table

The audit table summarizes every variable in the cleaned dataset.

For each variable, it reports:

- R class
- number of missing values
- percentage of missing values
- number of unique values

This is useful to understand the quality and structure of the merged dataset.

### 12. Explore variable distributions

The script produces histograms, density plots, boxplots and Q-Q plots for:

- `dna_total_ng`
- `call_rate`

These plots were used to assess normality, skewness and outliers.

### 13. Univariable tests

The script performs Wilcoxon rank-sum tests to compare continuous variables by QC outcome.

Tests include:

- `dna_total_ng ~ qc_pass`
- `concentration_current_ng_ul ~ qc_pass`

Wilcoxon tests were used because these variables were not normally distributed.

### 14. Collector type and QC outcome

The script creates a contingency table between:

- `collector_type`
- `qc_pass`

It then performs a chi-square test to evaluate whether QC outcome is independent of collector type.

A proportional bar plot is generated to visualize QC pass/fail proportions by collector type.

### 15. Year and QC outcome

The script evaluates the relationship between year and QC outcome.

A chi-square test with Monte Carlo simulation is used because some year categories may contain few QC failures, leading to low expected cell counts.

### 16. Correlation between DNA amount and call rate

The script evaluates the relationship between:

- `dna_total_ng`
- `call_rate`

It first assesses normality using histograms, Q-Q plots and Shapiro-Wilk tests.

If both variables are normally distributed, Pearson correlation is used.

Otherwise, Spearman correlation is used.

In this context, Spearman correlation is generally more appropriate because DNA amount and call rate are usually non-normally distributed.

### 17. Correlation heatmap

The script calculates a Spearman correlation matrix for continuous variables:

- `dna_total_ng`
- `concentration_current_ng_ul`
- `volume_current_ul`
- `call_rate`
- `year`

A heatmap is generated using `corrplot`.

### 18. Logistic regression model

The script fits a logistic regression model:

`qc_pass ~ dna_total_ng + collector_type + year`

This evaluates whether DNA amount, collector type and year are associated with the probability of passing QC.

### 19. ROC curve and AUC

The script uses the logistic regression model to calculate predicted probabilities of QC pass.

A ROC curve and AUC are then calculated using the `pROC` package.

This assesses the predictive ability of the logistic regression model.

### 20. Linear regression model for call rate

Because call rate is a continuous outcome, the script also fits a linear regression model:

`call_rate ~ dna_total_ng + collector_type + year`

This evaluates whether DNA amount, collector type and year explain variability in genotyping call rate.

Model diagnostic plots are generated to assess model assumptions.

### 21. Call rate by collector type

A boxplot is generated to compare call rate distribution between collector types.

## Output files generated by the script

The script generates the following files:

- `merged_qc_metadata_clean.tsv`
- `merged_qc_metadata_clean.csv`
- `merged_qc_metadata_clean.xlsx`
- `audit_table_merged_qc_metadata.csv`
- `merged_qc_metadata_clean.rds`
- `audit_table_merged_qc_metadata.rds`
- `qc_logistic_model.rds`
- `callrate_linear_model.rds`


This analysis investigates whether pre-analytical and technical variables influence genotyping quality. It supports the evaluation of DNA quantity, collector type and year as potential contributors to QC outcome and call rate variability. The combination of univariable analyses, correlation analysis and regression models provides a structured framework for understanding genotyping performance in biobanked cervical screening-derived samples.
