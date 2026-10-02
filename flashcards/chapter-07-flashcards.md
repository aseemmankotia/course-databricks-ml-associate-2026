## Chapter 7: Training Classical Models: scikit-learn vs Spark ML — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | When should you use single-node scikit-learn vs Spark ML for training? | scikit-learn: data fits in driver memory. Spark ML: dataset too large for a single machine, needs distributed training across the cluster. Exam tip: 'fits in driver memory' is the key phrase pointing to scikit-learn. |
| 2 | What does an Estimator do in the Spark ML Pipeline API? | Implements .fit(df) to learn parameters from data, returning a Transformer (Model). Examples: LogisticRegression, StringIndexer. |
| 3 | What does a Transformer do in the Spark ML Pipeline API? | Implements .transform(df) to convert one DataFrame into another (e.g., adding features or predictions column). A fitted Estimator produces a Transformer. |
| 4 | What is returned by calling .fit() on a Spark ML Pipeline object? | A PipelineModel — a Transformer chaining all fitted stages, exposing .transform() for inference on new data. |
| 5 | How do you train one scikit-learn model per group (e.g. per store) in parallel across a cluster? | Use df.groupBy('key').applyInPandas(train_fn, schema) — a Pandas UDF. Each group's data goes to an executor as a pandas DataFrame trained independently. Exam tip: parallel by GROUP, not by row partition. |
| 6 | What two lines register joblib-spark as a backend? | `from joblibspark import register_spark` then `register_spark()`. Then wrap sklearn calls with `with parallel_backend('spark'): ...` |
| 7 | What kind of scikit-learn workloads does joblib-spark distribute? | Independent joblib-parallelized tasks like GridSearchCV, RandomizedSearchCV, and cross_val_score — NOT row-wise model fitting itself. It distributes many fit/score calls, not one model's training over partitioned data. |
| 8 | MLflow flavor for logging a scikit-learn model? | mlflow.sklearn.log_model(model, artifact_path). Load back with mlflow.sklearn.load_model() or mlflow.pyfunc.load_model(). |
| 9 | MLflow flavor for logging a Spark ML model/pipeline? | mlflow.spark.log_model(pipelineModel, artifact_path). Preserves Spark ML native serialization for distributed reloading. |
| 10 | How do you scale inference of a single-node (scikit-learn) model over a large Spark DataFrame? | Use mlflow.pyfunc.spark_udf(spark, model_uri) to wrap the model as a Spark UDF, then apply with df.withColumn('prediction', udf(*cols)) for distributed scoring across executors. |
| 11 | Pandas UDF per-group training vs joblib-spark — key difference? | applyInPandas: trains MANY independent models, one per group, in parallel (data-parallel by key). joblib-spark: distributes MANY independent tasks (CV folds/hyperparam combos) for training ONE model, requiring the full dataset to fit on each worker. |
| 12 | Which Spark ML algorithms mirror common scikit-learn estimators? | LogisticRegression, LinearRegression, RandomForestClassifier/Regressor, GBTClassifier/Regressor, KMeans — all trained natively on distributed DataFrames instead of collecting data to the driver. |
| 13 | Is VectorAssembler an Estimator or a Transformer? | Transformer — it has no learnable parameters, only implements .transform() to combine columns into a feature vector. Contrast with StringIndexer, which IS an Estimator (must .fit() to learn label mappings). |
