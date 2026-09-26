# A/B Testing Toolkit: Experiment Design, Validity & Advanced Methods

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-stats-8CAAE6?logo=scipy&logoColor=white)
![statsmodels](https://img.shields.io/badge/statsmodels-inference-4051B5)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)

A hands-on collection of **simulation-based A/B testing methods**, implemented from scratch in Python. It covers the full experiment lifecycle:

- designing and sizing a test
- checking that the test is valid
- analysing difficult metrics
- controlling errors across many comparisons or repeated looks at the data
- estimating causal effects when randomisation isn't possible
- making tests faster with variance reduction

Every method is checked with **Monte Carlo simulation**. Each is run on thousands of synthetic A/A tests, where there is no effect, to confirm the false-positive rate, and on A/B tests to measure power.

---

## Notebooks

| # | Notebook | Topics | Key result |
|---|---|---|---|
| 01 | [Splitting, Monte Carlo & Bootstrap](notebooks/01_splitting_monte_carlo_bootstrap.ipynb) | Hash-based (salted MD5) splitter, A/A validation, simulated power, Monte Carlo sample size, bootstrap test | The hash splitter gives uniform A/A p-values (KS p = 0.51). Detecting a +14% relative change in churn needs about **5,100 users** |
| 02 | [SRM & Novelty Effect](notebooks/02_srm_and_novelty_effect.ipynb) | χ² SRM test, sequential SRM (`ssrm-test`), segmentation, LinkedIn novelty-effect detector | SRM detected (p = 4.5e-8); the **sequential test flags it on day 6–7**. The novelty detector separates the affected segment (R² = 0.92) from the unaffected one (R² = 0.09) |
| 03 | [Ratio Metrics (CTR)](notebooks/03_ratio_metrics.ipynb) | Poisson bootstrap, bucketization, delta method, linearization, smoothing | All methods keep the A/A false-positive rate at **≈ 5%**. The delta method, linearization and Poisson bootstrap give the highest power (≈ 21%) |
| 04 | [Multiple Comparisons & Sequential Testing](notebooks/04_multiple_testing_and_sequential.ipynb) | FWER, 5 correction methods, multi-arm sample size, peeking, SPRT, mSPRT | Three comparisons raise the false-positive rate from 4.7% to **13.5%**. Holm–Šidák and Hommel keep it at 3.8% with much more power than Bonferroni |
| 05 | [Causal Inference without A/B](notebooks/05_causal_inference_without_ab.ipynb) | Difference-in-Differences, matching and PSM, IPW, instrumental variables (DoubleML), CausalImpact | IV recovers the true effect (−2.49, 95% CI [−3.41, −1.57], true −2), where the naive comparison gets even the sign wrong (+0.62) |
| 06 | [Variance Reduction](notebooks/06_variance_reduction.ipynb) | Outliers and winsorization, stratification, CUPED, VWE | CUPED cuts variance by **85%** (ρ = 0.93) and VWE by **90%**, so the same effect is detectable with a fraction of the users |

## Highlights

### Test design & validity
- **Deterministic splitting.** `md5(user_id + salt) % 100` puts a user in the same group every time, needs no stored assignment table, and reshuffles everyone when the salt changes. It was validated with A/A simulations and a Kolmogorov–Smirnov uniformity test.
- **Power by simulation.** A known effect is injected into historical data, and the sample size is increased until the simulated power reaches 80%. This works for any metric or test, not only when textbook assumptions hold.
- **SRM monitoring.** A sequential SRM test gives always-valid p-values, so a broken split is caught **during** the experiment rather than after it ends.
- **Novelty and primacy effects.** Segmentation and a regression on decaying time features, 1/t^α and 1/t^γ, detect effects that change over the first days of a test.

### Metrics & statistical inference
- **Ratio metrics.** CTR randomised by user but measured on views breaks the independence assumption behind a standard t-test. The notebook compares five correct approaches on 10,000 simulated experiments:

  | Method | A/A false-positive rate | A/B power |
  |---|---|---|
  | Poisson bootstrap | 0.054 | 0.211 |
  | Bucketization, t-test | 0.053 | 0.204 |
  | Delta method | 0.053 | 0.210 |
  | Linearization, t-test | 0.053 | 0.209 |
  | Linearization, Mann–Whitney | 0.048 | 0.096 |

- **Multiple comparisons.** A/B/C/D test with three true effects:

  | Correction | A/A alpha level | Power (all 3 detected) |
  |---|---|---|
  | Bonferroni | 0.037 | 0.393 |
  | Šidák | 0.038 | 0.396 |
  | Holm–Šidák | 0.038 | 0.564 |
  | Simes–Hochberg | 0.038 | 0.588 |
  | Hommel | 0.038 | 0.588 |

- **Sequential testing.** The notebook shows how peeking inflates false positives, and implements Wald's SPRT for conversions and a mixture SPRT (mSPRT).

### Beyond randomised experiments
- **Difference-in-Differences:** the policy effect is +24.0 sales units (OLS, p < 0.001).
- **Matching, PSM and IPW:** the adjusted log-wage gap estimates are 0.28–0.42, compared with an unadjusted gap of 0.47.
- **Instrumental variables:** DoubleML's IIVM model removes the bias from a hidden confounder.
- **Synthetic control:** CausalImpact estimates a +13.3% effect on the BTC price during the event window, with a 95% interval of [10.9%, 15.8%].

### Faster experiments
| Technique | Variance vs original |
|---|---|
| Winsorization at the 95th percentile | 68% (and 47% if the top 5% is removed) |
| Stratification | 27% |
| CUPED (pre-period correlation 0.93) | 15% |
| Variance-Weighted Estimator | 10% |
| CUPED with an uncorrelated covariate | 100%, no gain |

## Repository structure

```
ab-testing-toolkit/
├── notebooks/
│   ├── 01_splitting_monte_carlo_bootstrap.ipynb
│   ├── 02_srm_and_novelty_effect.ipynb
│   ├── 03_ratio_metrics.ipynb
│   ├── 04_multiple_testing_and_sequential.ipynb
│   ├── 05_causal_inference_without_ab.ipynb
│   └── 06_variance_reduction.ipynb
├── requirements.txt
└── README.md
```

## Data

- **Notebook 01** uses the public [IBM Telco Customer Churn](https://github.com/IBM/telco-customer-churn-on-icp4d) dataset: 7,032 customers after dropping rows without billing history, with a churn rate of 26.6%.
- **Notebook 05** uses the `Wages` panel from the R package `plm` (downloaded through `statsmodels`) and Yahoo Finance prices (through `yfinance`).
- **All other notebooks** use simulated data with known properties, so each method can be checked against the ground truth.

## How to run

```bash
git clone https://github.com/annapmp/projects.git
cd projects/ab-testing-toolkit
pip install -r requirements.txt
jupyter notebook notebooks/
```

The notebooks were developed in Google Colab. All outputs are saved, so every result can be reviewed on GitHub without running anything.
