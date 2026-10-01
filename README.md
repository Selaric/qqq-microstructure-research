# QQQ Two-Day Microstructure Research

Exploratory research into whether the relationship between consecutive QQQ daily highs and lows contains information about next-day price behavior, and whether any observed relationship can be used before the next session closes.

This is a research notebook, not a trading system. It does not implement a strategy backtest, model transaction costs, or establish that any reported relationship is profitable.

## Research Question

The notebook studies two range-normalized measures of how the next day's price extremes cross the prior day's range:

- `dhl_norm = (high[t] - low[t+1]) / range[t]`
- `dlh_norm = (high[t+1] - low[t]) / range[t]`

It compares next-day candle direction, range expansion, wick structure, and opening-gap direction, with results grouped by VIX regime: Low (<15), Mid (15-25), and High (>25).

## Project Contents

- `research_assistant.ipynb`: QuantConnect Research notebook containing data retrieval, feature construction, exploratory statistics, classification experiments, conditional-probability analyses, and an intraday checkpoint study.

## Data and Environment

The notebook uses QuantConnect's `QuantBook` API and therefore must be run in a QuantConnect Research environment with access to QQQ, VIX, and minute-resolution QQQ history. It is not a standalone notebook that can run with only a local market-data CSV.

The analysis requests daily QQQ and VIX data from January 2006 through September 30, 2026, and minute QQQ data from October 2024 through September 29, 2026. The notebook also uses the following Python libraries:

- pandas
- NumPy
- Matplotlib
- scikit-learn
- statsmodels
- SciPy

QuantConnect provides the `QuantBook`, `Resolution`, and data-access interfaces used in the notebook. Confirm the selected Research environment has compatible versions of the listed scientific Python packages.

## Notebook Workflow

1. Retrieve and align daily QQQ OHLCV and VIX data by trading date.
2. Construct invasion measures, next-day outcomes, volatility regimes, and lagged candidate features.
3. Summarize and visualize the resulting time series.
4. Select candidate features and compare five classifiers using expanding-window walk-forward splits.
5. Examine conditional probabilities and logistic relationships by VIX regime and time segment.
6. Explore first-touch and fixed-time intraday signals using minute bars.

## Findings Recorded in the Notebook

The notebook's narrative reports that lagged, close-of-day features did not beat the majority-class baseline, while contemporaneous daily invasion measures were strongly associated with next-day candle structure. Its later intraday analysis reports a relationship between the running invasion measure at fixed afternoon checkpoints and the eventual candle direction.

These are notebook-authored claims, not independently verified results: **none of the notebook cells have been executed, and no cell outputs are stored in the file.** Re-run the analysis and validate the calculations before relying on any figures or conclusions.

## Review Notes and Limitations

- **Walk-forward results are not fully out-of-sample.** Correlations and decision-tree importances are calculated using the complete modeling frame before the walk-forward splits. The test periods therefore influence feature selection. Perform feature selection independently within each training window, or predefine features using a separate training period, before interpreting reported test accuracy.
- **Intraday thresholds use full-history data.** The notebook applies quintile cut points estimated from the full daily sample to the more recent minute-bar study. Re-estimate thresholds using data available before each evaluation period to avoid look-ahead.
- **Conclusions are unverified.** The notebook includes prose with numerical findings, but its code cells have not been run and the file contains no outputs to reproduce those numbers.
- **No trading performance is established.** There is no portfolio simulation, execution model, transaction-cost estimate, or risk-adjusted performance analysis.
- **Data access and date coverage matter.** Results depend on QuantConnect's history availability, timestamps, symbol settings, and the actual current data coverage in the Research environment. Confirm returned date ranges rather than assuming the requested end date is fully available.
- **The final cell adds SPY without further analysis.** It appears to be a leftover setup cell and is not part of the documented QQQ/VIX workflow.

## Suggested Validation Before Further Use

1. Run the notebook from top to bottom in QuantConnect Research and inspect each data pull, assertion, model fit, and chart.
2. Fix feature-selection leakage by fitting selection only on each training window, then rerun all walk-forward evaluations.
3. Use time-ordered, training-only threshold estimation for the intraday analysis and report sample sizes and uncertainty for each bucket.
4. Keep any strategy claims provisional until a separate, leakage-aware backtest includes realistic execution, costs, and risk controls.