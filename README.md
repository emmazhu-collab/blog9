# Blog 9: Predicting Stock Returns with Gradient Boosting

This project contains the code, saved market data, results, and figures for Blog 9. The article source remains in `website-recovered/blog/posts/post9/index.qmd`, following the same separation used for the earlier blog projects.

The analysis compares a classroom-style XGBoost baseline, a shallower XGBoost model, and a zero-return forecast. It also compares top-10 portfolios from both XGBoost models with passive SPY.

## Project files

```text
blog9/
├── blog9.Rproj
├── README.md
├── .gitignore
├── code/
│   ├── Blog9_StockPrediction.R
│   └── reference/
│       └── UPenn_XGBoostStockPrediction.R
├── data/source/
│   ├── yahoo_prices.csv
│   └── SOURCE.txt
└── results/
    ├── figures/
    │   ├── portfolio_growth.png
    │   └── prediction_errors.png
    ├── tables/
    │   ├── data_coverage.csv
    │   ├── investment_metrics.csv
    │   ├── model_settings.csv
    │   ├── portfolio_holdings.csv
    │   ├── portfolio_returns.csv
    │   ├── prediction_metrics.csv
    │   ├── sample_counts.csv
    │   ├── study_window.csv
    │   └── test_predictions.csv
    └── session-info.txt
```

`code/reference/UPenn_XGBoostStockPrediction.R` is a copy of the professor's original classroom script, retained for reference. The article is not duplicated in this project; its current source is in the website project.

## Data and method

The analysis uses daily prices for a 95-company stock universe based on the classroom experiment and for SPY. The saved snapshot in `data/source/yahoo_prices.csv` was downloaded from Yahoo Finance through `tidyquant`; `SOURCE.txt` records the source, date, and requested time window. Keeping this snapshot makes the analysis reproducible without downloading data again. A fresh download can differ if the provider revises historical prices. The saved adjusted prices are not a point-in-time archive.

The R script prepares market predictors, fits both models, evaluates predictions on the same later test observations, builds the portfolios, and saves tables and figures in `results/`. The baseline reproduces the classroom model's main settings as a new implementation; it is not guaranteed to match every default of the original `parsnip` workflow. The improvement changes tree depth from 5 to 2 and minimum child weight from 10 to 50.

The target is adjusted price at the close 20 trading days after the signal divided by adjusted price at the next trading day's close, minus one. That is a 19-trading-day holding interval. Training rows whose target period crosses into the test period are removed. Portfolio holdings use adjacent, non-overlapping intervals, and SPY uses the same entry and exit dates.

## Run the analysis

Open `blog9.Rproj` in RStudio. Install the required packages once in the R Console:

```r
install.packages(c("tidyverse", "tidyquant", "slider", "xgboost"))
```

From this project folder, run either:

```r
source("code/Blog9_StockPrediction.R")
```

or in a Terminal:

```sh
Rscript code/Blog9_StockPrediction.R
```

The script expects the project folder to be the working directory. With the included price snapshot, it needs no API key or internet access. It replaces the generated files under `results/`, but does not change the saved source data or the professor's reference script. If the snapshot is missing, it downloads prices from Yahoo Finance and records the source details.

The script does not render the article. The article reads the saved tables and figures; rendering does not fit the models or download prices.

## Results

`results/tables/prediction_metrics.csv` contains RMSE and MAE for both XGBoost models and the zero-return forecast, calculated on identical test rows. `investment_metrics.csv` contains cumulative returns and annualized Sharpe ratios. The remaining tables document data coverage, the train/test window, model settings, predictions, holdings, and holding-period returns. The two figures in `results/figures/` are the figures used by the article. Saved CSVs retain more precision than the rounded values reported in the article.

The zero-return forecast has the lowest prediction errors in the saved test results. The shallow model has the highest cumulative portfolio return, including after the assumed costs, while SPY has the higher annualized Sharpe ratio. The cost sensitivity assumes a 0.1% cost on each model-portfolio purchase and sale; it is not a measured execution cost. Sharpe ratios assume a zero risk-free rate.

## Limits and sources

The stock universe was not rebuilt using only companies an investor could have selected at each historical date, so survivorship bias may make the backtest look stronger. The results also cover one market period, and concentrated holdings and shared market shocks limit what the backtest can say about future investment performance.

- [Yahoo Finance](https://finance.yahoo.com/) — daily price data.
- [tidyquant `tq_get()` documentation](https://business-science.github.io/tidyquant/reference/tq_get.html) — R download interface.
- [parsnip XGBoost engine documentation](https://parsnip.tidymodels.org/reference/details_boost_tree_xgboost.html) — classroom parameter mapping.
