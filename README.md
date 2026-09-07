# Turning Churn Data into Retained Revenue

Predictive-targeting proposal for Company A, a wireless telecom operator (~100k customers).
Built for GCI World 2026, final assignment.

**Author:** Ishraq Kamal Adib

## Summary

Company A spreads its retention budget evenly across the customer base and has no way to
rank customers by churn risk in advance. This project builds a monthly churn-risk scoring
model and proposes routing retention spend to the top decile of predicted risk instead of
the whole base.

| Metric | Value |
|---|---|
| Model | XGBoost |
| ROC-AUC (test) | 0.691 |
| Top-decile lift | 1.60× |
| Top-decile capture of next-month churners | ~16% (~30% at top 20%) |
| Net value, one targeting cycle (conservative case) | +$333K |
| Value vs. spending the same budget untargeted | ~5.6× |

Full writeup and figures: `reports/Retained_Revenue_Proposal.pdf`.

## Approach

1. Understand the market and data
2. Form a churn hypothesis (three actionable levers: handset/contract lifecycle,
   usage disengagement, service friction)
3. Build and compare ML models (logistic regression, random forest, HistGradientBoosting,
   XGBoost)
4. Translate the model into a targeting proposal
5. Verify impact and limitations (sensitivity analysis, calibration, causal caveats)

## Data

Dataset: Cell2Cell telecom churn data, provided as "Company A" course material
(100,000 customers, ~100 features: usage, billing, handset, tenure + appended demographics).
The sample is oversampled to ~49.6% churners by design; true monthly churn is ~1–2%.
All revenue figures in the report use the corrected base rate, not the sample rate.

**Raw data (`Client.csv`, `Record.csv`) is not included in this repo** — it is course-provided
and not licensed for redistribution. To reproduce, place both files in `data/` (see
`notebooks/churn_analysis.ipynb` for the expected path) and update `DATA_DIR` in the
first code cell.

## Repo structure

## Setup

```bash
python -m venv .venv
source .venv/bin/activate        # WSL2/Linux/macOS
pip install -r requirements.txt
jupyter notebook notebooks/churn_analysis.ipynb
```

## Key limitations

- AUC ≈ 0.69 reflects low-signal customer data; value comes from reliable ranking, not
  near-perfect classification.
- Save rate and cost-per-contact are assumptions (no public benchmark) — see the
  sensitivity analysis in the report.
- Model lift is correlational; a randomized control holdout is needed to measure true
  incremental retention before committing budget.

## References

See `reports/Retained_Revenue_Proposal.pdf`, page 13, for full sourcing (T-Mobile, Verizon,
AT&T churn disclosures, Bain/HBR retention-economics research, scikit-learn, XGBoost).
