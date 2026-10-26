# EVALUATION OF INSURANCE FRAUD ANALYSIS


## Summary

Fraud is rare, but it is very expensive. Fraudulent policies make up **4.8%** of the portfolio, yet they claim about **12 times as often** as genuine policies and produce a loss ratio of **602.5%** against 49.7% for genuine policies. The analysis found identifiable patterns (payment type, vehicle age, driver age, vehicle value, region), and a decision tree turned some of them into simple rules. The predictive models are not yet strong enough to flag fraud automatically, so the immediate value lies in **risk prioritisation, monitoring and process improvement**, with model improvement as the next phase.

---

## Financial impact

| Metric | Overall | Non-fraud | Fraud |
|---|---|---|---|
| Policies | 100,000 | 95,200 | 4,800 (4.8%) |
| Loss ratio | 77.2% | 49.7% | 602.5% |
| Claim frequency (claims per policy) | 0.080 | 0.052 | 0.621 |
| Average claim severity | £2,650 | £2,590 | £2,751 |
| Total incurred | £21.1m | £12.9m | £8.2m |

- Fraud policies account for about **39% of total incurred cost** while representing under 5% of policies.
- The gap between the two groups is driven mainly by **how often** fraudulent policies claim, not by how much each claim costs (severity is only about 6% higher).

## Operational impact

| Policy year | Investigations | Confirmed frauds | Conversion rate |
|---|---|---|---|
| 2018 | 3,103 | 2,025 | 65.3% |
| 2019 | 3,033 | 2,305 | 76.0% |
| 2020 (to March) | 439 | 470 | 107.1% |
| **Overall** | **6,575** | **4,800** | **73.0%** |

- Investigation quality appears to have improved from 2018 to 2019, with fewer false positives.
- About 27% of investigations overall did not end in confirmed fraud, which is a direct operational cost.

## Performance Analysis

### Trends

- The monthly fraud rate declined over the period. An OLS fit on the decomposed trend gives a slope of about **−0.05 percentage points per month**.
- Seasonal decomposition (Apr 2018 – Mar 2020, 12-month period) separates trend, seasonality and noise so unusual spikes can be spotted.

### Fraud indicators

- **Main predictors (SHAP and tree importances):** payment type (direct debit), vehicle age, driver age, vehicle value, and region (South East and North West).
- Fraud was more associated with **direct debit payments, higher vehicle values and certain driver age groups**.
- The decision tree generated readable IF/THEN rules combining payment type, vehicle age, driver age, premium and mileage.

### Model performance (fraud class, 30,000-policy test set)

| Model | Precision | Recall | F1 | Accuracy |
|---|---|---|---|---|
| Decision Tree (depth 5) | 0.27 | 0.17 | 0.21 | 0.94 |
| XGBoost + SMOTENC | 0.20 | 0.07 | 0.11 | 0.94 |

---

### Strengths

- **Careful data preparation.** Mixed date formats and Excel serials were handled, raw columns were kept for audit, and implausible dates were nulled.
- **Right KPIs for the question.** Loss ratio, claim frequency, claim severity and conversion rate link fraud to both financial and operational impact.
- **Exposure and risk shown together.** Dual-axis charts show policy volume and fraud rate side by side, so small high-risk segments are not hidden behind large low-risk ones.
- **Sound handling of class imbalance.** Oversampling was applied to the training set only, after a stratified split, so there is no leakage of synthetic samples into the test set.
- **Interpretability.** Decision tree rules and SHAP give explanations the business can discuss, not just a score.

### Weaknesses 

