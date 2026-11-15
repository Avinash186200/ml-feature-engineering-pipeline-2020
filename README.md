# ML feature engineering pipeline (2020)

This is the data preparation side of my early trading ML work. It takes raw OHLC price bars for many currency
pairs and timeframes, builds a wide set of technical indicator features, joins them to the trade entries of a
rule-based strategy, and then prepares model-ready data: standardisation per segment, K-means clustering, time
features, and decision-tree feature importance to rank the variables. The output feeds the classifiers in my
[trading classifiers repo](https://github.com/Avinash186200/ml-trading-classifiers-2020).

**When:** built in 2020 (the first notebook is dated 16 Sep 2020; uploaded to GitHub in March 2022).

**Modern version:** the maintained version of this workflow is
[ml-model-building-workflow](https://github.com/Avinash186200/ml-model-building-workflow).

## Problem

A model is only as good as its inputs. Raw prices on their own are not useful features, and different currencies
and timeframes live on very different scales. I needed one repeatable pipeline that turns raw bars plus a list of
trades into a clean, comparable feature table per segment.

## Approach

- **Feature creation:** for each currency and timeframe (H1, H4, D1), compute indicators with `ta` and TA-Lib
  (trend, momentum, volatility, volume families), EMAs, and mid prices from bid/ask.
- **Join:** merge the indicator table with the strategy's trade entries on currency, timeframe and date-hour, so
  every trade has the market state at entry.
- **Time features:** year, month, day, hour, hour-of-day buckets and recency.
- **Standardisation:** `StandardScaler` fitted per currency / condition / timeframe / strategy segment.
- **Clustering:** K-means with 10, 20, 30, 40 and 50 clusters per segment; the labels are saved as features.
- **Feature importance:** a decision tree per segment scores each variable; variables are ranked within segment.

## Notebooks

| Notebook | What it does |
|---|---|
| [`01_feature_engineering_2020-09-16.ipynb`](notebooks/01_feature_engineering_2020-09-16.ipynb) | Builds the indicator features per currency and timeframe from raw bars |
| [`02_merge_features_with_trade_entries.ipynb`](notebooks/02_merge_features_with_trade_entries.ipynb) | Joins features to trade entries and the good/bad label |
| [`03_standardisation_clustering_feature_importance.ipynb`](notebooks/03_standardisation_clustering_feature_importance.ipynb) | Time features, per-segment scaling, K-means clusters, decision-tree importance ranking |

## How to run

```bash
python -m venv .venv
.venv/Scripts/activate        # on Linux / macOS: source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook notebooks/
```

Run the notebooks in order. The data is not included; see [data/README.md](data/README.md) for the expected
columns, and set the `path` variables at the top of each notebook. More detail is in
[docs/methodology.md](docs/methodology.md).

## What I would do differently today

- **Fit on train only.** The scaler, the clusters and the importance ranking were fitted on the whole history.
  Downstream test results are then a bit optimistic. Today I would fit them inside a pipeline on the training
  window only and apply them to later data.
- **Point-in-time joins.** The join is on date and hour. I would make sure each feature uses only bars that had
  closed before the trade was entered, and test that with an explicit check.
- **Fewer, better features.** Hundreds of overlapping indicators are highly correlated. I would prune by
  correlation and use permutation or SHAP importance on a validation fold instead of a single tree.
- **Clusters as features.** Five cluster counts is a lot of extra columns; I would pick one by validation.
- **Engineering.** Plain functions in a package, a config file instead of hard-coded paths, Parquet instead of CSV
  and Excel, and unit tests for each transform.
