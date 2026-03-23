
## Overview
This project develops an end-to-end default-risk pipeline for Home Credit, including:
- exploratory analysis,
- reusable data preparation and feature engineering,
- comparative modeling and hyperparameter tuning,
- Kaggle submission generation,
- and model governance documentation.

## Project Files

### Analysis and Documentation
- `HOME_CREDIT_EDA.qmd` — Exploratory data analysis (target imbalance, predictors, missing data, anomalies, supplementary joins)
- `Data_preparation.qmd` — Reusable train/test data preparation and feature engineering functions
- `Modeling.qmd` — Full modeling workflow (benchmarks, model comparison, imbalance handling, tuning, final training, submission)
- `Modeling.html` — Rendered modeling report
- `MODEL_CARD_NOTEBOOK.qmd` — Assignment model card notebook (executive summary, performance, threshold analysis, SHAP, fairness, risks)
- `MODEL_CARD.qmd` — Extended model card version
- `MODEL_CARD_README.md` — Quick guide to model card sections

### Core Data Sources
- `application_train.csv`
- `application_test.csv`
- `bureau.csv`
- `bureau_balance.csv`
- `previous_application.csv`
- `installments_payments.csv`
- `credit_card_balance.csv`
- `POS_CASH_balance.csv`
- `sample_submission.csv`

## [Key Findings (EDA)](https://github.com/PhanChung09/home-credit-project/blob/main/HOME_CREDIT_EDA.html)
- Target is highly imbalanced (~92% non-default vs ~8% default)
- Majority-class baseline can show high accuracy but weak discrimination; AUC is the primary metric
- Strong predictors include `EXT_SOURCE_*`, age-related fields, and credit history features
- Key anomaly: `DAYS_EMPLOYED = 365243` placeholder value
- Missingness is informative and explicitly modeled via indicators/imputation strategy

## [Data Preparation](https://github.com/PhanChung09/home-credit-project/blob/main/Data_preparation.html)
`Data_preparation.qmd` provides reusable functions that:
- fix known anomalies,
- engineer demographic and financial-ratio features,
- aggregate supplementary transactional data to `SK_ID_CURR`,
- join aggregated features back to application data,
- and keep train/test feature schemas consistent.

### Train/Test Consistency Approach
- Train-only statistics (medians/bin thresholds) are fit on training data
- Stored values are reused on test data
- Train/test columns are aligned identically (except `TARGET`)

## [Modeling Notebook Summary (`Modeling.qmd`)](https://github.com/PhanChung09/home-credit-project/blob/main/Modeling.html)

### Models Compared
- Majority-class baseline
- Logistic regression (core and expanded predictor sets)
- Random forest
- XGBoost

### Performance Summary
- Baseline accuracy is inflated by imbalance and not sufficient
- AUC-based comparison favored boosted trees
- XGBoost delivered strongest out-of-sample CV AUC with practical training time

### Class Imbalance and Tuning
- Compared no adjustment, class weighting, upsampling, and downsampling
- Used randomized search (~20 iterations) with 5K sample and 3-fold CV
- Selected tuned XGBoost and retrained on full training data

### Kaggle
- Submission file generated in the modeling notebook
- Public leaderboard AUC: **0.794**

## Model Card Assignment Notebook (`MODEL_CARD_NOTEBOOK.qmd`)

This notebook covers the required assignment sections:
- Model Details
- Intended Use
- Performance Metrics (CV AUC, Kaggle AUC, precision/recall at threshold)
- Decision Threshold Analysis with cost assumptions and sensitivity analysis
- Explainability (SHAP on 1,000-row sample)
- Adverse Action Mapping
- Fairness Analysis (`CODE_GENDER`, `NAME_EDUCATION_TYPE`)
- Limitations and Risks
- Executive Summary

Speed-up constraints are explicitly implemented:
- SHAP computed on a 1,000-row sample
- Modeling workflow/model settings reused from `Modeling.qmd`

## How to Run

```bash
quarto render HOME_CREDIT_EDA.qmd
quarto render Data_preparation.qmd
quarto render Modeling.qmd
quarto render MODEL_CARD_NOTEBOOK.qmd
```

## Author
Phan Chung

## Last Updated
March 22, 2026
