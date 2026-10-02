## Chapter 5 Cheat Sheet: EDA & Data Cleaning at Scale

### Missing Values
| Method | Behavior |
|---|---|
| `df.dropna(how='any')` | Drop row if ANY column null (default) |
| `df.dropna(how='all')` | Drop row only if ALL columns null |
| `df.dropna(thresh=n)` | Keep rows with at least `n` non-null values |
| `df.dropna(subset=[...])` | Only consider listed columns for null check |
| `df.fillna(value)` | Fill all nulls with a single value |
| `df.na.fill({'colA':0,'colB':'NA'})` | Per-column fill values via dict |
| Per-group imputation | `groupBy` + `approxQuantile`/`avg`, then join back + `coalesce()` |

### Summary Statistics
| Method | Output |
|---|---|
| `df.describe()` | count, mean, stddev, min, max |
| `df.summary()` | count, mean, stddev, min, 25%, 50%, 75%, max |
| `df.summary('count','min','max')` | Custom subset of stats |
| `df.approxQuantile(col, [p1,p2], relativeError)` | Approximate percentiles; `relativeError=0.0` = exact (expensive) |
| Null counts | Not in describe()/summary(); use `count(when(col(c).isNull(),c))` |

### Outlier Detection (IQR method)
```
q1, q3 = df.approxQuantile('col', [0.25, 0.75], 0.05)[0], df.approxQuantile('col', [0.25, 0.75], 0.05)[1]
iqr = q3 - q1
lower, upper = q1 - 1.5*iqr, q3 + 1.5*iqr
outliers = df.filter((col('col') < lower) | (col('col') > upper))
```

### pandas API on Spark vs toPandas()
| Feature | `toPandas()` | `df.pandas_api()` |
|---|---|---|
| Computation | Collects to driver | Stays distributed |
| Memory risk | High (OOM on big data) | Low |
| Syntax | Native pandas | pandas-like (pyspark.pandas) |
| Use case | Small data, final viz | Large-data EDA |

### Databricks-Specific EDA
- `dbutils.data.summarize(df)` — one-line auto profile: stats + histograms + null counts (Databricks notebooks only).
- `display(df)` — renders table; supports manual chart building (bar, histogram, box plot) via UI.

### Visualizing Distributions Safely
1. Sample first: `df.sample(fraction=0.01).toPandas()`
2. Plot with matplotlib/seaborn or `display()` built-in charts
3. Look for: long tails (skew), multi-modal shapes, extreme spikes (outliers)

### Data Quality Checks Before Modeling
- Verify null counts per column post-cleaning
- Verify row counts before/after dropna to quantify data loss
- Check value ranges/domains match expectations (e.g., no negative ages)
- Confirm schema types match expected feature types before feeding into pipelines

### Common Exam Traps
- Confusing `describe()` (no percentiles) with `summary()` (has percentiles)
- Calling `toPandas()` on huge DataFrames instead of using pandas API on Spark
- Using `relativeError=0.0` unnecessarily on huge datasets (very expensive)
- Forgetting `dropna(how='all')` vs default `how='any')` semantics
