# Home Credit Default Risk Project

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
- Majority-class baseline can show high accuracy but weak discrimination; AUC is the primary metric
- Strong predictors include `EXT_SOURCE_*`, age-related fields, and credit history features
- Key anomaly: `DAYS_EMPLOYED = 365243` placeholder value
- Missingness is informative and explicitly modeled via indicators/imputation strategy

## Data Preparation
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

## Modeling Notebook Summary (`Modeling.qmd`)

### Models Compared
The modeling notebook evaluates multiple model families:
- Majority-class baseline
- Logistic regression (core feature set and expanded feature set)
- Random Forest
- XGBoost (gradient boosting)

### Performance Comparison
- Baseline majority model achieves high accuracy due to imbalance, but AUC near random
- Logistic regression improves ranking vs. baseline but underperforms tree ensembles
- Random Forest improves non-linear capture but is less efficient at this scale
- XGBoost delivers the strongest out-of-sample AUC during 3-fold CV

### Class Imbalance Experiments
The notebook compares:
- no adjustment,
- class weighting (`scale_pos_weight`),
- upsampling,
- downsampling.

Best practical performance came from **XGBoost with class weighting**, balancing predictive power and runtime.

### Hyperparameter Tuning Strategy
- Randomized search (about 20 iterations)
- 5K-row sample for tuning speed
- 3-fold CV for robust but efficient estimates
- Final model retrained on full training data using best parameters

### Why Final Model Was Selected
The final model is **tuned XGBoost with imbalance adjustment and supplementary features** because it:
- consistently produced the best estimated out-of-sample AUC,
- handled class imbalance effectively,
- benefited from engineered + aggregated bureau/previous/installment features,
- and scaled efficiently for full-data training.

### Kaggle Result
- Public leaderboard AUC: **0.794**
- Submission generated from final model in `submission_modeling.csv`

## Model Performance Snapshot
- Kaggle public AUC: **0.794**
- Cross-validation AUC: approximately **0.76**
- Threshold/business impact analysis documented in `MODEL_CARD.qmd`

## How to Run

```bash
quarto render HOME_CREDIT_EDA.qmd
quarto render Data_preparation.qmd
quarto render Modeling.qmd
quarto render MODEL_CARD.qmd
```

> If local data files are outside the repo path, update file paths in notebook load-data chunks before execution.

## Author
Phan Chung

## Last Updated
March 22, 2026
