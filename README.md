# Celebrity Board Governance as a Signal of Leverage Discipline in Cyclical Sectors

> **Evidence from Construction and Real Estate — Pre and Post-COVID**

[![Python 3.8+](https://img.shields.io/badge/python-3.8%2B-blue.svg)](https://www.python.org/)
[![Platform: Google Colab](https://img.shields.io/badge/platform-Google%20Colab-orange.svg)](https://colab.research.google.com/)

---

## Overview

This research notebook investigates whether firms in the **Construction and Real Estate (C+RE)** sectors whose boards exhibit high *Celebrity Governance Scores* (CGS) demonstrate measurably better **leverage discipline** and **risk-adjusted returns** compared to non-celebrity peers — and whether that relationship shifted after the COVID-19 shock of March 2020.

The study constructs a C+RE universe from Russell 3000 constituents, scores each firm on a composite three-dimension CGS, then applies a multi-method empirical battery: Fama-French three-factor regressions, Difference-in-Differences (DiD), Propensity Score Matching (PSM), and a leverage suppression-effect test. The key finding is that CGS functions as a **governance signal** of underlying leverage discipline — not a mediation chain — with the celebrity effect *strengthening* once leverage is controlled, confirming a suppression structure.

---

## Research Design

### Celebrity Governance Score (CGS)

Each firm is scored across three dimensions, ranked percentile-style within the universe:

| Dimension | Proxy | Interpretation |
|---|---|---|
| **Image** (`d_image`) | Market capitalization rank | Visibility and institutional prominence |
| **Influence** (`d_influence`) | 12-month momentum rank | Perceived performance leadership |
| **Sovereignty** (`d_sov`) | Revenue rank | Operational scale and market position |

`CGS = mean(d_image, d_influence, d_sov)`

Firms in the **top 25%** of CGS (default cutoff: 0.75) are classified as *Celebrity*; the remainder as *Non-Celebrity*.

### Study Windows

| Period | Dates |
|---|---|
| Pre-COVID | 2015-01-01 → 2020-02-28 |
| Post-COVID | 2020-03-01 → 2025-04-30 |

---

## Methodology

The notebook runs six empirical steps, each producing a saved output:

```
Step 1  →  Rebuild C+RE Universe
Step 2  →  Build 3-Dimension CGS
Step 3  →  Fetch Returns + Fama-French Factors
Step 4  →  Portfolio Construction & Window Splits
Steps 5–10  →  Six Tables + Three Figures
```

### Tables

| # | Method | Key Variables |
|---|---|---|
| **Table 1** | Descriptive statistics (Welch *t*-test) | Market cap, beta, D/E, current ratio, Debt/EBITDA, CGS |
| **Table 2** | Fama-French 3-factor regression (HAC SEs) | Alpha, market beta, SMB, HML, Sharpe — 4 portfolio × period combinations |
| **Table 3** | Difference-in-Differences (cluster-robust SEs) | Treat × Post coefficient; basic model and FF3-controlled model |
| **Table 4** | Propensity Score Matching (1:1 nearest-neighbor) | Matched post-COVID cumulative return and FF3 alpha |
| **Table 5** | Leverage signal / suppression-effect analysis | Paths a, b, c, c′; Sobel test; suppression confirmation |
| **Table 6** | Robustness checks | Alternative CGS cutoffs (70%, 80%), placebo time splits (2018, 2019) |

### Figures

| # | Content |
|---|---|
| **Figure 1** | Cumulative returns — Celebrity vs. Non-Celebrity vs. S&P 500 (2015–2025) with COVID-19 shock marker |
| **Figure 2** | CGS score distribution by celebrity classification |
| **Figure 3** | Leverage distributions — Panel A: Debt/Equity, Panel B: Debt/EBITDA (Welch *p*-values from raw data; 95th-percentile cap for display only) |

---

## Repository Structure

```
.
├── cyclical_sector_celeb_bod_indictor.ipynb   # Main research notebook
├── README.md
└── outputs/                                   # Generated on run (Google Drive)
    ├── cre_universe.csv
    ├── cre_cgs.csv
    ├── table1_descriptive.csv
    ├── table2_ff3.csv
    ├── table3_did.csv
    ├── table4_psm.csv
    ├── table5_leverage_signal.csv
    ├── table6_robustness.csv
    ├── figure1_cumulative_returns.png
    ├── figure2_cgs_distribution.png
    └── figure3_leverage_distribution.png
```

---

## Requirements

```bash
pip install yfinance pandas numpy statsmodels scikit-learn scipy matplotlib pandas-datareader
```

| Package | Purpose |
|---|---|
| `yfinance` | Price history, fundamental data, ETF holdings |
| `pandas_datareader` | Fama-French factor download (Ken French data library) |
| `statsmodels` | OLS, FF3 regressions, DiD with clustered SEs |
| `scikit-learn` | Logistic regression (propensity scores), nearest-neighbor matching |
| `scipy` | Welch *t*-tests, Sobel test |
| `matplotlib` | All figures |

> **Platform:** Designed for **Google Colab** with Google Drive mounted at `/content/drive/MyDrive/Russell3000_Diagnostic/`. Adapt the `DRIVE_DIR` / `OUT` paths to run locally.

---

## Quick Start

1. **Open in Colab** — upload or link the notebook, then mount your Drive:
   ```python
   from google.colab import drive
   drive.mount("/content/drive")
   ```

2. **Set output path** — edit `DRIVE_DIR` in the first cell if needed:
   ```python
   DRIVE_DIR = "/content/drive/MyDrive/Russell3000_Diagnostic"
   ```

3. **Run all cells** — the notebook is sequential; all steps must run in order for the panel dataset and portfolio returns to be available downstream.

4. **Standalone figure regeneration** — the final cells are self-contained and can be re-run independently to regenerate corrected Figure 1 (S&P 500 legend fix) and Figure 3 (Welch *p*-values from raw, uncapped data) without re-executing the full pipeline.

---

## Key Parameters

```python
PRE_START  = "2015-01-01"
PRE_END    = "2020-02-28"
POST_START = "2020-03-01"
POST_END   = "2025-04-30"
CGS_CUTOFF = 0.75          # Top 25% → Celebrity classification
MIN_MCAP   = 100e6         # $100M minimum market cap filter
```

---

## Interpretation Notes

- **Suppression, not mediation.** Table 5 tests whether celebrity governance *mediates* returns through leverage reduction. It does not. When leverage is controlled, the celebrity coefficient *increases* — a textbook suppression effect. This means CGS and low leverage are **independent predictors** of post-COVID returns; CGS signals governance quality, which in turn is correlated with but not causally upstream of leverage discipline.
- **Sobel test non-significance** confirms the absence of a mediation chain and is consistent with suppression.
- **Propensity score matching** controls for pre-existing differences in size, beta, and leverage structure, isolating the governance signal effect.
- **Placebo splits** (Table 6) use 2018 and 2019 as alternative treatment thresholds to verify the pre-COVID period does not spuriously generate the same effect.

---

## Data Sources

| Source | Data |
|---|---|
| Yahoo Finance (`yfinance`) | Equity prices, market cap, D/E, beta, EBITDA, total debt |
| Sector ETFs (ITB, XHB, PKB, VNQ, IYR, XLRE, REM, REZ) | Initial universe construction |
| Kenneth French Data Library | Fama-French 3-factor monthly returns (Mkt-RF, SMB, HML, RF) |
