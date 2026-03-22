# Home Credit Default Risk Project

## Overview
This project builds and documents a default-risk modeling workflow for Home Credit, including:
- exploratory analysis,
- reusable data preparation/feature engineering,
- and model governance documentation.

## Project Files

### Analysis and Documentation
- `HOME_CREDIT_EDA.qmd` — Exploratory Data Analysis (target imbalance, predictors, missing data, anomalies, joins)
- `Data_preparation.qmd` — Reusable data preparation pipeline for train/test processing
- `MODEL_CARD.qmd` — Model card (performance, threshold analysis, fairness, risks)
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

## Key Findings (EDA)
- Target is highly imbalanced (~92% non-default vs ~8% default)
- Majority-class baseline is high in accuracy but not useful; AUC is preferred
- Strong predictors include `EXT_SOURCE_*`, age-related fields, and credit history features
- Key data issue: `DAYS_EMPLOYED = 365243` placeholder anomaly
- Missingness itself is informative and handled explicitly in pipeline design

## Data Preparation Script
`Data_preparation.qmd` is organized as reusable functions that:
- clean known anomalies,
- engineer demographic and financial ratio features,
- aggregate supplementary transactional datasets to `SK_ID_CURR`,
- join aggregates back to application-level data,
- and enforce train/test consistency.

### Train/Test Consistency Approach
- Train-only statistics (e.g., medians/bins/thresholds) are computed on training data
- Those values are stored and reused on test data
- Output schemas are aligned so train/test have identical columns (except `TARGET`)

## Model Performance Snapshot
- Kaggle public AUC: **0.794**
- Cross-validation AUC: approximately **0.76**
- Threshold and business-impact analysis documented in `MODEL_CARD.qmd`

## How to Run

```bash
quarto render HOME_CREDIT_EDA.qmd
quarto render Data_preparation.qmd
quarto render MODEL_CARD.qmd
```

## Author
Phan Chung

## Last Updated
March 21, 2026
