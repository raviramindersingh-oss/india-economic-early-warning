# India Economic Early-Warning System

A small-scale machine learning project exploring whether standard macroeconomic indicators can help flag potential next-quarter growth slowdowns in India.

## Overview

This project combines economics and machine learning to investigate whether information available in the current quarter can help identify a subsequent deterioration in real GDP growth.

The project emphasizes:

- real economic data
- transparent target construction
- lagged features
- leakage checks
- chronological time-series validation
- interpretable classical machine learning
- robustness testing

This is a portfolio/feasibility project, not an official economic forecasting system.

## Research Question

**Can standard macroeconomic indicators provide useful information for identifying potential next-quarter slowdowns in India's GDP growth beyond a simple calendar-quarter baseline?**

## Data

The raw dataset contains 79 quarterly observations from April 2004 to October 2023 with seven variables:

- Real GDP
- Inflation
- Industrial production
- Central bank rate
- Exports
- Imports
- Date

The final machine-learning dataset contains 70 observations after constructing the target and required lagged features.

## Target Definition

The primary target is based on year-over-year GDP growth:

> `target = 1` when next-quarter YoY GDP growth is at least 1 percentage point lower than the current quarter's YoY GDP growth.

Otherwise:

> `target = 0`

This definition was selected because an initial quarter-over-quarter target was found to be strongly affected by quarterly seasonality. The final target is therefore designed to capture **growth deterioration** rather than simply identify a particular calendar quarter.

## Features

The model uses current and lagged economic indicators, including:

- YoY GDP growth
- lagged YoY GDP growth
- inflation
- industrial production
- central bank rate
- export growth
- import growth
- selected one-quarter and four-quarter lags

Calendar quarter is used for the baseline comparison.

## Models

Three models are evaluated:

1. **Quarter-only Logistic Regression** — calendar-quarter baseline
2. **Economic Logistic Regression** — primary model
3. **Economic Random Forest** — nonlinear comparison model

The primary Logistic Regression uses standardized features, L2 regularization, and balanced class weighting.

## Validation

The project uses **5-fold expanding-window chronological validation** with `TimeSeriesSplit`.

The data are not randomly shuffled. Standardization is fitted using the training portion of each fold only.

This prevents future observations from being used to train earlier validation periods.

## Results

The main metric is mean ROC-AUC across the five chronological validation folds.

| Model | Mean ROC-AUC | Std. Dev. |
|---|---:|---:|
| Quarter-only Logistic Regression | 0.568 | 0.208 |
| **Economic Logistic Regression** | **0.735** | **0.249** |
| Economic Random Forest | 0.700 | 0.161 |

The economic Logistic Regression performed best overall.

Its mean ROC-AUC was **0.735 ± 0.249**, compared with **0.568 ± 0.208** for the calendar-quarter-only baseline.

Because each validation fold contains only 11 observations, performance varies substantially between periods. The results should therefore be interpreted cautiously.

## Robustness Check

A second target definition was tested:

> next-quarter YoY GDP growth below 5%

Under this alternative definition:

| Model | Mean ROC-AUC | Std. Dev. |
|---|---:|---:|
| Quarter-only Logistic Regression | 0.688 | 0.132 |
| **Economic Logistic Regression** | **0.794** | **0.159** |

The economic-feature model again outperformed the calendar-quarter baseline.

Across the two target definitions, this provides a useful robustness check, although the small sample prevents strong generalization.

## Out-of-Fold Predictions

The repository includes the out-of-fold predictions used to evaluate the primary Economic Logistic Regression:

`results/c1_economic_lr_oof_predictions_sklearn.csv`

The predictions cover the five chronological validation folds and are generated without training on the corresponding validation observations.

## Visualizations

The project can include the following final figures:

1. OOF prediction probability timeline
2. ROC curve model comparison
3. Economic Logistic Regression confusion matrix
4. Robustness target comparison
5. Identified GDP growth slowdown periods

## Limitations

This project has several important limitations:

- The sample is small, with only 70 observations in the final ML dataset.
- Validation folds contain only 11 observations each.
- Fold-level ROC-AUC varies considerably.
- The data do not include historical data-release dates or vintage information, so the analysis does not fully reproduce a real-time forecasting environment.
- Model coefficients and feature importance indicate model associations, not causal effects.
- The system is not intended to be an official recession predictor or production forecasting system.

## Reproducibility

Install the required packages:

```bash
pip install -r requirements.txt
```

Then open:

`notebooks/India_Economic_Early_Warning.ipynb`

The notebook uses relative paths and is designed to run from the repository structure.

## Repository Structure

```text
india-economic-early-warning/
├── README.md
├── requirements.txt
├── data/
│   ├── economic_data.csv
│   └── ml_ready_economic_slowdown.csv
├── notebooks/
│   └── India_Economic_Early_Warning.ipynb
├── results/
│   └── c1_economic_lr_oof_predictions_sklearn.csv
└── figures/
    └── [final project figures]
```

## Conclusion

The results suggest that standard macroeconomic indicators contain useful information for distinguishing periods of potential growth deterioration beyond simple calendar-quarter patterns.

However, the short quarterly sample and substantial variation across validation periods mean that this should be viewed as a **feasibility demonstration**, not a production forecasting system.
