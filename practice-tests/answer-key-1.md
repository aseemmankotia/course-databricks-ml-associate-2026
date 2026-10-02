# Answer Key — Practice Test 1

**Q1. Correct: A**

AutoML automatically preprocesses data, trains multiple candidate models, and produces editable source-code notebooks for each trial, giving both a fast baseline and full transparency into the generated code.

---

**Q2. Correct: D**

Feature Engineering in Unity Catalog (Feature Store) provides managed feature tables with primary keys, point-in-time correct training set creation, automatic lineage to source data and downstream models, and reuse across teams.

---

**Q3. Correct: A**

mlflow.autolog() enables automatic logging of parameters, metrics, and the trained model artifact for supported libraries (including scikit-learn) without requiring explicit log_param/log_metric calls.

---

**Q4. Correct: C**

Models in Unity Catalog use the catalog.schema.model naming convention to provide a single, account-level governed registry with unified access control, auditing, and lineage across all workspaces attached to the metastore.

---

**Q5. Correct: B**

In MLflow, an experiment groups related runs together, and each individual run independently records its own parameters, metrics, tags, and artifacts, enabling comparison across runs within the same experiment.

---

**Q6. Correct: D**

Databricks Runtime for Machine Learning on GPU instance types comes preinstalled with deep learning libraries such as TensorFlow, PyTorch, and Horovod along with the necessary GPU drivers, minimizing manual setup for deep learning workloads.

---

**Q7. Correct: C**

The Feature Engineering client's create_training_set() with a FeatureLookup object is the designed API for joining feature tables to a label DataFrame; it tracks lineage and packages the lookup logic so the same logic can be reused at inference time via score_batch or model serving.

---

**Q8. Correct: A**

Unity Catalog Model Registry replaces the legacy Stages concept with aliases (e.g., 'champion', 'challenger') that point to a specific model version, allowing downstream code to reference models by alias (models:/catalog.schema.model@champion) without hardcoding version numbers.

---

**Q9. Correct: C**

AutoML automates algorithm selection and hyperparameter search for classification, regression, and forecasting tasks, and produces a fully editable notebook per trial so the user can inspect and customize the generated code.

---

**Q10. Correct: D**

mlflow.autolog() enables automatic logging of parameters, metrics, and the trained model artifact for supported libraries like scikit-learn, without requiring manual log_param/log_metric/log_model calls.

---

**Q11. Correct: C**

Databricks Runtime for ML ships preconfigured with GPU drivers, CUDA/cuDNN libraries, and popular ML/deep learning frameworks (PyTorch, TensorFlow, XGBoost, Hugging Face libraries), eliminating manual environment setup that would otherwise be required on Standard Runtime.

---

**Q12. Correct: A**

Setting timestamp_lookup_key in a FeatureLookup instructs the Feature Engineering client to perform a point-in-time join, matching each label row to the most recent feature values that were valid at or before its event timestamp, preventing future data from leaking into training.

---

**Q13. Correct: A**

Feature Engineering tables (in Unity Catalog or the classic Feature Store) require one or more primary key columns. These keys are what FeatureLookup uses to join feature values to a training DataFrame at training time and to score/serve requests.

---

**Q14. Correct: A**

FeatureLookup objects specify the feature table, feature names, and lookup key; passing a list of FeatureLookups to FeatureEngineeringClient.create_training_set() (or the classic FeatureStoreClient) joins features to the label DataFrame and packages the lookup metadata with the logged model for automatic serving-time retrieval.

---

**Q15. Correct: A**

mlflow.autolog() accepts a log_models parameter; setting log_models=False keeps automatic parameter, metric, and tag logging active while suppressing the automatic model artifact logging step, letting the user log a custom model with mlflow.<flavor>.log_model() instead.

---

**Q16. Correct: C**

Models in Unity Catalog use the same three-level namespace as tables (catalog.schema.model_name), and they inherit UC's centralized access control, auditing, and lineage tracking, allowing governed access across workspaces attached to the same metastore.

---

**Q17. Correct: A**

AutoML generates a notebook per trial (and highlights the best one) containing the actual Python source code for data preprocessing, model training with the selected algorithm and hyperparameters, and it logs the run's parameters, metrics, and model to MLflow — the notebook is fully editable so users can adjust it and rerun.

---

**Q18. Correct: D**

Fitting the scaler only on training data and reusing those fitted parameters (mean/std) on the test set ensures the test set statistics never influence training, which is the core requirement for avoiding leakage.

---

**Q19. Correct: C**

The pandas API on Spark (pyspark.pandas) provides a pandas-compatible API that executes in a distributed manner across the cluster, letting teams reuse most of their existing pandas syntax while scaling to large datasets.

---

**Q20. Correct: B**

For skewed numeric distributions, the median is robust to outliers and skew, making it a better single-value imputation choice than the mean and preserving more of the dataset than dropping rows.

---

**Q21. Correct: C**

Passing stratify=y ensures the split preserves the same class distribution in both the train and test sets, which is critical when working with imbalanced classification targets.

---

**Q22. Correct: B**

Tree-based models split on thresholds rather than assuming linear relationships, so label/ordinal encoding or target encoding into a single numeric column works well for high-cardinality categoricals without exploding dimensionality.

---

**Q23. Correct: C**

