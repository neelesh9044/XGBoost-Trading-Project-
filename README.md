# XGBoost Cross-Sectional Equity Alpha Strategy

An XGBoost model in R that predicts stock-level excess return against the market
average, turned into a long-only strategy and backtested out-of-sample on
2018–2025 with transaction costs applied.

## Target and features

The model predicts **excess return**, defined as `fwd_return - mkt_avg.ret` — that
is, return relative to the cross-sectional market average rather than raw return.

| Feature | Type |
|---|---|
| Profit margin | Fundamental |
| 6-month momentum (rolling return) | Price |
| 12-month momentum (rolling return) | Price |
| Sector classification | Categorical |
| Inflation | Macro |
| `quality_signal` | Composite of profitability and momentum |

Stocks are retained only if they have at least 120 months (10 years) of history.

## Model

Gradient-boosted trees via `xgboost` in the `tidymodels` framework:

| Hyperparameter | Value |
|---|---|
| Trees | 150 |
| Max tree depth | 6 |
| Min node size | 10 |
| Learning rate | 0.05 |

**Train / test split:** trained on data before 1 January 2018, tested on
1 January 2018 through 2025. The test window is a genuine out-of-sample holdout,
not a random split.

## Strategy

1. Rank all eligible stocks by predicted alpha.
2. Go long the top 5%.
3. Macro overlay: if inflation exceeds 4%, hold 80% equity and 20% cash.
4. Deduct 10 bps from returns as transaction costs.

## Results

| | Strategy | Market |
|---|---|---|
| Annualised return | **18.05%** | 11.91% |
| Sharpe ratio | 0.4601 | 0.4700 |
| Beta | 1.603 | 1.00 |
| Max drawdown | −35.87% | — |

## How to read these numbers

The headline 18.05% versus the market's 11.91% is **not evidence of alpha.** The
strategy runs a beta of 1.603, and its Sharpe ratio (0.4601) is marginally *below*
the market's (0.4700). In other words, the excess return is largely compensation
for taking roughly 60% more market risk, and on a risk-adjusted basis the strategy
does not beat a passive benchmark over this period. The −35.87% maximum drawdown
is consistent with that leverage-like exposure.

What the project does demonstrate is the full modelling workflow — feature
construction, a clean out-of-sample split, cost-aware backtesting, and honest
performance attribution.

Natural extensions would be beta-neutralising the portfolio, adding a short leg,
constraining sector exposure, and testing whether the signal survives once market
exposure is hedged out.

## Files

| File | What it is |
|---|---|
| `xgboost_sp500_alpha.Rmd` | Full R Markdown source |
| `xgboost_sp500_alpha.html` | Knitted report — open this to read the analysis |
| `spx_macro.RData` | Dataset |

## Stack

R, `tidyverse`, `tidymodels`, `xgboost`, `PerformanceAnalytics`, `lubridate`.

## Context

Individual project completed during the MSc in Finance (Quantitative and Financial
Markets track) at emlyon business school.
