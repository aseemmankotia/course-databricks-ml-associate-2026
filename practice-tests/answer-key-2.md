# Answer Key — Practice Test 2

**Q1. Correct: A**

timestamp_lookup_key tells create_training_set to perform a point-in-time join, matching each observation only with feature values that existed at or before its timestamp, which prevents future data (label leakage) from entering the training set.

---

**Q2. Correct: C**

Models registered in Unity Catalog use aliases (e.g., 'Champion', 'Challenger') instead of the legacy stage system (Staging/Production/Archived). Assigning an alias is the supported way to mark a version as the one currently used for a purpose like production serving.

---

**Q3. Correct: A**

AutoML produces a fully editable, glass-box source code notebook for each trial (including the best trial). Data scientists are expected to open this notebook, modify preprocessing/feature engineering or hyperparameters directly in the code, and rerun it to produce a customized model — this is the documented intended workflow.

---

**Q4. Correct: C**

MLflow nested runs, started with mlflow.start_run(nested=True) inside an active parent run, let each Hyperopt/SparkTrials trial log its own params, metrics, and artifacts as a child run visible in the tracking UI under the parent run — exactly the hierarchy Hyperopt integration with MLflow uses.

---

**Q5. Correct: D**

Databricks Runtime ML GPU editions ship with CUDA, cuDNN, and GPU-compatible builds of deep learning frameworks preinstalled and configured; combined with a GPU instance type, this is the only listed option that provides working GPU-accelerated training out of the box.

---

**Q6. Correct: A**

Unity Catalog governs models the same way it governs tables and other securable objects: fine-grained permissions are granted via SQL GRANT statements at the catalog, schema, or individual model level, allowing precise control such as granting EXECUTE/USE privileges only to the fraud-detection team's group.

---

**Q7. Correct: A**

timestamp_lookup_key tells the Feature Engineering client which column in the training DataFrame to use for a point-in-time (as-of) join against the feature table's timestamp key, ensuring only historically valid feature values are retrieved.

---

**Q8. Correct: C**

AutoML automatically produces a data exploration notebook containing profiling summaries, missing-value statistics, and distribution visualizations before running any training trials, so users can review data quality issues.

---

**Q9. Correct: C**

Unity Catalog model registry replaces stages with aliases (e.g., @champion, @challenger) — mutable named references you assign to specific model versions to indicate their deployment role.

---

**Q10. Correct: C**

Databricks Runtime ML (GPU) ships with pre-installed, GPU-optimized versions of TensorFlow, PyTorch, and Horovod along with compatible CUDA/cuDNN drivers, avoiding manual setup.

---

**Q11. Correct: B**

Autologging with default settings logs parameters, training metrics, and the model artifact automatically, but it does not save the full training dataset as a CSV artifact — dataset logging must be explicitly enabled via log_datasets or handled separately.

---

**Q12. Correct: B**

primary_keys is a required parameter that defines the column(s) uniquely identifying each row, which the Feature Engineering client uses to perform lookups when joining features to training data or serving them online.

---

**Q13. Correct: C**

timestamp_lookup_key tells create_training_set to perform a point-in-time correct join, matching each label row to the most recent feature values available before that row's timestamp, preventing label leakage.

---

**Q14. Correct: B**

Unity Catalog's Model Registry replaces the legacy Staging/Production stage concept with aliases, which are mutable pointers (e.g., 'Champion', 'Challenger') assigned to specific model versions and referenced at serving/inference time.

---

**Q15. Correct: A**

AutoML's data exploration/profiling step detects high-cardinality columns that behave like unique identifiers and marks their semantic type as unsupported in the data exploration notebook, automatically excluding them from the generated training pipelines.

---

**Q16. Correct: B**

SparkTrials integrates with MLflow automatically: each hyperparameter trial run by Hyperopt is logged as a nested child run under the enclosing parent run, without requiring the user to manually manage nested run context.

---

**Q17. Correct: C**

Granting SELECT at the table level scopes access to exactly the single feature table requested, consistent with least-privilege access control in Unity Catalog's three-level namespace (catalog.schema.table).

---

**Q18. Correct: D**

