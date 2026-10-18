# SPX 15-Minute Prediction Lab — v1 prototype

A mobile-friendly static research app for inspecting 5-minute OHLC candles, generating an illustrative 15-minute forecast, and running a walk-forward historical test. It runs locally in the browser and can be hosted as a GitHub Pages static site.

## Important limitations

- No live SPX feed, live news, options chain, implied volatility, Greeks, or broker integration is connected.
- Forecast mix percentages are heuristic and uncalibrated. Do not read them as proven probabilities or trading advice.
- Synthetic demo candles are computer-generated test data, **not real SPX prices**. They are only for checking that the UI and workflow function.
- The first pass is rule-based, not trained machine learning. Backtest results are only meaningful with real, clean, correctly timestamped data.
- Index direction does not equal SPX option profitability. A proper options backtest needs historical option contract quotes, expiry, strike, bid/ask, Greeks/IV, and realistic fees/slippage.
- Don't put API keys in `index.html` or any public GitHub repository. A secure backend/serverless function is needed for feeds that require keys.

## Use it

1. Open `index.html` in a browser, or publish the folder using GitHub Pages.
2. Use **Open synthetic demo** only to explore the interface. The data is explicitly labeled synthetic.
3. To test real market behaviour, import a CSV of real 5-minute bars with these columns:

   `timestamp,open,high,low,close,volume`

   The timestamp should include date and time. ISO timestamps such as `2026-10-01T09:30:00-04:00` are preferred. Bars should be in ascending or descending order (the app sorts them), use a consistent 5-minute interval, and cover multiple sessions/market conditions.
4. Run the walk-forward test. Each forecast uses only bars through that timestamp and compares with the close three bars later. Windows with session/time gaps are skipped.
5. Export the forecast log for further review.

## Algorithms in v1

- short-horizon momentum
- fast/slow EMA trend
- mean reversion from a rolling mean
- position in the prior trading range
- candle body pressure
- heuristic consensus
- simple momentum-persistence baseline

The default weights are experimental. They should not be tuned and evaluated on the same period. Use a chronological development/test split, report performance by session/regime, compare against simple baselines, and test more than one sideways threshold.

## News notebook

The UI includes a local event notebook, but it does **not** automatically fetch news or inject hand-entered sentiment into the model. For a proper later test, headlines/events need timestamps, source, publication/release time, historical availability, and a defined feature method so no future information leaks into the model.

## Data choices for a later live version

Before choosing a provider, decide whether the intended instrument is Cboe SPX index options, SPY ETF options, or a broker's `SPX500`/`US500` CFD. These are not interchangeable, and the symbol affects data availability and options logic. Provider pricing, licensing, delays, redistribution limits and historical depth must be checked directly.

Useful provider documentation to investigate (availability/terms can change):
- Alpha Vantage intraday API: https://www.alphavantage.co/documentation/
- Financial Modeling Prep API documentation: https://site.financialmodelingprep.com/developer/docs
- Cboe US options market data: https://www.cboe.com/data/market-data-services/us/options/
- Cboe Option Trades historical data: https://datashop.cboe.com/option-trades

## GitHub Pages deployment

1. Create a new GitHub account (or use a new account you control), then create a **public** repository such as `spx-15-minute-lab`.
2. Upload `index.html` and `README.md` to the repository's root (not inside another nested folder).
3. Open **Settings → Pages**. Under build/deployment choose **Deploy from a branch**, select `main` and `/ (root)`, then save.
4. Wait for GitHub Pages to publish the site. The URL will be shown in the Pages section.
5. This static version needs no API credentials. Do not commit secrets later; live data should be connected through a secure backend.

## Suggested next steps

1. Confirm instrument (`SPX` index options vs `SPY` options vs broker `SPX500`).
2. Obtain a small real 5-minute historical dataset from an acceptable data source and run the initial test.
3. Save every forecast timestamp and score; reserve later chronological dates as an untouched test set.
4. Add historical event/news data and test whether it improves out-of-sample results.
5. Only then add a secured data backend, refresh logic, option-chain display and options-specific paper trading.
