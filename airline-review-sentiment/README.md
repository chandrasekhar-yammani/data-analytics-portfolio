# Airline Review Recommendation — Text Analytics in Python

**Tools:** Python (pandas, scikit-learn) · TF-IDF · Lexicon & transformer-based sentiment · Random Forest · CRISP-DM
**Dataset:** 23,171 airline reviews

## Problem
Predict whether a customer would recommend an airline based on structured ratings and the text of their review, and identify what drives that recommendation.

## Approach (CRISP-DM)
1. **Business understanding:** identify drivers of customer recommendation for airline service improvement.
2. **Data preparation:** cleaned structured ratings; extracted text sentiment using both lexicon-based and transformer-based methods, and TF-IDF features from review text.
3. **Modelling:** combined structured and text-derived features in a Random Forest classifier.
4. **Evaluation:** chronological hold-out split (train on earlier reviews, test on later ones) to simulate real-world deployment rather than random splitting.

## Results
- **F1 score: 0.917 / Accuracy: 96%** on chronological hold-out.
- **Value-for-money** emerged as the strongest driver of recommendation — stronger than in-flight service or punctuality alone.

![Top terms driving recommendation](top-terms.png)
![Confusion matrix](confusion-matrix.png)

## Key Takeaway
Combining structured ratings with text-derived sentiment features improved predictive power over ratings alone, and a chronological (rather than random) evaluation split gives a more honest estimate of real-world performance — an approach directly applicable to customer-feedback and NPS-style business problems.

## Code

`airline_review_analysis.ipynb` — Section 1: data cleaning & sentiment extraction · Section 2: TF-IDF feature engineering · Section 3: Random Forest model · Section 4: chronological evaluation

## Files
- `airline_review_analysis.ipynb` — full analysis notebook
- `top-terms.png` — top terms/word importance chart
- `confusion-matrix.png` — model confusion matrix

*MSc Business Analytics, Queen's University Belfast*
