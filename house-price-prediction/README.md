# House Price Prediction — Predictive Modelling in R

**Tools:** R · Random Forest · Multiple Linear Regression
**Dataset:** 15,147 property records, 41 variables

## Problem
Predict property sale price from structural, locational and quality features, and compare a flexible non-linear model against a traditional linear baseline.

## Approach
1. Data cleaning: IQR-based outlier detection, log transformation of skewed variables, multicollinearity checks (VIF, Durbin-Watson).
2. Built four progressively refined linear regression models — starting with key focal variables, then testing log transformation, then adding numerical and categorical variables.
3. Comparison model: Random Forest, capturing non-linear relationships and feature interactions.
4. Evaluated all models on RMSE, R², MAE, autocorrelation (Durbin-Watson) and multicollinearity (VIF).

## Results

| Model | RMSE | R² (%) | MAE | Durbin-Watson | VIF |
|---|---|---|---|---|---|
| Key focal areas, no log transform (Model 1) | 462k | 30.09 | 211k | 1.67 | 1–2 (no multicollinearity) |
| Key focal areas, with log transform (Model 2) | 440k | 35.46 | 194k | 1.31 | 1–2 (no multicollinearity) |
| + Numerical & category #1 (Model 3) | 436k | 42.42 | 183k | 1.33 | 1–2 (no multicollinearity) |
| + Numerical & all categories (Model 4) | 434k | 42.09 | 180k | 1.34 | 1–2 (no multicollinearity) |
| **Random Forest (comparison)** | **291k** | **77.45** | **102k** | — | — |

## Interpretation

- Larger living areas, more bedrooms, better school ratings, waterfront access, and having a spa and garage are strong drivers of house price.
- Location matters, but only for certain cities — it is not a uniformly strong predictor across the whole dataset.
- Log transformation and progressively adding numerical and categorical variables steadily improved the linear models (R² rising from 30.1% to 42.4%), but even the best linear model was substantially outperformed by Random Forest (R² 77.45%), confirming meaningful non-linear relationships in the data that linear regression cannot capture.
- All linear models passed multicollinearity checks (VIF between 1 and 2) and showed acceptable residual independence (Durbin-Watson close to 2), meaning the regression coefficients are reliable and interpretable — supporting the interpretation above.

## Key Takeaway
Careful iterative model-building — testing transformations, then adding variables in stages — makes it possible to isolate exactly which changes drive predictive improvement, while diagnostic checks (VIF, Durbin-Watson) confirm the model's coefficients can be trusted for interpretation. Random Forest's clear advantage over even the best-specified linear model shows the value of testing both interpretable and flexible approaches before drawing business conclusions.

## Code

`house-price-model.R` — Section 1: data cleaning, outlier & multicollinearity checks · Section 2: linear regression models (1–4) · Section 3: Random Forest model · Section 4: evaluation & comparison

## Files
- `house-price-model.R` — full analysis script

*MSc Business Analytics, Queen's University Belfast — Statistics for Business module*
