# README - Extended QC analysis with clinical variables

## Project

**Evaluation of DNA Quality and Case-Cohort Analysis of Endometrial Cancer-Associated SNPs**

This README explains the R script used to analyse additional clinical and technical variables in relation to genotyping QC outcome and call rate.

## Files

### 1. `extended_QC_clinical_variables_codigo_comentado.txt`

This file contains the complete cleaned and commented R code.

### 2. `README_extended_QC_clinical_variables.txt`

This file explains the purpose of the analysis, the input files, the main analytical steps and the generated outputs.

## Purpose of the analysis

The purpose of this analysis is to extend the previous QC analysis by including additional variables from NKCx/clinical records, such as:

- age
- cytology information
- HPV diagnosis
- cohort/case status
- collector type
- year/sample year
- DNA amount
- call rate

The analysis evaluates whether these variables are associated with:

1. **QC pass/fail outcome**
2. **Genotyping call rate**

## Input files

### 1. `WK-3864_241205_QCstats_NKCx_JW260310.xlsx`

This file contains QC statistics and additional clinical variables.

Expected variables include:

- `qc_pass`
- `dna_total_ng`
- `call_rate`
- `AGE`
- `SNOMED_WORST`
- `SNOMED_SEVERITY`
- `HPVDIAG`
- `translation`
- `collector_type`
- `year`

### 2. `WK-3864_241205_QCstats_NKCx_JW260313.xlsx`

This file contains the final dataset including the cohort/case variable.

It is used to create:

- `cohort_binary`

where:

- `1 = case`
- `0 = cohort/control`

## Main steps in the script

### 1. Load and inspect the dataset

The script loads the Excel file and inspects:

- column names
- variable types
- summary statistics
- missing values

The initial inspection showed many missing values in cytology and HPV-related variables.

### 2. Convert variables

Variables are converted to suitable formats:

- `qc_pass` to logical
- `dna_total_ng`, `call_rate`, `AGE`, `SNOMED_SEVERITY` to numeric
- `SNOMED_WORST`, `HPVDIAG`, `translation` to character
- `collector_type` and `year` to factors

### 3. Create simplified cytology groups

The script creates `cytology_group` from `SNOMED_SEVERITY`.

Groups are defined as:

- `poor_quality`
- `normal`
- `abnormal`
- `other`

This simplifies cytology information for exploratory analysis.

### 4. Create HPV groups

The script creates `hpv_group` from `HPVDIAG`.

Groups are defined as:

- `negative`
- `positive`
- `other`

Later, a more detailed HPV variable called `hpv_final` is created from the `translation` variable.

The final HPV groups include:

- `Negative`
- `Other_high_risk`
- `HPV16`
- `HPV18`
- `Missing`
- `Other`

### 5. Age analysis

The script evaluates age in relation to QC and genotyping performance.

It performs:

- descriptive statistics of age by QC outcome
- Wilcoxon test comparing age between QC pass/fail groups
- Spearman correlation between age and call rate
- Spearman correlation between age and DNA amount
- plots of age distribution, age by QC outcome and age vs call rate

### 6. Cytology analysis

The script evaluates whether cytology group is related to:

- QC outcome
- call rate
- total DNA amount

It performs:

- contingency table of cytology group vs QC outcome
- Fisher's exact test
- descriptive statistics by cytology group
- Kruskal-Wallis test for call rate by cytology group
- Kruskal-Wallis test for DNA amount by cytology group
- boxplots of call rate and DNA amount by cytology group

Fisher's exact test is used because some cytology groups may contain small counts.

### 7. HPV analysis

The script evaluates whether HPV group is related to:

- QC outcome
- call rate
- total DNA amount

It performs:

- contingency table of HPV group vs QC outcome
- Fisher's exact test
- descriptive statistics by HPV group
- Wilcoxon tests for call rate and DNA amount by HPV group
- boxplots with statistical comparisons

