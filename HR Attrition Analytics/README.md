# HR Attrition Analytics

**Tools:** Tableau · KNIME · Decision Trees · Random Forest · Gradient Boosting
**Dataset:** 1,455 employee records

## Problem
Identify the key drivers of employee attrition and translate them into actionable, costed retention recommendations for the business.

## Approach
1. Data profiling and quality diagnosis in KNIME (missing values, encoding, class balance).
2. Built and compared three classification models: Decision Tree, Random Forest, and Gradient Boosting.
3. Visualised attrition drivers and workforce segments in Tableau for a non-technical business audience.
4. Translated top model drivers into concrete, costed retention recommendations.

## Results

| Model | Accuracy | AUC |
|---|---|---|
| Decision Tree | — | — |
| Random Forest | — | — |
| **Gradient Boosting (best)** | **91.8%** | **0.94** |

![Tableau dashboard](tableau-dashboard.png)
![Model comparison](model-comparison.png)

## Key Takeaway
Beyond model accuracy, the real value was translating statistical drivers of attrition into a business-facing Tableau dashboard and a set of prioritised, costed retention actions — the step that turns an analytical model into a decision HR can actually act on.

## Code

`hr_attrition_workflow.knwf` — KNIME workflow: data profiling, cleaning, model comparison
`hr_attrition_dashboard.twbx` — Tableau workbook: attrition drivers and workforce segmentation dashboard

## Files
- `hr_attrition_workflow.knwf` — KNIME workflow
- `hr_attrition_dashboard.twbx` — Tableau workbook
- `tableau-dashboard.png` — dashboard screenshot
- `model-comparison.png` — model accuracy/AUC comparison chart

*MSc Business Analytics, Queen's University Belfast — HR Analytics module*
