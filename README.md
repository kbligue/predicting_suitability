# Suitability Modelling Workflow African Swine Fever in the Philippines

## Overview

This repository contains the R code used to develop and evaluate machine learning models for predicting African swine fever (ASF) suitability in the Philippines. The workflow includes data preparation, predictor screening, model training and evaluation using cross-validation, final model fitting, suitability prediction, characterisation of predicted suitable areas, and generation of supplementary outputs used in the associated manuscript.

The analysis has been developed with reproducibility in mind. Package versions are managed using **renv**, and the repository is organised so that the primary analysis pipeline is separated from auxiliary scripts used to generate figures and supplementary outputs.

---

## Repository structure

```text
.
├── main.R                     # Main analysis pipeline
├── R/                         # Helper functions used by main.R
├── data/                      # Input datasets
│   └── folds/                 # Cross-validation fold datasets
├── scripts/
│   └── R/                     # Stand-alone utility R scripts
│   └── ArcPy/                 # ArcPy scripts for data processing
├── outputs/                   # Analysis outputs (directory created during execution)
├── index.html				   # Interactive suitability map widget published as GitHub Pages
├── renv/                      # renv project infrastructure
├── renv.lock                  # Reproducible package environment
├── .Rprofile
├── asf-ph-suitability.Rproj   # RStudio project
└── README.md
```
# Reproducibility

This project uses the **renv** package to manage package versions.

## First-time setup

Clone the repository and open the RStudio project.

If `renv` is not already installed:

```r
install.packages("renv")
```

Restore the project environment:

```r
renv::restore()
```

This installs the package versions recorded in `renv.lock`.

---

## Running the analysis

Run the complete modelling workflow by executing:

```r
source("main.R")
```

or by opening `main.R` in RStudio and clicking **Source**.

---

# Input data

Input datasets should be placed in the `data/` directory.

The repository assumes that all required input files are available before executing `main.R`.

Cross-validation fold datasets are stored in:

```text
data/folds/
```

These datasets are generated using `scripts/R/data_split_for_cv.R`.

---

# Notes

* Predictor removal following multicollinearity assessment is **not fully automated**. Variables removed after the multicollinearity analysis are manually selected based on researcher judgement and are intentionally hard-coded to ensure reproducibility of the published analysis.
* The final model configuration is **not selected automatically**. The model type and hyperparameter settings are manually specified based on the cross-validation results to reproduce the final model reported in the associated manuscript.
* Cross-validation fold assignments are fixed to ensure consistent model comparisons.
* Random number generation is controlled using fixed seeds to maximise reproducibility.

---


### Main pipeline

The primary workflow is executed from:

```text
main.R
```

This script orchestrates the complete modelling pipeline by calling helper functions stored in the `R/` directory.

The pipeline performs the following major steps:

1. **Configure reproducibility and the analysis environment**
   - Sets the master random seed and apply reproducibility settings.
   - Loads the required packages and project helper functions.

2. **Define analysis settings**
   - Specifies the identifier, response, and predictor columns.
   - Defines the number of cross-validation folds, the correlation threshold, and input/output locations.

3. **Configure candidate models**
   - Defines the training and hyperparameter-tuning settings for random forest, boosted regression tree, and MaxEnt models.

4. **Create output directories**
   - Creates the directories used to store trained models, fold-level metrics, predictions, and other analysis outputs.

5. **Screen predictors for multicollinearity**
   - Evaluates predictor correlations across the cross-validation datasets.
   - Generates a correlation plot.
   - Removes selected correlated predictors following manual review.

6. **Train and evaluate candidate models using cross-validation**
   - Fits candidate models using the pre-generated cross-validation folds.
   - Tunes model hyperparameters.
   - Saves trained fold models, predictions, variable-importance results, and performance metrics.

7. **Assess agreement among candidate-model predictions**
   - Assesses agreement among candidate models using their cross-validation predictions.

8. **Train the final model and generate suitability predictions**
   - Retrains the selected model on the full modelling dataset using the chosen hyperparameter configuration.
   - Applies the final model to the training and deployment dataset to generate suitability predictions.

9. **Record the computational environment**
   - Saves `sessionInfo()` to document the R version, platform, and package versions used in the analysis.

---

## Helper functions (`R/`)

The `R/` directory contains reusable functions supporting the main pipeline.

These scripts are intended to be sourced automatically by `main.R` and generally should not be executed independently.

---

## Stand-alone scripts (`scripts/R/`)

Several analyses are intentionally separated from the main workflow because they either prepare shared inputs or generate post-processing outputs used in the manuscript.

### `data_split_for_cv.R`

Creates the cross-validation datasets used by the modelling workflow.

This script partitions the input data into cross-validation folds and exports each fold as an individual CSV file in:

```text
data/folds/
```

These fold datasets are shared across all modelling algorithms to ensure consistent cross-validation partitions.

This script only needs to be run when generating new cross-validation splits.

---

### `hyperparameter_tuning_summary.R`

Summarises hyperparameter tuning results as presented in Supplementary File S3.

---

### `variable_importance_ranking.R`

Ranks variables by importance within each fold, then summarises the mean and SD of each variable's rank across folds.

---

### `characterise_predicted_suitable_areas.R`

Conducts the four-step analyses to characterise predicted suitable areas:
1. EDA: boxplot comparison of predictors and suitability scores, suitable vs unsuitable areas
2. One-dimensional partial dependence plots for each influential predictor
3. Pairwise interaction partial dependence plots (heatmaps) with Friedman's H interaction-strength statistic
4. Clustering of suitable areas' predictor profiles 


---
### `interactive_map.R`

Builds an interactive Leaflet map of the final model predictions.


---

# Software requirements

The workflow has been developed in R.

Package versions are managed through `renv`.

After running:

```r
renv::restore()
```

all required R packages should be installed automatically.

---

# Citation

If you use this code, please cite:

> **<Authors>** (<Year>). *<Title of manuscript>*. <Journal>. <DOI>

---

# License

Specify the licence under which this repository is distributed (e.g., MIT License or GPL-3.0).

---

# Contact

For questions regarding the code or the associated manuscript, please contact:

**<Name>**

**<Institution>**

**<Email>**
