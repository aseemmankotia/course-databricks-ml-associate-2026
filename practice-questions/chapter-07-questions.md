## Chapter 7: Training Classical Models: scikit-learn vs Spark ML — Practice Questions

**Q1.** A data science team needs to train a single gradient boosting model on a dataset of 500GB stored in Delta Lake. The dataset does not fit into the memory of the Databricks driver node. Which approach BEST meets this requirement?

A) Load data into a pandas DataFrame with pandas API on Spark and train with scikit-learn on the driver
B) Use joblib-spark to distribute scikit-learn's GradientBoostingClassifier training across the cluster
C) Use Spark ML's GBTClassifier/GBTRegressor within a Pipeline to train directly on the distributed DataFrame
D) Sample 10% of the data so it fits in driver memory, then train with scikit-learn

<details>
<summary>Answer</summary>

**Correct: C**

Spark ML's algorithms are designed to train natively on distributed DataFrames without requiring all data on one node, correctly handling datasets larger than driver memory.

**Why the others are wrong:**
- pandas API on Spark still requires collecting data into a single pandas structure for scikit-learn, which won't fit in driver memory at this scale.
- joblib-spark distributes independent tasks (like hyperparameter search or CV folds) that each still require the full dataset locally — it does not partition data for one model's fit.
- Sampling changes the training set, altering the actual problem rather than solving the architecture requirement.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A retail company wants to train 5,000 independent scikit-learn models, one per store, where each store's data (a few thousand rows) fits comfortably in memory. Which technique is the MOST efficient way to train all models using the cluster's full capacity?

A) Use joblib-spark's parallel_backend to fit a single scikit-learn model across the cluster
B) Use a Pandas UDF with applyInPandas to group by store_id and train one scikit-learn model per group in parallel across executors
C) Loop through each store sequentially on the driver, saving each trained model
D) Use Spark ML's OneVsRest classifier to encapsulate all 5,000 models as one Pipeline

<details>
<summary>Answer</summary>

**Correct: B**

applyInPandas groups the DataFrame by key and sends each group's pandas DataFrame to executors, letting them each train a scikit-learn model in parallel — the standard pattern for per-group models.

**Why the others are wrong:**
- joblib-spark parallelizes tasks for training/tuning ONE model, not many independent group models.
- Sequential looping on the driver wastes cluster parallelism and is slow at this scale.
- OneVsRest is a Spark ML multiclass meta-estimator, unrelated to independent per-group models.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A data scientist has a dataset of 2 million rows that fits into driver memory. They want to speed up a scikit-learn RandomForestClassifier's GridSearchCV, which currently only runs on the driver. What is the recommended way to distribute this specific workload in Databricks?

A) Convert the dataset to a Spark DataFrame and use Spark ML's RandomForestClassifier instead
B) Use a Pandas UDF grouped by a random partition key to train separate forests in parallel
C) Increase the driver node size to have more cores available
D) Register the joblib-spark backend and wrap GridSearchCV.fit() inside a `with parallel_backend('spark')` context

<details>
<summary>Answer</summary>

**Correct: D**

joblib-spark plugs into scikit-learn's joblib-based parallelism (used internally by GridSearchCV) letting the many independent fit/scoring tasks run across the whole Spark cluster instead of only the driver's cores.

**Why the others are wrong:**
- Switching algorithms changes model semantics and isn't required — the exam scenario asks to scale the SAME sklearn workflow.
- Grouping by a random key trains separate models on data subsets, not the requested hyperparameter search over the full dataset.
- Scaling the driver vertically helps somewhat but isn't the designed distributed solution the exam expects.

</details>

---

**Q4.** A team trained one model using scikit-learn's LogisticRegression on the driver and another using Spark ML's LogisticRegression on a distributed DataFrame. Both need to be logged with MLflow. Which statement is correct?

A) The scikit-learn model should be logged with mlflow.sklearn.log_model(); the Spark ML model should be logged with mlflow.spark.log_model()
B) Both models must be logged using mlflow.sklearn.log_model() because MLflow does not support Spark ML models
C) Both models must first be converted to ONNX format before MLflow can log either of them
D) Spark ML models cannot be logged with MLflow; they must be manually saved to DBFS and reloaded with Spark

<details>
<summary>Answer</summary>

