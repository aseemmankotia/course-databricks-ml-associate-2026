## Chapter 5: Exploratory Data Analysis and Data Cleaning at Scale — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | df.dropna(how='any') vs df.dropna(how='all') | 'any' drops a row if AT LEAST ONE column is null. 'all' drops a row only if EVERY column is null. Exam tip: 'any' is the default if how is omitted. |
| 2 | PySpark syntax to fill nulls in specific columns with different values | df.na.fill({'colA': 0, 'colB': 'unknown'}) — a dict maps column name to fill value. Exam tip: fillna() and na.fill() are interchangeable. |
| 3 | df.describe() default output columns | count, mean, stddev, min, max. Exam tip: no percentiles — use summary() for quartiles. |
| 4 | df.summary() default output columns | count, mean, stddev, min, 25%, 50%, 75%, max. Exam tip: summary() can take stat names as args, e.g. df.summary('count','min','max') to limit output. |
| 5 | How to compute an approximate median/percentile efficiently on a huge Spark DataFrame? | df.approxQuantile('col', [0.5], relativeError). relativeError=0.0 = exact but expensive; higher value = faster, less precise. Exam tip: use for IQR outlier detection at scale. |
| 6 | IQR outlier detection formula | IQR = Q3 - Q1. Lower bound = Q1 - 1.5*IQR. Upper bound = Q3 + 1.5*IQR. Values outside these bounds are flagged as outliers. Exam tip: compute Q1/Q3 with approxQuantile on large data. |
| 7 | toPandas() vs pandas API on Spark for EDA on large data | toPandas() collects ALL data to the driver — risks OOM on large datasets. pandas API on Spark (df.pandas_api()) keeps computation distributed while offering pandas-like syntax. Exam tip: choose pandas API on Spark when data won't fit on one machine. |
| 8 | How to convert a Spark DataFrame to pandas API on Spark? | df.pandas_api() (current method name; older code may show to_pandas_on_spark(), now deprecated in favor of pandas_api()). Import via `import pyspark.pandas as ps`. |
| 9 | Databricks command for one-line automated data profiling with histograms | dbutils.data.summarize(df) — generates per-column stats, null counts, and histogram visualizations directly in the notebook output. Databricks-notebook only, not vanilla OSS Spark. |
| 10 | How to quickly check null counts per column in a Spark DataFrame? | df.select([count(when(col(c).isNull(), c)).alias(c) for c in df.columns]).show() — summary()/describe() do NOT report null counts directly. |
| 11 | Detecting skew visually before modeling | Convert a sampled subset to pandas (df.sample(fraction=...).toPandas()) and plot a histogram; a long tail on one side indicates skew. Exam tip: never call toPandas() on the FULL large dataset just to plot — sample first. |
| 12 | Why sample before visualizing distributions in a notebook? | Visualization libraries (matplotlib/seaborn) and toPandas() require data to fit in driver memory. Sampling avoids driver OOM while still revealing distribution shape/skew/outliers. |
