## Chapter 11: Batch, Streaming, and Real-Time Model Serving — Practice Questions

**Q1.** A retail company has 800 million historical transaction records sitting in a Delta table and needs fraud-risk scores computed once overnight for reporting. There is no low-latency requirement. Which approach BEST meets this requirement?

A) Use mlflow.pyfunc.spark_udf to apply the model as a UDF across the Delta table in a Spark batch job
B) Deploy a Databricks Model Serving endpoint and send each row as a separate REST request
C) Wrap the model in a Structured Streaming job reading from the Delta table with a trigger of once
D) Export the model to ONNX and score all 800 million rows on a single driver node

<details>
<summary>Answer</summary>

**Correct: A**

spark_udf distributes inference across the cluster using Spark's parallelism, which is the standard and most cost-effective way to score large static datasets.

**Why the others are wrong:**
- Per-row REST calls to a Serving endpoint add massive network overhead and are not designed for large-scale batch throughput
- Structured Streaming adds unnecessary checkpointing/state overhead for a one-time static batch job — spark_udf is simpler and sufficient
- Single-node scoring ignores Spark's distributed compute and would take far too long at this scale

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A mobile app needs a fraud prediction returned in under 200ms for each individual transaction as the user checks out. Which Databricks capability should be used?

A) A Databricks Model Serving endpoint exposing the model via REST API
B) mlflow.pyfunc.spark_udf applied to a Delta table
C) A Structured Streaming job with a 1-minute trigger interval
D) A scheduled Databricks Job running batch inference every hour

<details>
<summary>Answer</summary>

**Correct: A**

Model Serving endpoints are purpose-built for real-time, low-latency, single-record REST inference with autoscaling compute.

**Why the others are wrong:**
- spark_udf requires a Spark job/cluster and is designed for bulk batch scoring, not single-record millisecond responses
- Even a 1-minute streaming trigger introduces latency far above the 200ms requirement
- Hourly batch jobs cannot meet a sub-200ms per-request latency requirement

</details>

---

**Q3.** New sensor readings arrive continuously as files landing in cloud storage, and predictions must be written to a Delta table within seconds of arrival, not in a single scheduled run. Which architecture BEST fits this requirement?

A) A Databricks Model Serving endpoint polled once per hour by a cron job
B) A Structured Streaming pipeline using Auto Loader as the source and mlflow.pyfunc.spark_udf inside the transformation, writing to a Delta sink
C) A one-time mlflow.pyfunc.spark_udf batch job scheduled nightly
D) A single-node Python script polling the storage directory in a while loop

<details>
<summary>Answer</summary>

**Correct: B**

Structured Streaming with Auto Loader continuously ingests new files and, combined with spark_udf, scores them incrementally and near-continuously, writing results with low end-to-end latency.

**Why the others are wrong:**
- Hourly polling of a Serving endpoint doesn't scale to continuous file arrival and wastes the endpoint's real-time design
- A nightly batch job doesn't satisfy the requirement for near-continuous scoring within seconds
- A manual polling script is not scalable, fault-tolerant, or exactly-once — it duplicates functionality Structured Streaming already provides

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** Which Databricks feature provides an autoscaling, managed REST endpoint that can scale to zero compute during idle periods?

A) Delta Live Tables
B) Databricks Model Serving
C) Databricks SQL Warehouses
D) MLflow Model Registry

<details>
<summary>Answer</summary>

**Correct: B**

Model Serving endpoints automatically scale compute based on request volume and can scale down to zero during idle periods to reduce cost.

**Why the others are wrong:**
- Delta Live Tables is a pipeline framework for building/orchestrating ETL, not a real-time inference REST endpoint
- SQL Warehouses serve SQL queries, not model inference REST calls
- The Model Registry (now largely superseded by Unity Catalog model versioning) stores and versions models but does not itself serve REST traffic

</details>

---

**Q5.** A model is registered in Unity Catalog as main.ml_models.churn_predictor with version 4 and alias 'champion' pointing to that version. Which URI correctly loads this model for use with mlflow.pyfunc.spark_udf?

