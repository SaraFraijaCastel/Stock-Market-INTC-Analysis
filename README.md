# INTC Intraday Scalping — Volatility & Risk Analysis

Statistical analysis of an INTC intraday scalping strategy using **real personal
trade records** and **minute-level price data**, to test whether time-of-day
patterns offer a tradable edge and to define data-driven take-profit (TP) and
stop-loss (SL) levels.

---

## Question

Does INTC show time-of-day windows with a reliable directional edge during the
first trading hour, and can excursion metrics (MFE / MAE) define better TP/SL
levels for a scalping strategy?

## Data

- **Personal trade records** — Webull order export (`data/raw/Webull_Orders_Records.csv`),
  used to reconstruct trade durations and realized per-share price changes.
- **Minute OHLCV for INTC** — downloaded once from the Polygon API for
  Mar–Sep 2026 and saved to `data/raw/INTC_1min_mar_sep_2026.csv`. The notebooks
  read this CSV, so the analysis **reproduces without any API key**.

## Method

| Notebook | What it does |
|---|---|
| `01_exploration.ipynb` | Reconstructs trade durations and per-share price changes from the order export; confirms duration doesn't drive realized change (consistent with a fixed profit target). |
| `02_volatility_analysis_1_month.ipynb` | Probability and magnitude of up/down moves in 5-minute windows, 09:30–11:00 ET, over one month. |
| `03_volatility_analysis_several_months.ipynb` | Repeats the analysis over six months, computes an expected value per window, and backtests a TP/SL rule using MFE (max favorable excursion) and MAE (max adverse excursion). |

Expected value per window is defined as:

```
EV = P(up) · median_up − (1 − P(up)) · median_down
```

## Key finding

⚠️ **The apparent edge did not survive a larger sample.**
On one month of data the 10:00–10:05 ET window showed ~76% probability of an
upward move. Expanding the sample to six months **substantially reduced that
edge** (probability of an up-move fell to roughly 0.44–0.58 across windows) — a
reminder that short samples overfit and that robustness checks must precede any
trading rule. This out-of-sample collapse is the most important result of the
project.

### Backtest (10:00–10:15 ET window, six months, 128 trading days)

Fixed thresholds: **TP = 0.36 USD**, **SL = 2.40 USD**, on a notional of $10,000
per trade. The SL was sized from the distribution of MAE among winning trades
(keeping 95% of winners required an SL of ~2.29 USD).

| Outcome | Count |
|---|---|
| Win (MFE ≥ TP) | 79 |
| No trigger | 45 |
| Loss (MAE > SL) | 4 |

- **Win rate:** ~62%
- **Net PnL:** ≈ **+$5,150** over the six-month window (mean ≈ $40/day)

> **Limitation (important):** this is an optimistic, first-order backtest. It
> checks whether MFE exceeded TP and whether MAE exceeded SL **independently
> within the window**, without modeling which was hit first intraday, and it
> ignores commissions and slippage. The numbers illustrate the framework, not a
> deployable edge.

## What I'd do next

- Model intrabar order (which of TP/SL is hit first) instead of independent MFE/MAE checks.
- Add transaction costs and slippage.
- Size TP/SL from ATR rather than fixed thresholds.
- Test across more tickers and market regimes to check generality.

## Repository structure

```
data/
  raw/            # Webull order export + INTC 1-min OHLCV CSV
  processed/
notebooks/
  01_exploration.ipynb
  02_volatility_analysis_1_month.ipynb
  03_volatility_analysis_several_months.ipynb
docs/
  analysis_log.md # running research notes
results/
src/
requirements.txt
```

## Reproduce

```bash
pip install -r requirements.txt
jupyter lab   # run notebooks 01 -> 02 -> 03 in order
```

All analysis reads the committed CSV files, so no API key or network access is
required.

---

*Tools: Python, pandas, NumPy, matplotlib, seaborn, yfinance, Polygon API.*