Computing target-mean encodings on the entire dataset (including test rows) leaks information about the test labels into the training features, inflating validation metrics. The correct approach is to fit the encoding strictly on the training split (or within each CV fold) and apply the already-fitted mapping to validation/test data, exactly as with any other transform requiring fit/transform separation.

---

**Q19. Correct: D**

pyspark.ml.feature.Imputer is the distributed, scalable estimator designed for this exact use case: it computes statistics (mean or median) in a distributed manner, supports strategy='median', and produces a fitted ImputerModel that can be composed as a Pipeline stage, ensuring the same imputation is consistently applied to train/validation/test/production data without recomputation or leakage.

---

**Q20. Correct: B**

The pandas API on Spark 'compute.default_index_type' option controls how the default index is generated. The default 'sequence' type computes a monotonically increasing index that requires a single-partition pass, creating a bottleneck on large conversions. Setting it to 'distributed' (or 'distributed-sequence') allows index creation to happen in parallel across partitions, which is the documented performance recommendation for large from_pandas conversions.

---

**Q21. Correct: B**

For time-dependent data, a random shuffle-based split violates the temporal ordering: rows from later dates can end up in the training set while earlier dates end up in test, so the model effectively 'sees the future' during training. The correct approach is a chronological split (e.g., train on the first N months, test on the most recent period) or a time-series-aware cross-validation strategy.

---

**Q22. Correct: B**

Feature Store's FeatureLookup supports a timestamp_lookup_key parameter that performs a point-in-time join, matching each label row to the most recent feature values available as of that row's timestamp, preventing future feature leakage automatically as part of create_training_set().

---

**Q23. Correct: A**

Data leakage occurs whenever information from validation/test data influences how a model or preprocessing step is fit. Deriving scaling statistics from the full dataset (including validation/test) would let the model indirectly 'see' those data points' distributional properties, inflating evaluation metrics. Fitting exclusively on training data guarantees an honest estimate of generalization performance.

---

**Q24. Correct: C**

Target encoding must be fit exclusively on training data (ideally with cross-validation/out-of-fold techniques to prevent overfitting to training targets) and then applied to validation/test sets using the learned mapping. This preserves the encoding's predictive signal without leaking target information across the split.

---

**Q25. Correct: A**

The pandas API on Spark is designed exactly for this scenario: it lets data scientists write familiar pandas-style syntax (including groupby-apply operations via applyInPandas-style semantics) while Spark distributes execution across the cluster, scaling to hundreds of millions of rows without collecting data to the driver.

---

**Q26. Correct: C**

When multiple rows share the same group-level label (here, customer_id), a naive row-level random split allows the same customer's data to appear in both train and test sets. The model can then effectively memorize customer-specific patterns instead of generalizing, inflating test performance. The correct approach is a group-aware split (e.g., scikit-learn's GroupShuffleSplit or manually splitting on unique customer_id) so that all of a customer's rows stay entirely within one split.

---

**Q27. Correct: A**

TPE (the default Hyperopt algorithm) is adaptive — it uses results from completed trials to choose better points to try next. When parallelism is close to max_evals, most trials launch before any results come back, so the algorithm can't learn from prior trials and effectively degrades to random search. Databricks documentation recommends setting parallelism no higher than roughly sqrt(max_evals) to preserve adaptive optimization quality.

---

**Q28. Correct: B**

AUC-PR focuses on the model's ability to correctly identify the minority (positive) class and is far more informative than ROC-based metrics when classes are severely imbalanced, since it isn't inflated by the large number of true negatives.

---

**Q29. Correct: C**

When a dataset fits comfortably in the memory of a single node, using scikit-learn directly is simpler, faster to iterate on, and gives full access to scikit-learn's API and hyperparameters — the overhead of a distributed framework is unnecessary and would slow down development for data this size.

---

**Q30. Correct: D**

With MLflow autologging enabled, each Hyperopt trial launched by SparkTrials is automatically logged as a nested child run under the parent fmin run. Calling search_runs against the experiment and ordering by the desired metric column correctly retrieves the best individual trial run.

---

**Q31. Correct: D**

