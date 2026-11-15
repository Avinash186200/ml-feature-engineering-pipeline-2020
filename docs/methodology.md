# Methodology

1. **Indicators.** For each currency and timeframe, compute indicator families with `ta` (`add_all_ta_features`)
   and TA-Lib, plus EMAs. Mid prices come from bid and ask.
2. **Join.** Merge indicators onto trade entries using currency, timeframe, year, month, day and hour.
3. **Time features.** Calendar fields, hour buckets and a recency feature.
4. **Scaling.** One `StandardScaler` per segment (currency, condition, timeframe, strategy), so features are
   comparable across pairs and timeframes.
5. **Clustering.** K-means with k in {10, 20, 30, 40, 50} on the scaled features of each segment. Cluster labels
   are written out as extra features.
6. **Ranking.** A decision tree per segment gives an importance score for each variable; variables are ranked within
   their segment and the top ones are used by the classifiers.

**Known limits:** steps 4 to 6 were fitted on all years at once, which leaks a little information into later test
years. The README lists what I would change.
