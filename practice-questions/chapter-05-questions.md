## Chapter 5: Exploratory Data Analysis and Data Cleaning at Scale — Practice Questions

**Q1.** A DataFrame has 12 columns. A data engineer wants to drop only the rows where every single column is null, keeping rows that have at least one non-null value. Which command accomplishes this?

A) df.dropna(how='any')
B) df.dropna(thresh=1)
C) df.na.drop(subset=df.columns)
D) df.dropna(how='all')

<details>
<summary>Answer</summary>

**Correct: D**

how='all' only drops a row when ALL values in that row are null; rows with partial data are kept.

**Why the others are wrong:**
- how='any' drops a row if ANY column is null, which removes far more rows than intended.
- thresh=1 keeps rows with at least 1 non-null value, which actually gives the same result as how='all' in behavior but thresh is not the standard/expected API call tested here and doesn't clearly express intent — it's a distractor that overlaps semantically but is not the canonical answer.
- na.drop(subset=df.columns) without how='all' defaults to how='any' behavior and drops rows with any null in the listed subset.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A retail dataset has an 'order_amount' column with extreme values caused by data entry errors. The data scientist wants to flag outliers using the interquartile range (IQR) method on a Spark DataFrame with 200 million rows, where computing an exact quantile is too expensive. Which approach BEST fits this requirement?

A) Convert the entire DataFrame to pandas with toPandas() and use pandas .quantile()
B) Use df.summary() and manually read the mean and stddev to flag values beyond 3 standard deviations
C) Use df.describe() to get min and max and treat those as the outlier thresholds
D) Use df.approxQuantile('order_amount', [0.25, 0.75], relativeError) with a relativeError greater than 0

<details>
<summary>Answer</summary>

**Correct: D**

approxQuantile computes distributed approximate quantiles efficiently on large datasets; a relativeError > 0 trades a small accuracy loss for speed, which is the standard scalable IQR approach in Spark.

**Why the others are wrong:**
- toPandas() on 200 million rows will attempt to collect all data to the driver and will likely cause an out-of-memory failure.
- Mean/stddev with a 3-sigma rule is a different (Z-score) outlier method, not the IQR method requested, and is sensitive to the extreme values already skewing the mean.
- min and max are the extreme values themselves, not thresholds for identifying which values are outliers.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A dataset contains a 'region' column and a 'sales' column with missing sales values. The best practice for imputing sales is to fill missing values with the median sales value calculated separately per region, not with a single global median. Which approach correctly implements this in PySpark?

A) Compute per-region medians with groupBy('region') and approxQuantile, then join the medians back and use coalesce() to fill nulls per group
B) df.fillna(df.select(mean('sales')).first()[0])
C) df.na.fill({'sales': df.approxQuantile('sales', [0.5], 0.0)[0]})
D) df.dropna(subset=['sales'])

<details>
<summary>Answer</summary>

**Correct: A**

Per-group imputation requires computing the statistic within each group and joining it back before using coalesce()/fillna logic conditioned on region; there is no single built-in fillna that fills per-group values directly.

**Why the others are wrong:**
- fillna with a global mean ignores the per-region requirement entirely.
- approxQuantile applied to the whole 'sales' column produces one global median, not per-region medians.
- Dropping rows discards data and does not satisfy the imputation requirement.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** A dataset is too large to fit into a single machine's memory, but the team wants to use familiar pandas-style syntax (like value_counts(), plot(), and describe()) for exploratory analysis instead of pure PySpark syntax. Which tool should they use?

A) pandas API on Spark (pyspark.pandas), accessed via df.pandas_api()
B) Convert the DataFrame with toPandas() and work entirely in pandas
C) Use only Spark SQL for all analysis
D) Export the DataFrame to a local CSV and reload with pandas

<details>
<summary>Answer</summary>

**Correct: A**

pandas API on Spark provides pandas-like syntax while keeping computation distributed across the cluster, avoiding driver memory limits.

**Why the others are wrong:**
- toPandas() collects all data to the driver, which fails or is extremely slow on data too large to fit in memory.
- Spark SQL doesn't provide pandas-style method chaining like value_counts() or .plot().
- Exporting to CSV and reloading locally still requires the data to fit in local memory and adds unnecessary I/O overhead.

</details>

---

**Q5.** In a Databricks notebook, which command produces an interactive, auto-generated data profile including histograms, null counts, and summary statistics for every column with a single call?

A) df.describe().show()
B) df.summary().show()
C) dbutils.data.summarize(df)
D) display(df)

<details>
<summary>Answer</summary>

**Correct: C**

dbutils.data.summarize(df) is a Databricks-specific utility that generates a full automated data profile with per-column histograms, null counts, and statistics in the notebook output.

**Why the others are wrong:**
- describe() only returns count, mean, stddev, min, and max as a table — no visualizations.
- summary() adds percentiles to describe()'s output but still returns a plain table, no histograms.
- display(df) renders the DataFrame as a table (with optional manual chart building) but does not auto-generate a full statistical profile.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** A data scientist calls df.summary() with no arguments on a Spark DataFrame. Which set of statistics is returned by default?

A) count, mean, stddev, min, max only
B) mean, median, mode, variance, skewness
C) count, mean, stddev, min, 25%, 50%, 75%, max
D) count, min, max, and null count

<details>
<summary>Answer</summary>

**Correct: C**

df.summary() with no arguments returns count, mean, stddev, min, the 25th/50th/75th percentiles, and max.

**Why the others are wrong:**
- That list describes df.describe(), not df.summary(), which additionally includes percentiles.
- Spark's summary() does not compute mode or skewness natively.
- Null counts are not part of summary()'s default output; they must be computed separately, e.g. with isNull() aggregations.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** When calling df.approxQuantile('col', [0.5], relativeError), what does setting relativeError=0.0 cause?

A) The exact quantile is computed, which can be significantly more computationally expensive than an approximate one
B) The quantile computation is skipped entirely and returns null
C) The function throws a runtime error because 0.0 is an invalid value
D) It returns the mean instead of the requested quantile

<details>
<summary>Answer</summary>

**Correct: A**

A relativeError of 0.0 forces an exact quantile calculation, which requires a full sort/shuffle and is far more expensive on large datasets than using a positive relativeError.

**Why the others are wrong:**
- The computation still runs; it simply becomes exact rather than approximate.
- 0.0 is a valid, accepted value for relativeError.
- approxQuantile always returns the requested quantile value(s), never the mean.

</details>

---

**Q8.** What is the key functional difference between df.describe() and df.summary() in Spark?

A) describe() runs on the driver only, while summary() is fully distributed
B) summary() includes percentile statistics (25%, 50%, 75%) in addition to what describe() provides, and its statistics can be customized
C) describe() works only on numeric columns while summary() works only on string columns
D) There is no difference; they are aliases for the same method

<details>
<summary>Answer</summary>

**Correct: B**

summary() is a superset of describe(): it returns everything describe() does plus quartile percentiles, and accepts arguments to select specific statistics like df.summary('count', 'min', 'max').

**Why the others are wrong:**
- Both methods execute as distributed Spark jobs, not driver-only operations.
- Both methods report statistics for numeric columns and count/min/max for string columns; neither is restricted the way this option describes.
- They are distinct methods with different default output columns, not aliases.

</details>

---