For time-series forecasting, a chronological split ensures the model is only trained on past data and evaluated on future data, mirroring how the model will be used in production and avoiding leakage of future information into training.

---

**Q24. Correct: B**

Fitting any transformer (scaler, encoder, imputer) on the full dataset before splitting leaks statistical information from the validation/test sets into the training process, inflating evaluation metrics that don't hold up in production. The correct approach is to fit the scaler only on the training set, then use that fitted scaler to transform validation and test sets.

---

**Q25. Correct: C**

Pandas API on Spark provides a pandas-compatible interface (pyspark.pandas) that runs distributed on a Spark cluster, allowing most existing pandas syntax to work with only minor code changes while scaling to large datasets.

---

**Q26. Correct: B**

Spark MLlib's Imputer transformer supports only two strategies for numeric columns: 'mean' and 'median'. Setting strategy='median' fills missing values with the median of each column.

---

**Q27. Correct: B**

With severe class imbalance, accuracy is misleading because a naive model that always predicts the majority class still scores high. F1 (harmonic mean of precision and recall) or PR-AUC directly measure how well the minority (fraud) class is detected.

---

**Q28. Correct: C**

SparkTrials distributes Hyperopt trial evaluations across the Spark cluster's workers, enabling parallel execution of single-node model training trials, which is ideal for tuning scikit-learn or other non-distributed models on Databricks.

---

**Q29. Correct: C**

Wrapping the objective function's per-trial run in mlflow.start_run(nested=True), while an outer parent run is already active, creates child runs nested under the parent — the standard pattern for organizing Hyperopt sweeps in the MLflow UI.

---

**Q30. Correct: B**

Grid search evaluates every possible combination of the specified discrete parameter values, which grows exponentially with the number of parameters. Random search instead samples a fixed budget of combinations from given distributions, often finding good hyperparameters more efficiently in high-dimensional spaces.

---

**Q31. Correct: B**

Spark ML's distributed estimators (e.g., pyspark.ml.regression.LinearRegression) are designed to train on data distributed across the cluster without requiring it to fit into a single node's memory, making them the correct choice for datasets that exceed driver memory.

---

**Q32. Correct: B**

In Spark ML, ParamGridBuilder constructs the grid of hyperparameter combinations, and CrossValidator wraps the Pipeline (estimator) together with an Evaluator to perform k-fold cross-validation across that grid, selecting the best-performing model.

---

**Q33. Correct: D**

SparkTrials distributes individual hyperparameter trial evaluations across the Spark cluster's worker nodes, with each worker running the single-node training job, which is exactly suited to this scenario.

---

**Q34. Correct: D**

parallelism sets how many trials SparkTrials evaluates concurrently. Because algorithms like TPE use results from prior trials to choose the next configuration, high parallelism relative to max_evals means fewer completed trials are available to guide the search, degrading optimization quality even though it speeds up wall-clock time.

---

**Q35. Correct: A**

With severe class imbalance, AUC-PR focuses on precision and recall for the minority (positive) class, giving a much more meaningful comparison of model quality than metrics dominated by the majority class.

---

**Q36. Correct: D**

CrossValidator performs full k-fold cross-validation for every parameter combination, giving a more robust estimate at higher computational cost, while TrainValidationSplit uses just one train/validation split per combination, which is faster but produces a less reliable estimate.

---

**Q37. Correct: A**

mlflow.sklearn.autolog() enables automatic logging of parameters, metrics, and the trained model artifact for scikit-learn estimators without requiring manual logging calls for each run.

---

**Q38. Correct: B**

scikit-learn's GradientBoostingClassifier is a single-node library that requires all training data to fit in the memory of one machine. Spark ML's GBTClassifier is designed to train in a distributed manner across the cluster, handling datasets that exceed single-node memory.

---

**Q39. Correct: A**

SparkTrials distributes hyperparameter evaluations across the Spark cluster, running multiple single-node training trials concurrently on different workers. The parallelism parameter controls how many trials run at once, which is exactly the use case for scaling up tuning of single-node models like scikit-learn's RandomForestClassifier.

---

**Q40. Correct: D**

With a highly imbalanced dataset, accuracy is misleading because a model can achieve high accuracy by simply predicting the majority class most of the time. F1 score (harmonic mean of precision and recall) or recall specifically on the minority class directly measures how well the model identifies actual fraud cases, which is the real business objective.

---

**Q41. Correct: D**

Model Serving endpoints expose a low-latency REST API backed by autoscaling compute, purpose-built for real-time, per-request scoring with sub-second response times.

---

**Q42. Correct: A**

mlflow.pyfunc.spark_udf wraps a registered model as a Spark UDF, allowing it to be applied in parallel across all executors, which is the standard scalable batch-inference pattern on Databricks.

---

**Q43. Correct: D**

Unity Catalog model registry uses the @alias syntax (e.g., models:/catalog.schema.model_name@champion) to reference a specific model version by alias rather than a numeric version or legacy stage.

---

**Q44. Correct: D**

Batch inference scores a fixed dataset at a point in time (e.g., a scheduled job), whereas streaming inference uses Structured Streaming to continuously score incoming records as new data arrives, without a defined end point.

---

**Q45. Correct: B**

Auto Loader combined with Structured Streaming incrementally processes only new files as they arrive and can apply an MLflow model as a UDF within the streaming pipeline, exactly matching the incremental, continuous scoring requirement.

---

