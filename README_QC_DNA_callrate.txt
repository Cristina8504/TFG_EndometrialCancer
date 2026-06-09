# README - Quality control exploratory analysis

## Project

**Evaluation of DNA Quality and Case-Cohort Analysis of Endometrial Cancer-Associated SNPs**

This README explains script used to explore DNA quantity, genotype call rate and sample-level quality control outcome in genotyping data.

## Files

### 1. `QC_DNA_callrate_codigo_comentado.txt`

The script performs an exploratory analysis of the relationship between:

- total DNA amount (`totdna_ng`)
- genotype call rate (`call_rate_raw`)
- sample QC outcome (`QC_pass`)

### 2. `README_QC_DNA_callrate.txt`

This file explains the purpose of the analysis, the expected input files, the main steps and the outputs.

## Input files

The script uses two input files:

### QC statistics file

`WK-3864_241205_QCstats.tsv`

This tab-separated file contains sample-level genotyping QC statistics.

Expected variables include:

- `LABEL_ID`: sample identifier
- `QC_pass`: whether the sample passed QC
- `totdna_ng`: total DNA amount in nanograms
- `call_rate_raw`: raw genotype call rate, equivalent to `1 - F_MISS`

### DNA extraction Excel file

`XL-4188_250410_Conc_TotalDNA_KI_karinsunstrom1stround.xlsx`

This file contains DNA extraction or concentration data from the first round.

The script compares this Excel file with the QC statistics file to check whether both datasets contain matching sample identifiers.

## Main purpose of the analysis

The main aim of this analysis is to evaluate whether DNA quantity is associated with genotyping performance and QC outcome.

Specifically, the script explores:

1. How many samples passed or failed QC.
2. Whether sample IDs are duplicated.
3. Whether the QC file and DNA extraction Excel file contain the same samples.
4. The distribution of total DNA amount.
5. The distribution of genotype call rate.
6. Whether samples with low DNA amount fail QC more often.
7. Whether high-DNA samples can still fail QC.
8. Whether applying DNA thresholds would reduce QC failures or cause excessive sample loss.

## Main analysis steps

### 1. Load the QC statistics file

The script reads the `.tsv` file using `read.delim()`.

### 2. Inspect the dataset

The script checks:

- structure of the dataset
- number of rows
- QC pass/fail counts
- number of unique sample IDs
- duplicated sample IDs

This confirms whether each row corresponds to one unique sample.

### 3. Compare QC file with DNA extraction Excel file

The script loads the Excel file using `read_excel()` and compares sample IDs between both datasets.

In the original analysis, there were no direct matches between `LABEL_ID` and `Sample_ID`, suggesting that the files either did not correspond to the exact same sample set or used different ID formats.

### 4. Clean variables

The script converts:

- `LABEL_ID` to character
- `totdna_ng` to numeric
- `call_rate_raw` to numeric
- `QC_pass` to logical

It also creates:

- `QC_fail`, defined as the opposite of `QC_pass`

### 5. Explore call rate by QC status

The script calculates minimum, maximum, mean and median call rate for QC-pass and QC-fail samples.

It also counts how many samples have `call_rate_raw < 0.98`.

This is important because sample-level QC failure is not necessarily determined only by call rate. Other QC metrics may also contribute to exclusion.

### 6. Descriptive statistics

The script calculates:

- QC pass/fail counts
- QC pass/fail proportions
- summary statistics for total DNA amount
- summary statistics for genotype call rate

### 7. DNA bin analysis

The script groups samples into DNA amount categories.

First, broad bins are created:

- `[0,100)` ng
- `[100,200)` ng
- `[200,Inf)` ng

Then, finer bins of 20 ng are created from 0 to 200 ng, plus a final category above 200 ng.

For each bin, the script calculates QC pass/fail counts and proportions.

### 8. Plots

The script generates several plots:

#### Boxplot

Shows genotype call rate by QC status.

#### Bar plot by broad DNA bins

Shows QC pass/fail proportions and counts across broad DNA categories.

#### Bar plot by 20 ng DNA bins

Shows QC pass/fail proportions across finer DNA amount categories.

#### Scatter plot

Shows the relationship between total DNA amount and genotype call rate, with total DNA displayed on a log10 scale.

### 9. High-DNA QC failures

The script identifies samples with:

`totdna_ng >= 200` and `QC_pass == FALSE`

This helps assess whether DNA quantity alone explains QC outcome.

If some samples with high DNA still fail QC, this suggests that other factors may influence genotyping performance, such as DNA integrity, inhibitors, storage conditions, collection variability or batch effects.

### 10. Threshold analysis

The script evaluates four possible minimum DNA thresholds:

- 50 ng
- 100 ng
- 150 ng
- 200 ng

For each threshold, it calculates:

- number of retained samples
- fraction of retained samples
- QC failure rate among retained samples
- number of QC failures avoided
- number of QC-pass samples lost

This evaluates the trade-off between improving QC performance and losing valid samples.

### 11. Sample retention plot

The script plots the fraction of samples retained as the minimum DNA threshold increases.

This helps visualize the loss of sample size caused by stricter DNA thresholds.

## Output files generated by the script

### `threshold_analysis.csv`

A CSV file containing the DNA threshold analysis.

Columns include:

- `threshold_ng`
- `n_kept`
- `kept_fraction`
- `fail_rate_kept`
- `fails_avoided`
- `passes_lost`

### `qc_cleaned_dataframe.rds`

An RDS file containing the cleaned QC dataset.

### `threshold_analysis_results.rds`

An RDS file containing the DNA threshold analysis results.

