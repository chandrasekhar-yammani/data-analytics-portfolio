# Machine Learning for Fraud Detection in High-Volume Transactional Systems

**A Cost-Sensitive, Explainable Evaluation of Supervised and Unsupervised Approaches**

**Tools:** Python · LightGBM · Logistic Regression · Isolation Forest · SHAP · Optuna · scikit-learn · SLURM/HPC
**Dataset:** IEEE-CIS Fraud Detection — 590,540 transactions, 434 features, 3.50% fraud rate (27.6:1 imbalance)

*MSc Business Analytics Dissertation, Queen's University Belfast*

## Problem

**Business problem: Given a fraud team that can only review a fixed number of transactions per day, how should transactions be routed to capture the most fraud value?**

Most published fraud detection work optimises a model score. That is not the problem a fraud operations team has. Every escalation costs investigator time; every missed fraud costs the transaction value plus downstream chargeback and remediation. Those costs are asymmetric by roughly 20:1 — and a model recommending more reviews than the team can perform has not solved anything, however well it ranks.

**Capabilities demonstrated:** Risk analytics · Cost-sensitive decision-making · Class-imbalance modelling · Explainable AI · Model governance

## Aim and Research Questions

To develop and evaluate a data-driven machine learning framework for fraud detection in high-volume transactional systems that balances predictive performance, operational relevance and interpretability.

