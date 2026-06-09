# README - Meta-analysis files

## Project

**Evaluation of DNA Quality and Case-Cohort Analysis of Endometrial Cancer-Associated SNPs**

This folder contains code used to perform the meta-analysis of selected endometrial cancer-associated SNPs across two independent genotyping rounds.

## Files included

### 1. `metanalisis.txt`

This file contains the cleaned R script for the meta-analysis.

The code performs the following steps:

1. Loads the saved R workspace containing PLINK association results.
2. Extracts additive genetic model results from Round 1 and Round 2.
3. Cleans SNP identifiers by removing technical prefixes such as `GSA-` and `exm-`.
4. Creates simplified tables containing SNP ID, effect allele, beta, standard error and p-value.
5. Merges Round 1 and Round 2 results by SNP ID.
6. Checks whether the effect allele is the same in both rounds.
7. Calculates inverse-variance weights.
8. Performs a fixed-effect inverse-variance meta-analysis.
9. Calculates:
   - combined beta
   - standard error
   - odds ratio
   - 95% confidence interval
   - Z statistic
   - meta-analysis p-value
   - Bonferroni-corrected p-value
10. Assesses whether the direction of effect is consistent between rounds.
11. Exports the final meta-analysis table to Excel.
12. Generates exploratory plots, including:
   - forest plot
   - scatter plot comparing Round 1 and Round 2
   - volcano plot
   - QQ plot

## Statistical approach

The analysis combines association results from two independent genotyping rounds using a **fixed-effect inverse-variance meta-analysis**.

This means that the beta estimates from Round 1 and Round 2 are combined as a weighted average. The weight assigned to each round depends on the inverse of its variance:

`weight = 1 / SE^2`

Therefore, estimates with smaller standard errors, and therefore greater precision, contribute more strongly to the final meta-analysis estimate.

## Important note about allele harmonization

Before combining beta estimates, it is essential to check whether the effect allele is the same in both rounds.

## Main output

The main output table is:

`meta_analysis_results_clean.xlsx`

This table includes:

- SNP ID
- effect allele in Round 1 and Round 2
- beta, standard error and p-value for each round
- combined meta-analysis beta
- meta-analysis odds ratio
- 95% confidence interval
- meta-analysis p-value
- Bonferroni-corrected p-value
- direction of effect in each round
- direction consistency between rounds

## Interpretation

A meta-analysis odds ratio greater than 1 suggests increased risk.

A meta-analysis odds ratio lower than 1 suggests a protective effect.

A Bonferroni-corrected p-value below 0.05 indicates that the association remains statistically significant after correction for multiple testing.

In this study, the strongest finding was observed for variants located in the HNF1B/17q12 locus, especially rs7501939, which remained statistically significant after Bonferroni correction.
