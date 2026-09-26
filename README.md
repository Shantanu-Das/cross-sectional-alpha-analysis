# Cross-Sectional Signal Analysis

## Overview
Study of whether simple, interpretable price and volume characteristics predict the cross-section of
next-month US equity returns, and whether combining them adds anything beyond the best single signal.
Nine candidate signals are tested with rank information coefficients (IC), then combined with penalised
linear models under a strictly chronological train/validation/test split, and finally turned into a
beta-neutral long-short portfolio to check economic relevance.

## Data
Daily adjusted prices, closes, and volume from Yahoo Finance (via `yfinance`) for the current
S&P 500, 400, and 600 constituents (S&P 1500), plus SPY as the market, from 2004 to 2026. After a
liquidity screen and cleaning the panel is about 298,000 stock-months across roughly 1,500 tickers.
The universe is today's membership, so the sample is subject to survivorship bias (see Limitations).

## Methodology
- Vendor-error cleaning of adjusted/unadjusted price jumps, rebuilt into a clean total-return index.
- Point-in-time liquidity universe (price, dollar volume, history filters).
- Nine signals: multi-horizon momentum, short-term reversal, realised volatility, volume surprise,
  price acceleration, distance from a moving average, and beta-adjusted momentum.
- Cross-sectional winsorisation and standardisation each month.
- Rank IC at 1, 3, and 6 month horizons with Newey-West (HAC) t-statistics.
- Ridge / Lasso / Elastic Net fitted on 2005-2015, tuned on 2016-2019, tested on 2020-2026, compared
  against single signals, an equal-weight combination, and a naive baseline; plus an expanding
  walk-forward.
- Beta-neutral decile long-short portfolio with turnover and transaction-cost sensitivity.

## Results
- Realised volatility is the only signal with a robustly non-zero mean rank IC on beta-adjusted
  returns (about -0.03 at one month, Newey-West t around -2.8); lower-volatility names earn higher
  beta-adjusted returns.
- Short-term reversal is weakly positive in-sample and does not persist into the 2020-2026 test.
- Momentum variants are not distinguishable from zero over the full sample.
- On the 2020-2026 test the models, the equal-weight combination, and the single signals are all
  statistically indistinguishable from zero, so the combination does not beat its best component
  out of sample.
- The beta-neutral long-short portfolios do not produce a positive Sharpe ratio net of a 25 bps
  per-unit-turnover cost.

Full numbers, tables, and figures are in the executed notebook and under `figures/`.

## Reproducibility
1. Python 3.11 or newer is recommended; the notebook was developed and run on Python 3.14.
2. Create an environment and install dependencies:
   ```
   python -m venv .venv
   .venv\Scripts\activate      # Windows
   source .venv/bin/activate   # macOS / Linux
   pip install -r requirements.txt
   ```
3. Launch Jupyter from the repository root:
   ```
   jupyter notebook
   ```
4. Open `cross_sectional_signal_analysis.ipynb` and run all cells.

## Limitations
- Survivorship bias: the universe is the current S&P 1500, so delisted and acquired firms are absent
  and there are no delisting returns. This flatters contrarian signals and penalises momentum.
- Free vendor data has residual adjustment errors below the cleaning thresholds; volume is split- but
  not dividend-adjusted.
- No fundamentals, market cap, or point-in-time sector data, so nothing is sector-neutral.
- Beta is estimated from daily returns and is noisy; the backtest ignores intra-month drift, borrow
  costs, and market impact beyond the flat turnover cost.
- One 21-year sample with a single test window opening at the 2020 crash; some tuning choices used
  the validation period.