| # | Research question | Answered in |
|---|---|---|
| 1 | How effectively can anomaly detection identify fraudulent behaviour? | [Model comparison](#model-comparison-holdout-pr-auc-with-95-bootstrap-ci) · [Anomaly is not fraud](#key-findings) |
| 2 | How do supervised and unsupervised approaches compare in this context? | [Model comparison](#model-comparison-holdout-pr-auc-with-95-bootstrap-ci) |
| 3 | What is the cost-benefit trade-off across different detection thresholds? | [The capacity problem](#the-capacity-problem) · [Three-tier routing](#three-tier-routing-framework-holdout) |
| 4 | How can explainability improve the practical adoption of these models? | [Key findings](#key-findings) · [Recommendation 3](#business-recommendations) |

## Approach

![Analytical framework](figures/fig1_framework.png)

1. **Chronological train/validation/holdout split** rather than random k-fold — justified by preliminary analysis showing weekly fraud rate varying 1.85%–5.06% and weekly volume 2,754–37,231. Random splitting would leak future periods and produce a deployment-optimistic result.
2. **Three model families compared:** LightGBM (primary), logistic regression (interpretable baseline), Isolation Forest (unsupervised comparison). Hyperparameters tuned with Optuna over 50 trials per configuration, run as SLURM batch jobs on the university HPC cluster.
3. **Backward-looking behavioural features** engineered from pseudo-entity identifiers (card + address + email), giving 92,690 entities with prior-transaction count, inter-transaction time, and prior amount statistics.
4. **Cost-sensitive threshold optimisation** using a Total Operational Loss equation, with cost parameters grounded in LexisNexis Risk Solutions (2024) and Höppner et al. (2022).
5. **Capacity-constrained variant** — optimise cost subject to a fixed alert rate — plus a three-tier operational routing framework (approve / step-up authentication / manual review).
6. **Explainability and validation** — SHAP attribution, isotonic probability calibration, bootstrap confidence intervals (2,000 resamples) and paired bootstrap significance tests.

## Results

### Model comparison (holdout, PR-AUC with 95% bootstrap CI)

| Model | Features | PR-AUC | 95% CI |
|---|---|---|---|
| **LightGBM (full feature set)** | 444 | **0.535** | [0.519, 0.551] |
| LightGBM (lean, interpretable) | 65 | 0.507 | [0.491, 0.524] |
| Logistic Regression (L1, C=0.01) | 252 | 0.361 | [0.345, 0.378] |
| Isolation Forest (unsupervised) | 444 | 0.113 | — |

Base rate is 3.44% on holdout, so all supervised models discriminate substantially. Gradient boosting beat logistic regression by +0.171 PR-AUC (paired bootstrap, p<0.001).

### The capacity problem

![The cost-sensitive decision process](figures/fig2_cost_decision.png)

The cost-optimal threshold sits near zero because a missed fraud costs ~$302 against ~$15 for a false positive. At that ratio precision barely enters the objective function, so the optimiser escalates roughly a fifth of all traffic — mathematically correct, operationally impossible. Constraining the same model to a 2.5% alert rate brings the workload down to roughly seventy-five investigations a day while still capturing 47.3% of fraud value at 62.4% precision.

### Three-tier routing framework (holdout)

| Tier | Volume | Fraud rate in tier | Share of fraud value |
|---|---|---|---|
| Manual review | 2.6% (~75/day) | 62.4% | 47.3% |
| Step-up authentication | 9.8% | 8.7% | 24.8% |
| Approve | 87.6% | 1.09% residual | — |

Boundaries fitted on validation at 2.5% review capacity and a 10% friction budget. A capacity-blind expected-value routing of the same model would send 434 transactions a day to manual review — nearly six times the workload for the same underlying model.

## Key Findings

**Anomaly is not fraud.** The Isolation Forest's top 1% of anomaly scores contained *zero* fraudulent transactions, where chance alone would give ~40. Verified not to be a tie artefact (98.6% unique scores, no degenerate block). The most anomalous transactions in this data are systematically legitimate — unusual but genuine purchases. Direct evidence against the intuition that unsupervised anomaly detection suits fraud.

**High transaction value is anti-predictive.** Contrary to the working hypothesis, the top 1% by value had a fraud rate of 2.32% against a 3.52% baseline — *below* average. Structured low-value transactions flagged at 11.15%, a 3.2x lift. The hypothesis was backwards and is reported as such.

**The 339 anonymised features are individually near-worthless.** The anonymised V-block absorbs the largest share of SHAP attribution (24.4%) only because there are 339 of them — per feature it carries 0.0096 against 0.274 for the eight card/address features, roughly **28x less informative per feature**. A lean 65-feature fully interpretable model reaches ~95% of full-model performance: a trade worth making where every feature must be explainable to a regulator.

**Importance is not direction.** The card identifier has the highest mean absolute SHAP value but a *negative* mean signed value, with only 34% of instances pushing toward fraud. Most cards have low historical fraud rates, so knowing the card usually *clears* a transaction.

**Class weighting made things worse.** The methodology had framed class weighting and cost-sensitive thresholding as complementary. Testing showed unweighted models beat weighted ones on holdout for every LightGBM variant (paired test −0.0031, p=0.012), and weighting badly damaged logistic regression calibration. The threshold does all the work; the methodology chapter was rewritten to match.

**The identity data block added nothing measurable.** Segmentation showed far stronger performance where identity data was present (PR-AUC 0.719 vs 0.279) — but a controlled ablation holding rows constant found +0.0017 at p=0.434. The segment gap is a base-rate difference between two populations (9.4% vs 2.0% fraud), not evidence the features carry information. Causal language was corrected throughout.

## Key Takeaway

The constraint was not a limitation on the analysis — it was the thing that made the analysis useful. An unconstrained cost optimisation produced a technically optimal system requiring hundreds of manual reviews per day that no fraud team could staff. Reframing it as a capacity-constrained problem produced a routing framework capturing 47.3% of fraud value at ~75 reviews per day, with a residual fraud rate of 1.09% on the 87.6% of transactions approved automatically. The analytical contribution is the translation from model score to staffable operating policy, not the model score itself.

## Business Recommendations

Each recommendation names an owner, a measurable KPI and a target, so the analysis lands as an operating decision rather than a report.

![Recommendations, owners and KPIs](figures/fig3_recommendations.png)

**1. Adopt capacity-constrained, three-tier routing as the deployment model.** Investigator capacity is a real operating constraint, not a parameter to discover after go-live. The review-tier boundary should be sized to actual staffing rather than to the mathematical cost optimum, with a named business owner accountable for it once live. *Caveat: the cost parameters are industry benchmarks, not any one institution's loss figures, and should be recalibrated before driving staffing decisions.*

**2. Direct data investment toward transaction-history features, not new identity acquisition.** A controlled ablation removing identity and device features produced no statistically detectable change in performance. The stronger segment-level result reflects a difference in fraud prevalence between two populations, not a causal contribution from the fields themselves — so identity-data expansion should not be funded as a fraud-detection priority on this evidence. *The ablation used hyperparameters tuned with identity features present, biasing the test toward finding a benefit; the null result is correspondingly more notable.*

**3. Retain the interpretable model as a governance instrument, not a discarded baseline.** The regularised logistic regression recovers roughly two-thirds of the leading model's performance and surfaces the same leading risk factors independently, giving compliance and investigation teams a second auditable line of evidence ahead of any external review.

**4. Separate frequent operational monitoring from periodic formal review.** No coherent performance decay was detected across the six-week holdout, but absence of observed decay over six weeks is not evidence of stability at a longer horizon — and does not by itself establish a retraining cadence. Operations should monitor alert-rate deviation and calibration drift continuously; model risk should review formally on a quarterly cycle, using the operational signal rather than the calendar to decide whether retraining is warranted.

## Validation & Engineering Rigour

- **19-check leakage audit** passed on real data: temporal partition integrity, behavioural features verified strictly backward-looking (prior means manually recomputed against 50 sampled entities), imputation statistics and encoders fitted on training rows only, no identifiers or timestamps in any branch.
- **Out-of-fold target encoding** introduced after finding the original encoder fit and transformed the same partition; corrected results came in slightly lower, as expected.
- **Two silent bugs caught by reading output rather than exit codes** — 15 text-valued identity columns silently dropped from one model branch, and a PCA step collapsing 339 columns into one component by zero-filling without scaling. Both runs completed cleanly; both would have invalidated results.
- **Probability calibration** — isotonic calibration reduced expected calibration error from 0.0169 to 0.0076 and the predicted-to-observed ratio from 0.51 to 0.97.
- **An unfair comparison caught and re-run** — one configuration had received 30 tuning trials under CPU contention against another's 50 clean trials. The conclusion reversed under matched conditions.

## Limitations

- Labels cover only *detected* fraud; undetected fraud is labelled legitimate, so the model learns the fraud existing systems already catch.
- Median pseudo-entity has only 2 transactions, so behavioural history is thin for a large share of rows.
- Calibrated probabilities still over-predict in the top decile, which systematically over-escalates the highest-risk transactions under expected-loss routing.
- Six time-based holdout windows showed fluctuating performance with no monotonic decay, so **no retraining cadence recommendation is supported** by this evidence.
- One instructive failure: a missed fraud of $1,795.80 scored 0.0027 — a high-value transaction on a low-risk card and address, where all three major features pushed risk *down*.

## Relevance

Builds directly on the class-imbalance and threshold-tuning approach rehearsed in my [Star Emergence imbalanced classification project](../star-emergence-imbalanced-classification), extended here with a cost-sensitive evaluation framework, explainable AI, and an operational routing policy designed for adoption by risk and compliance teams.

---

*Academic work on a public dataset ([IEEE-CIS Fraud Detection](https://www.kaggle.com/competitions/ieee-fraud-detection), Kaggle), not a production deployment. Cost parameters derived from published industry research rather than a specific organisation's figures. Transaction amounts in the dataset's native USD. Raw data is not redistributed in this repository.*

*MSc Business Analytics, Queen's University Belfast — Dissertation*
