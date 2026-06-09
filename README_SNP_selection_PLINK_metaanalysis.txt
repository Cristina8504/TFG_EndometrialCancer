# README - SNP selection, PLINK association and meta-analysis

## Project

Evaluation of DNA Quality and Case-Cohort Analysis of Endometrial Cancer-Associated SNPs

## Files

### 1. SNP_selection_PLINK_metaanalysis_codigo_comentado.txt

This file contains the cleaned and commented R code.

### 2. README_SNP_selection_PLINK_metaanalysis.txt

This file explains the workflow, inputs, outputs and interpretation.

## Purpose of the analysis

This analysis evaluates candidate SNPs previously associated with endometrial cancer or related traits.

The workflow includes:

1. Loading GWAS Catalog association export files.
2. Cleaning SNP identifiers.
3. Creating a final candidate SNP list.
4. Reading PLINK/PLINK2 association results from Round 1 and Round 2.
5. Keeping only additive genetic model results.
6. Calculating odds ratios.
7. Combining estimates across rounds using fixed-effect inverse-variance meta-analysis.
8. Applying Bonferroni correction.
9. Evaluating effect direction consistency between rounds.
10. Creating exploratory association plots.

## Input files

### GWAS Catalog export files

The script reads five GWAS Catalog TSV files:

- EFO_0004230_associations_export.tsv
- EFO_1001514_associations_export.tsv
- MONDO_0002447_associations_export.tsv
- MONDO_0005213_associations_export.tsv
- MONDO_0006003_associations_export.tsv

These files contain association information for selected traits or ontology terms.

### PLINK/PLINK2 association files

The script reads two PLINK2 logistic regression output files:

- snps_new_analysis_plink2_correct.PHENO1.glm.logistic.hybrid
- snps_round2_glm.PHENO1.glm.logistic.hybrid

These correspond to Round 1 and Round 2 genotyping association analyses.

## Main steps

### 1. GWAS Catalog SNP cleaning

The GWAS Catalog riskAllele field often contains SNP and allele information together, for example:

rs7501939-A

The script removes everything after the hyphen, keeping only:

rs7501939

### 2. Candidate SNP list

A manual list of candidate SNPs is created from GWAS literature and GWAS Catalog results.

The list includes SNPs associated with:

- endometrial cancer
- broader cancer traits
- pleiotropic traits potentially related to endometrial cancer

The list is exported as:

snps_list.txt

### 3. PLINK2 Round 1 and Round 2 processing

The script reads Round 1 and Round 2 GLM logistic outputs.

For each round, it:

- renames the chromosome column
- keeps TEST == "ADD"
- removes technical prefixes such as GSA- and exm-
- calculates odds ratios as exp(BETA)
- creates compact tables with ID, A1, A1 frequency, OR and p-value

### 4. Meta-analysis preparation

For each round, the script keeps:

- SNP ID
- effect allele
- beta
- standard error
- p-value

The two rounds are merged by SNP ID.

### 5. Allele consistency check

The script checks whether the effect allele is the same in both rounds using:

Same_A1

If Same_A1 is FALSE, allele harmonization is required before combining beta estimates.

### 6. Fixed-effect inverse-variance meta-analysis

Weights are calculated as:

weight = 1 / SE^2

The combined beta is calculated as a weighted average of Round 1 and Round 2 beta estimates.

The script calculates:

- meta-analysis beta
- standard error
- odds ratio
- 95% confidence interval
- Z statistic
- meta-analysis p-value
- Bonferroni-corrected p-value

### 7. Direction consistency

The script classifies each SNP as:

- Risk if beta > 0
- Protective if beta < 0

It then checks whether the direction is consistent between Round 1 and Round 2.

## Output files

The script generates:

- snps_list.txt
- tabla_glm_round2.xlsx
- meta_analysis_results_clean_from_plink2.xlsx
- candidate_snps_list.rds
- round1_glm_additive_results.rds
- round2_glm_additive_results.rds
- meta_analysis_dataframe.rds
- meta_analysis_table.rds

## Plots generated

The script generates:

- forest plot
- scatter plot comparing Round 1 and Round 2 effects
- volcano plot
- simplified candidate SNP association plot
- QQ plot
- histogram of meta-analysis odds ratios
- direction consistency plot
- effect size vs standard error plot
- funnel-style plot
- OR vs p-value plot

