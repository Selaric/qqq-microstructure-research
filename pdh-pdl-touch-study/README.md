# PDH/PDL Touch Study: Research Report

## Executive Summary

This project investigates whether intraday touches of the prior day's high (PDH) or low (PDL) in SPY and QQQ lead to a tradable reversal. The notebook begins as a descriptive event study, then expands into simulations, out-of-sample checks, execution-timing tests, and a post-mortem.

The central conclusion recorded in the notebook is negative: an early simulator used each session's own daily high, low, and range as if they were prior-session levels. That look-ahead error invalidates the initial event-study and discovery results. The notebook's later narrative says corrected prior-day-level tests and real-time entry tests do not show a robust, cost-surviving fade or momentum strategy. **The strategy variants tested are rejected by the notebook's stated criteria; the underlying phenomenon remains unproven.**

This is a research report, not investment advice or a deployable trading strategy. The numerical findings below are transcribed from notebook prose, not independently reproduced: the supplied notebook has not been executed and contains no saved cell outputs.

## Study Scope

- **Markets:** SPY and QQQ, with VIX used for selected event filters.
- **Period requested:** 2021 through October 1, 2026, subject to QuantConnect data availability.
- **Data:** QuantConnect LEAN minute and daily bars; the notebook requests raw-normalized ETF prices for its intraday calculations.
- **Event:** first intraday touch of the previous session's high or low, followed by measuring excursion/retrace depth relative to the prior day's range.
- **Extensions:** time-of-day and VIX/volume conditioning, first-touch and re-arming variants, a 15-minute test, stop and holding-period comparisons, and a QQQ replication.

The notebook's scope grew beyond its original “measurement only, no trades” description. Later cells do simulate hypothetical trades and state performance claims; those claims require the limitations below to be resolved before they can be relied upon.

## Findings Reported in the Notebook

| Analysis | Notebook-reported result | Interpretation |
| --- | --- | --- |
| Context audit | The original simulator used day *d* OHLC as day *d*'s “prior-day” context instead of shifting levels from day *d-1*. | Initial Phase 0a/0b event-study and discovery findings are marked void in the notebook post-mortem. |
| Corrected full-session fade | Median gross return: SPY −7.8 bps; QQQ −16.3 bps. | Negative before transaction costs. |
| Real-time 15-minute test | Median gross fade: SPY −1.6 bps; QQQ −0.4 bps; both are negative after the stated 2 bps round-trip cost. | The pre-registered short-horizon fade test is reported as killed. The momentum mirror also fails the stated validation/cost hurdle. |
| Re-arming variant | The notebook's master record reports negative fade means and mirror results below 2 bps costs. | Re-arming did not rescue the tested rule. |
| LEAN backtest record | The notebook's master record reports −13.9% net, Sharpe −1.0, 15% win rate, and 17% maximum drawdown. | The referenced LEAN run artifacts are not included here, so these figures are not independently checked. |

These are claims recorded in markdown cells, not results reproduced from this copy. In particular, the report does not treat the notebook's earlier positive performance figures as valid: the notebook itself attributes them to same-day level leakage.

## Methodology and Review

The event engine retrieves daily context and minute bars, detects the first level touch, and records excursion depth, touch timing, persistence, volume, VIX changes, and subsequent returns. Later cells test signal thresholds and compare discovery-period choices with later validation years. The post-mortem correctly identifies the core temporal-alignment risk: a feature called “prior-day” must be formed from data available before the event day, not from that day's eventual high or low.

The key reproducibility checks are:

1. Verify every PDH, PDL, and range input is shifted from the prior completed session before event-day calculations.
2. Confirm entry prices are observable at the stated decision time; do not enter retrospectively at an intraday extreme.
3. Rebuild the corrected event set from raw bars, then reproduce each stated sample size and return statistic.
4. Reconcile the notebook simulation with the referenced LEAN trade log, including fills, stops, and costs.
5. Keep discovery choices separate from validation data and report uncertainty, not only medians or point estimates.

## Reproduction Requirements and Gaps

The notebook requires a QuantConnect Research environment with `QuantBook`, SPY/QQQ minute and daily history, VIX history, and the scientific Python stack used by the notebook (pandas, NumPy, and SciPy; statsmodels is optional in one diagnostic cell).

Two cited project artifacts were not present alongside the supplied notebook when this report was prepared:

- `scalp15_test.py`, imported for the 15-minute and re-arming tests.
- `RESEARCH_RECORD.md`, cited as the full narrative record.

The notebook also references LEAN run IDs, order-book details, and research cards that are not bundled here. Consequently, the referenced tests and final backtest metrics cannot be fully reproduced from this subproject alone. None of the notebook's 27 cells has a saved execution result.

## Project Files

- `pdh_pdl_touch_event_study.ipynb`: source notebook, copied without changing its cell content.
- `README.md`: this report, including the notebook's stated results and reproducibility caveats.

## Conclusion

Based on the notebook's own post-mortem, the apparent early edge was an artifact of look-ahead, and the corrected fade, short-horizon, and re-arming variants did not meet their stated performance thresholds. Do not use the voided early results to justify a strategy. Any further study should begin with strictly prior-session levels, realistic decision-time fills, a clean discovery/validation split, and reproducible trade-level cost accounting.
