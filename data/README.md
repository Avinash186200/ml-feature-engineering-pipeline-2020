# Data

The data is not included in this repo. The notebooks expect:

1. **Raw price bars** (CSV), one file per timeframe or one combined file, with columns:
   `Currency`, `TimeFrame` (H1 / H4 / D1), `date`, `bidopen`, `bidhigh`, `bidlow`, `bidclose`,
   `askopen`, `askhigh`, `asklow`, `askclose`, `volume`.
2. **Trade entries** (Excel or CSV) from the rule-based strategy, with columns:
   `Currency`, `TimeFrame`, `Condition`, `Strategy`, `date`, `Good_Bad_Flag` (1 good / 0 bad), `Profit`,
   `TP_Ratio` and the exit fields of the strategy.

Outputs written by the notebooks (also not included): the indicator table, `Normal Model data.csv`,
`All Strategy_model_standarised_data.csv`, one clustered file per cluster count, and
`All Strategy_Relative_importance.csv`.