Because RMSE squares each error before averaging, it disproportionately penalizes large errors/outliers. A model that avoids a few large mistakes (Model A) can therefore have a lower RMSE, while a model that is more consistently accurate on typical cases but occasionally has a larger miss can have a lower MAE (Model B). The two metrics weight error magnitudes differently and can disagree on ranking.

---

**Q32. Correct: B**

hp.quniform samples a continuous value over the given range and rounds it to the nearest multiple of the quantization step (q=1 here), producing integer-valued samples appropriate for an integer hyperparameter like max_depth (the value should be cast to int in the objective function).

---

**Q33. Correct: A**

TPE (the default Hyperopt algorithm) improves suggestions by learning from completed trial results. When parallelism approaches max_evals, most or all trials launch before any results are available, so TPE behaves like random search. Databricks documentation recommends parallelism be meaningfully smaller than max_evals (e.g., sqrt(max_evals)) to preserve adaptive behavior.

---

**Q34. Correct: D**

When the dataset fits in memory, distributing the hyperparameter search itself (not the training data) across the cluster is the most efficient pattern. SparkTrials or joblibspark let each worker train a full scikit-learn model on the complete in-memory data with a different hyperparameter set in parallel, which is far faster than single-node grid search and doesn't require reimplementing the algorithm in Spark ML.

---

**Q35. Correct: D**

The parameter grid has 4 x 3 = 12 hyperparameter combinations. CrossValidator trains a separate model for each combination on each of the 5 folds: 12 x 5 = 60 total model fits.

---

**Q36. Correct: D**

With severe class imbalance, PR-AUC focuses on performance for the positive (minority) class and is far more sensitive to false positives/negatives on that class than ROC-AUC, which can look deceptively high because the large number of true negatives dominates the false positive rate calculation.

---

**Q37. Correct: A**

To group individual Hyperopt trial runs under one parent run in the MLflow UI, wrap the entire fmin call in an outer mlflow.start_run(), and inside the objective function call mlflow.start_run(nested=True) for each trial. This creates the parent-child run hierarchy MLflow displays in the UI, making it easy to compare trials for a single tuning session.

---

**Q38. Correct: D**

When false negatives are more costly than false positives, recall (true positive rate) is the metric that should be prioritized over raw accuracy. Model B catches more actual defaults (recall 0.85 vs 0.62), directly reducing the more expensive error type, even though its overall accuracy is slightly lower.

---

**Q39. Correct: C**

TPE is a sequential model-based algorithm that improves suggestions based on results of previous trials. When parallelism is high relative to max_evals, many trials run concurrently before results feed back into the model, so TPE gets very few sequential updates and behaves close to random search. Best practice is to keep parallelism well below max_evals (e.g., parallelism <= sqrt(max_evals) as a rule of thumb).

---

**Q40. Correct: B**

With severe class imbalance, areaUnderROC can look deceptively high because the true negative rate dominates the curve, masking poor recall/precision on the rare positive class. areaUnderPR focuses on precision and recall for the positive class and is far more sensitive to how well the model actually identifies rare events, making it a better selection criterion during cross-validation for imbalanced problems.

---

**Q41. Correct: A**

Model Serving endpoints are purpose-built for low-latency, high-throughput real-time inference and provide managed autoscaling (including scale-to-zero and burst handling) without requiring the team to manage cluster infrastructure.

---

**Q42. Correct: B**

mlflow.pyfunc.spark_udf loads the registered model and distributes scoring across the Spark cluster as a vectorized UDF applied to a DataFrame column, which is the standard, scalable pattern for large-scale batch inference on Databricks.

---

**Q43. Correct: A**

Latency spikes after idle periods are the classic symptom of scale-to-zero cold starts. Configuring a minimum non-zero provisioned concurrency keeps at least one instance warm, eliminating cold-start latency at the cost of paying for idle capacity.

---

**Q44. Correct: D**

Applying an MLflow model as a Spark UDF inside a Structured Streaming pipeline lets the model score records continuously and in a distributed manner as micro-batches arrive, matching the near-real-time, always-on pipeline requirement without external network calls per event.

---

**Q45. Correct: D**

Unity Catalog Model Registry uses mutable aliases (like @Champion) that point to a specific version and can be reassigned without changing downstream code, replacing the legacy workspace registry's Staging/Production stage transitions.

---