### 8. Extended logistic model including clinical variables

A logistic regression model is fitted with `qc_pass` as outcome.

Predictors include:

- `dna_total_ng`
- `collector_type`
- `year`
- `AGE`
- `hpv_group`
- `cytology_group`

This evaluates whether clinical and technical variables are associated with passing genotyping QC.

### 9. Cohort/case variable

The script loads a second Excel file and creates a binary variable:

- `cohort_binary = 1` for cases
- `cohort_binary = 0` for cohort/control samples

The script then evaluates whether QC outcome or call rate differs between cases and cohort samples.

### 10. Logistic regression including cohort/case status

The script fits a model with `qc_pass` as outcome and predictors including:

- `cohort_binary`
- `HPVDIAG`
- `SNOMED_WORST`
- `dna_total_ng`
- `year`
- `collector_type`
- `AGE`

It then calculates odds ratios and confidence intervals.

### 11. QC failure outcome

The script creates a new variable:

`qc_fail`

where:

- `1 = QC fail`
- `0 = QC pass`

This outcome is easier to interpret when modelling predictors of genotyping failure.

### 12. Log transformation of DNA

The variable `dna_total_ng` is log-transformed:

`log_dna = log(dna_total_ng + 1)`

This is done because DNA amount is right-skewed.

Adding 1 avoids problems if any DNA value is zero.

### 13. Missing data handling

The script:

- converts missing cytology group values into a `"Missing"` category
- replaces a missing `sample_year` value with 2016
- excludes samples with missing AGE or volume for complete-case modelling

This creates `df_noNA`, the final dataset used for some models.

### 14. Logistic regression models for QC failure

Two logistic regression models are fitted:

#### Short model

`qc_fail ~ log_dna + collector_type + cytology_group + cohort_binary`

#### Extended model

`qc_fail ~ log_dna + collector_type + cytology_group + hpv_final + AGE + sample_year`

These models evaluate whether technical and clinical variables are associated with QC failure.

### 15. Odds ratio tables

For the logistic models, the script extracts:

- coefficient estimates
- standard errors
- p-values
- odds ratios
- 95% confidence intervals

The OR tables are exported to Excel.

### 16. Weighted logistic regression

Because QC failures are rare, a weighted logistic regression model is tested.

QC fail samples receive higher weight:

`weight = 20`

while QC pass samples receive weight:

`weight = 1`

This attempts to compensate for class imbalance.

### 17. Linear regression models for call rate

Because call rate is continuous, the script also fits linear regression models.

#### Short linear model

`call_rate ~ log_dna + collector_type + cytology_group`

#### Extended linear model

`call_rate ~ log_dna + collector_type + cytology_group + hpv_final + AGE + sample_year + volume`

These models evaluate predictors of genotyping performance measured as call rate.

### 18. ROC curve

The script calculates predicted probabilities of QC failure and generates:

- ROC curve
- AUC value

This evaluates the predictive performance of the QC failure model.

### 19. Predicted probability density plot

The script plots the distribution of predicted QC failure probabilities by observed QC outcome.

This helps visualize whether the model separates QC pass and QC fail samples.

### 20. Linear model diagnostics

The script generates standard diagnostic plots for the linear model:

1. residuals vs fitted
2. Q-Q plot
3. scale-location plot
4. residuals vs leverage

These assess whether linear regression assumptions are reasonably met.

## Output files generated by the script

The cleaned script generates:

- `odds_ratios_table_qc_cohort.xlsx`
- `modelround1_nocomplete_OR_table.xlsx`
- `modelfinalglmOR_table.xlsx`
- `model_linear_full_results.xlsx`
- `extended_clinical_qc_dataframe.rds`
- `extended_clinical_qc_complete_cases.rds`
- `modelround1_nocomplete.rds`
- `modelround1_more_variables.rds`
- `model_weighted_qc_fail.rds`
- `model_linear_full.rds`
- `model_linear_full2.rds`