**Correct: A**

MLflow provides separate model flavors — mlflow.sklearn for scikit-learn estimators and mlflow.spark for Spark ML pipelines/models — each preserving correct native serialization and reload behavior.

**Why the others are wrong:**
- MLflow explicitly supports the 'spark' flavor for Spark ML models.
- ONNX conversion is optional and not required for MLflow logging.
- mlflow.spark.log_model() exists specifically to avoid manual DBFS handling.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** Which two lines of code are required before wrapping scikit-learn's cross_val_score() in a joblib-spark parallel backend context?

A) `from sklearn.spark import SparkBackend` and `SparkBackend.enable()`
B) `import pyspark.ml.tuning as tuning` and `tuning.enable_joblib()`
C) `from joblibspark import register_spark` and `register_spark()`
D) `spark.conf.set("spark.joblib.enabled", "true")` and restart the cluster

<details>
<summary>Answer</summary>

**Correct: C**

The joblib-spark library requires importing register_spark from the joblibspark package and calling it once to register 'spark' as a valid joblib backend before using `with parallel_backend('spark'):`.

**Why the others are wrong:**
- No such sklearn.spark module exists.
- Not a real API; confuses Spark ML's tuning module with the separate joblib-spark library.
- joblib-spark is a Python-level registration, not a cluster-wide Spark configuration setting.

</details>

---

**Q6.** In the Spark ML Pipeline API, what is returned when you call .fit() on a Pipeline containing a VectorAssembler stage followed by a LogisticRegression stage?

A) A new Estimator ready to call .fit() again
B) A pandas DataFrame containing predictions
C) A raw NumPy array of learned coefficients
D) A PipelineModel, which is a Transformer that can call .transform() on new data

<details>
<summary>Answer</summary>

**Correct: D**

Calling .fit() on a Pipeline (itself an Estimator) produces a PipelineModel — a Transformer whose .transform() method applies each fitted stage in sequence to new data.

**Why the others are wrong:**
- Fitting an Estimator produces a Transformer/Model, not another Estimator.
- transform() returns a Spark DataFrame, not pandas, and only after calling .transform() separately.
- Coefficients may be accessible from underlying stages, but .fit() itself returns the full PipelineModel object.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** In the Spark ML Pipeline API, what is the fundamental distinction between an Estimator and a Transformer?

A) An Estimator implements .transform() to produce predictions; a Transformer implements .fit() to learn parameters
B) Estimators only work with Spark ML, while Transformers only work with scikit-learn
C) There is no functional difference — the terms are interchangeable in Spark ML
D) An Estimator implements .fit() to learn parameters from data and produce a Model; a Transformer implements .transform() to convert one DataFrame into another

<details>
<summary>Answer</summary>

**Correct: D**

This matches the Spark ML Pipeline API design exactly: Estimators (e.g., LogisticRegression, StringIndexer) learn from data via fit(); Transformers (e.g., VectorAssembler, or a fitted Model) convert DataFrames via transform().

**Why the others are wrong:**
- This reverses the actual definitions.
- Both concepts exist entirely within Spark ML, not scikit-learn.
- They are distinct stage types with different method contracts in the Pipeline API.

</details>

---

**Q8.** A scikit-learn model was trained on a sample of data that fit on the driver node, but production requires scoring a 200GB Spark DataFrame. Which approach BEST scales inference?

A) Use mlflow.pyfunc.spark_udf() to load the logged scikit-learn model as a Spark UDF and apply it in a distributed .withColumn() transformation
B) Collect the 200GB DataFrame into a pandas DataFrame and call model.predict()
C) Retrain the model using Spark ML so it can natively score the DataFrame
D) Export the model to ONNX and run predictions using the driver's CPU in a loop over partitions

<details>
<summary>Answer</summary>

**Correct: A**

mlflow.pyfunc.spark_udf() wraps a single-node model (including scikit-learn) so its predict() logic runs in parallel across executors on partitions of a Spark DataFrame — the standard way to scale single-node model inference in Databricks.

**Why the others are wrong:**
- Collecting 200GB to the driver risks OOM and defeats distributed scaling.
- Retraining changes model behavior and is unnecessary just to score data.
- Manually looping over partitions on the driver is not how Spark distributes work and is inefficient.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