A) models:/main.ml_models.churn_predictor/4
B) runs:/main.ml_models.churn_predictor/4
C) dbfs:/models/main/ml_models/churn_predictor/4
D) unity-catalog://main.ml_models.churn_predictor@champion

<details>
<summary>Answer</summary>

**Correct: A**

Unity Catalog model URIs follow the format models:/<catalog>.<schema>.<model_name>/<version_or_alias>, and version 4 (or @champion) is a valid reference to load the model.

**Why the others are wrong:**
- The runs:/ scheme references an MLflow run's artifact path, not a registered model version, and doesn't use catalog.schema naming
- dbfs:/ paths reference raw filesystem locations, not the model registry, and won't resolve UC-managed model metadata
- This is not a valid MLflow URI scheme — Unity Catalog model references always start with models:/

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** When creating a Databricks Model Serving endpoint, which setting directly controls whether the endpoint reduces its compute to zero after a period of no incoming requests?

A) The scale-to-zero configuration on the serving endpoint's compute settings
B) The model's flavor (e.g., sklearn vs pyfunc)
C) The Unity Catalog model alias assigned to the endpoint
D) The number of columns in the input schema

<details>
<summary>Answer</summary>

**Correct: A**

Scale-to-zero is an explicit compute configuration on the endpoint that determines whether provisioned compute is released entirely during idle periods to save cost.

**Why the others are wrong:**
- The model flavor affects how the model is loaded/served but has no bearing on idle scaling behavior
- The alias only determines which model version is served, not the scaling behavior
- Input schema size affects payload processing, not compute scaling policy

</details>

---

**Q7.** A team wants to apply a registered MLflow model to a DataFrame column and add the predictions as a new column in a Spark batch job. Which line of code correctly accomplishes this?

A) predict_udf = mlflow.pyfunc.load_model(model_uri); df.withColumn('pred', predict_udf('feature_col'))
B) predict_udf = mlflow.spark.log_model(model_uri); df.select(predict_udf('feature_col'))
C) predict_udf = spark.udf.register('model', model_uri); df.withColumn('pred', predict_udf())
D) predict_udf = mlflow.pyfunc.spark_udf(spark, model_uri); df.withColumn('pred', predict_udf(*feature_cols))

<details>
<summary>Answer</summary>

**Correct: D**

mlflow.pyfunc.spark_udf(spark, model_uri) returns a proper Spark UDF that can be applied to one or more feature columns with withColumn to add a predictions column.

**Why the others are wrong:**
- mlflow.pyfunc.load_model returns a plain Python model object, not a Spark UDF — it cannot be used directly inside withColumn on a distributed DataFrame
- mlflow.spark.log_model is used to log a model to a run, not to load or create an inference UDF
- spark.udf.register is used for registering Python/SQL functions manually, not for loading an MLflow model into a UDF

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** Which statement BEST describes the fundamental tradeoff between Model Serving (real-time REST) and mlflow.pyfunc.spark_udf (batch) inference?

A) Model Serving always produces more accurate predictions than spark_udf because it uses newer hardware
B) spark_udf can only be used with deep learning models, while Model Serving works with any model type
C) Model Serving and spark_udf produce identical latency and throughput characteristics; the choice is purely stylistic
D) Model Serving optimizes for low per-request latency on individual records, while spark_udf optimizes for high throughput across large distributed datasets at higher per-request latency

<details>
<summary>Answer</summary>

**Correct: D**

Real-time serving is architected for fast single-record responses (low latency, lower per-node throughput), while batch scoring via spark_udf leverages cluster parallelism for very high aggregate throughput but is not designed for millisecond single-record responses.

**Why the others are wrong:**
- Accuracy is determined by the model itself, not by the serving mechanism
- spark_udf works with any pyfunc-compatible model, not just deep learning models
- The two approaches have very different latency/throughput profiles — this is the core exam distinction, not a stylistic choice

</details>

---

