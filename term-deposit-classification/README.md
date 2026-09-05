# Bank Term-Deposit Marketing — Classification in R

**Tools:** R · Logistic Regression · Feature Engineering
**Dataset:** 40,966 telemarketing records, 29 variables

## Problem
Predict whether a bank customer will subscribe to a term deposit following a telemarketing campaign, and identify the strongest drivers of a positive response.

## Approach
1. Feature engineering on campaign and customer variables (prior contact outcome, contact frequency, macroeconomic indicators).
2. Built sequential logistic regression models, adding feature groups iteratively to assess contribution.
3. Imbalance-aware evaluation (the positive class — subscribers — is a minority).
4. Final model evaluated on AUC and balanced accuracy rather than raw accuracy.

## Results

- **AUC: 0.857**
- **Balanced accuracy: 0.72**
- Strongest predictors: outcome of prior marketing campaign, and macroeconomic indicators (employment variation rate, consumer confidence).

![ROC curve](roc-curve.png)
![Top predictors](feature-importance.png)

## Key Takeaway
Prior customer interaction history and macroeconomic context outweigh demographic variables in predicting campaign response — a directly actionable insight for targeting future campaigns, and a demonstration of translating a classification model into a business recommendation.

## Code

`term-deposit-model.R` — Section 1: feature engineering · Section 2: sequential logistic regression models · Section 3: imbalance-aware evaluation (AUC, balanced accuracy) · Section 4: driver analysis

## Files
- `term-deposit-model.R` — full analysis script
- `roc-curve.png` — ROC curve for final model
- `feature-importance.png` — top predictor variables

*MSc Business Analytics, Queen's University Belfast*
