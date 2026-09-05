# Airline Review Recommendation — Text Analytics in Python

**Tools:** Python (pandas, scikit-learn) · TF-IDF · Lexicon & transformer-based sentiment · Random Forest · CRISP-DM
**Dataset:** 23,171 airline reviews

## Problem
Predict whether a customer would recommend an airline based on structured ratings and the text of their review, and identify what drives that recommendation.

## Approach (CRISP-DM)
1. **Business understanding:** identify drivers of customer recommendation for airline service improvement.
2. **Data preparation:** missing values in key numerical variables handled via median imputation (robust to skewed, ordinal data; more stable than k-NN imputation under sparse or heterogeneous-scale conditions). Extracted text sentiment using lexicon-based and transformer-based methods, plus TF-IDF features from review text.
3. **Modelling:** compared numerical-only, text-only, and combined feature models using Random Forest.
4. **Evaluation:** chronological 80/20 split on Date Flown (Train: 15,533 · Test: 3,884) — a forward-looking robustness check that evaluates the model on genuinely future reviews rather than a random split.

## Results — Numerical vs. Text vs. Combined Features

| Measure | Numerical | Text | Combined |
|---|---|---|---|
| Accuracy | 0.948 | 0.893 | **0.958** |
| F1-Score | 0.925 | 0.848 | **0.939** |
| ROC-AUC | 0.989 | 0.951 | **0.991** |
| False Positives | 105 | 221 | **87** |
| False Positive Rate | 5.2% | 10.7% | **4.2%** |

Combining structured ratings with text-derived features outperformed either feature set alone across every metric, cutting the false-positive rate roughly in half compared to text-only and meaningfully below numerical-only.

## Top Dominant Features (Text Model)

| Feature | Coefficient | Effect |
|---|---|---|
| review | 3.43 | Strongly positive |
| thank | 2.43 | Positive |
| easy | 2.23 | Positive |
| early | 2.07 | Positive |
| Value For Money | 1.98 | Positive |
| smooth | 1.67 | Positive |
| new | 1.50 | Positive |
| delayed | -2.24 | Strongly negative |
| worst | -2.19 | Negative |
| hour | -2.11 | Negative |
| disappointing | -1.91 | Negative |
| pm | -1.74 | Negative |
| changed | -1.74 | Negative |
| doesnt | -1.58 | Negative |
| sitting | -1.53 | Negative |
| unfortunately | -1.49 | Negative |

## Key Takeaway
Combining numerical ratings with text-derived sentiment features improved predictive power over either alone, and a chronological (rather than random) evaluation split gives a more honest estimate of real-world performance. Delay-related language ("delayed", "hour", "pm") is a consistent, strong signal of dissatisfaction, while service-quality and value language ("thank", "easy", "Value For Money") drives positive recommendation — pointing directly to punctuality and cabin experience as the highest-leverage areas for improvement.

## Recommendations
1. **Strengthen operational reliability** — delay-related terms consistently signal dissatisfaction, so punctuality and disruption management should be core improvement areas.
2. **Prioritise cabin experience improvements**, particularly in Premium Economy and Economy, where dissatisfaction is structurally highest.
3. **Address staff behaviour** — negative interpersonal-interaction terms strongly influence recommendation likelihood.
4. **Encourage satisfied passengers to leave reviews**, since positive text tokens meaningfully strengthen predictive confidence.
5. **Integrate real-time text analytics into feedback systems** to enable early detection of emerging issues and proactive service recovery.

## Code

`airline_review_analysis.ipynb` — Section 1: data cleaning & median imputation · Section 2: sentiment extraction & TF-IDF feature engineering · Section 3: Random Forest models (numerical / text / combined) · Section 4: chronological evaluation

## Files
- `airline_review_analysis.ipynb` — full analysis notebook

*MSc Business Analytics, Queen's University Belfast*
