Understand how a major financial index behaves over time, test whether it can be forecast reliably, and report forecasts with honest uncertainty. The notebook compares an ARIMA model to a naive baseline so you can judge whether the added complexity is worth it.

Data
Item	Detail
Source	Yahoo Finance via the yfinance library
Ticker	^GSPC (S&P 500)
Period	2013-01-01 to 2022-12-31 (the end date is exclusive, so the last row is the final trading day of 2022)
Series analyzed	Daily Close price, aligned to a business-day calendar with holidays forward-filled

Data is downloaded when the notebook runs, so an internet connection is required. To use a different index or period, change the ticker and dates in the download cell.

Requirements
Python 3.9 or newer
Packages:
bash
pip install yfinance statsmodels seaborn matplotlib pandas numpy
How to run
Install the packages above.
Open the notebook in Jupyter, VS Code, or Google Colab.
Choose Run All. The ARIMA order search fits 16 models and may take about a minute.
Notebook structure
Section	What it does
1. Setup and data loading	Imports, download, summary statistics, missing value check, price plot
2. Trend	50-day and 200-day moving averages
3. Log returns and volatility	Daily log returns, 21-day rolling annualized volatility
4. Stationarity tests	Augmented Dickey-Fuller tests on price, log price, and log returns
5. Decomposition	Additive trend, seasonal, and residual components of log price (period 252)
6. ACF and PACF	Autocorrelation of returns and of squared returns (volatility clustering)
7. Train/test split	Chronological split: train before 2022, test on 2022
8. ARIMA order selection	Grid search over p and q from 0 to 3 with d=1, ranked by AIC
9. Fit and forecast	Best model forecasts the 2022 holdout with a 95% interval, plotted with a naive baseline
10. Evaluation	MAE, RMSE, and MAPE for ARIMA vs naive, plus 95% interval coverage
11. Residual diagnostics	Residual plots, Ljung-Box tests on residuals and squared residuals
12. Findings and recommendations	Editable summary of conclusions
Method notes
Log transform: the model is fit on log prices so that changes are proportional, and forecasts are converted back with exp.
Differencing (d=1): prices are expected to be non-stationary, and differencing once is the standard fix. The ADF section lets you confirm this on your data.
Chronological split: time series cannot be shuffled. The final year is held out so the model is tested on data it has not seen.
Naive baseline: a flat forecast at the last training value. Markets are close to a random walk, so this baseline is hard to beat and is the honest benchmark.
What to look for in the results

These are typical patterns for equity indexes, not guaranteed outputs. Check them against your own run.

Prices and log prices usually fail the stationarity test, while log returns pass.
Returns show little linear autocorrelation, while squared returns show strong autocorrelation (volatility clustering).
ARIMA and the naive baseline often have similar errors.
2022 was a market decline that followed a long uptrend, so a trend-based model is likely to over-forecast, and a 95% interval may cover well under 95% of actual values.
Significant autocorrelation in squared residuals indicates the constant-variance assumption does not hold.
Limitations
ARIMA models the average level and cannot predict regime changes or sudden shocks.
Prediction intervals assume constant variance, which financial returns violate, so they are likely too narrow.
A single train/test split gives one noisy estimate of accuracy.
Results depend on the downloaded data, and Yahoo Finance may revise or change its data and format.
This analysis is for learning and planning support and is not investment advice.
Troubleshooting
Problem	Fix
ValueError or odd shapes with sp500['Close']	Newer yfinance versions return MultiIndex columns. The notebook flattens them in the download cell, so make sure that cell ran.
Empty DataFrame from yf.download	Check your internet connection, upgrade with pip install -U yfinance, and retry. Yahoo sometimes rate-limits requests.
seasonal_decompose error	The series needs at least two full periods (504 observations). Keep the date range at 2+ years or lower the period value.
ARIMA convergence warnings	These are suppressed by default. If results look unstable, try a smaller order grid.
Slow order search	Reduce the range(0, 4) loops to range(0, 3).