| Area | Issue | Why it matters |
|---|---|---|
| **Model recall** | Fraud recall is 17% (tree) and 7% (XGBoost) | Most fraud is missed. The 94% accuracy mostly reflects the 95% non-fraud base rate, so it says little about fraud detection. |
| **Conversion rate above 100%** | 2020 shows 107%, meaning some fraud-flagged policies were never flagged for investigation | The flags are not nested as assumed. Conversion and false-positive rates for 2020 are not reliable. |
| **Partial final year** | 2020 covers policies only up to March | Year-on-year comparisons for 2020 are not like-for-like. |
| **Trend significance** | The OLS was fitted to a smoothed (decomposed) trend with only 24 points. R² of 0.999 and the very small p-value are inflated by smoothing and autocorrelation (Durbin-Watson ≈ 1.5) | The downward direction is plausible, but the strength of evidence is overstated. |
| **Loss ratio of 602.5%** | Based on only 4,800 policies, and fraud policies may carry lower earned premium if cancelled early | Treat it as a strong signal of severity, not a precise figure. |
| **Possible leakage** | `net_written_premium` and claim-related fields can be affected by the fraud outcome | Model performance may not hold in production if a feature is only known after fraud is detected. |
| **Narrow tree rules** | Some rules rely on very tight thresholds (e.g. a premium band of £128.30–£128.33) | These are likely overfitted and should not be used as business rules. Many rules also describe low-risk segments rather than high-risk ones. |
| **Association, not causation** | Payment type, age and region are correlates of fraud | Using them directly in decisions needs care, including fairness and regulatory review. |
| **Single split** | One 70/30 split, no cross-validation, fixed hyperparameters | Performance estimates may be unstable. |

---

## Recommendations

### Short term: use the existing insights

1. **Prioritise investigation by risk, not by rule alone.** Use the main predictors (direct debit payment, vehicle age, driver age, vehicle value, region) as inputs to a risk score that ranks policies for review. This directs investigator time to the segments most associated with fraud and helps reduce the roughly 27% of investigations that find no fraud.
2. **Reconcile the fraud and investigation flags.** Agree definitions with the business and fix the cases where fraud is flagged without an investigation. Until then, report conversion and false-positive rates with this caveat.
3. **Monitor a small set of KPIs monthly.** Track fraud rate, loss ratio (fraud vs non-fraud), claim frequency, conversion rate and false-positive rate, using the seasonal decomposition to separate normal seasonality from genuine spikes.
4. **Quantify the business case.** Combine cost per investigation, average fraud claim cost (about £2,750) and detection rate to estimate savings from reallocating investigation resources.

### Medium term: strengthen the model

5. **Improve fraud recall.** Tune the decision threshold using precision-recall curves, try class weights or cost-sensitive learning, and compare against the current oversampling approach.
6. **Validate properly.** Use cross-validation, report PR-AUC alongside precision and recall, and test on a time-based split (train on earlier months, test on later ones) to mimic real use.
7. **Add features.** Examples: gaps between licence test date, vehicle purchase date and policy start; sales channel (`business_provider`); and no-claims discount history.
8. **Audit features for leakage and fairness.** Remove anything only known after a fraud outcome, and review the use of age, gender and region with compliance.
9. **Replace overfitted rules.** Prune the tree or use a minimum leaf size so rules are stable and explainable, and keep SHAP for ongoing explanation.

### Longer term: prevention

10. **Pilot CoverCheck for ghost broking.** The proposal lets customers upload suspicious documents, uses OCR and text analysis to extract the content, checks it against Hastings records and the FCA register, and issues a warning.
    - **Benefits:** early scam detection, customer trust, safer renewals, and support for genuine brokers.
    - **Risks:** false positives, build and running costs, and lower uptake among less tech-savvy customers.
    - **Suggested approach:** start with a small pilot, measure false-positive rate and customer response, and keep a non-digital route available for customers who prefer it.

---

## Suggested Roadmap

| Phase | Action | Output |
|---|---|---|
| **Now** | Reconcile flags, set up monthly KPI monitoring, build a simple risk-ranking for investigators | Reliable KPIs and a prioritised investigation queue |
| **Next** | Threshold tuning, cross-validation, time-based testing, new features, leakage and fairness review | A model with measurably better fraud recall |
| **Later** | CoverCheck pilot and cost-benefit evaluation | Evidence on whether to scale prevention tools |

---
