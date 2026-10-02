# Practice Test 2: Databricks ML Associate Certification: Exam Prep

- **Questions:** 45
- **Time limit:** 90 minutes
- **Passing score:** 70%%

> Answers are in `answer-key-2.md`. Track results in `score-tracker.md`.

---

**Q1.** A data scientist is using Feature Engineering in Unity Catalog to build a training set. Each observation in the label table has an event timestamp, and the data scientist must ensure that only feature values available AS OF that timestamp are joined in, to prevent label leakage. Which FeatureLookup parameter must be specified to achieve this point-in-time correct join?

A) timestamp_lookup_key
B) lookup_key
C) primary_keys
D) feature_lookup

---

**Q2.** A team has registered a model to Unity Catalog and wants to designate the newest version as ready for production traffic using the current, non-deprecated Unity Catalog model versioning approach. What should they do?

A) Transition the model stage to "Production" using the MLflow Client
B) Set a model version tag "stage=Production"
C) Assign the alias "Champion" to the model version
D) Archive all previous model versions

---

**Q3.** A data scientist runs an AutoML classification experiment on a customer churn dataset and wants to modify the preprocessing steps (e.g., imputation strategy) used by the single best-performing trial, then retrain it. What is the correct approach?

A) Open the auto-generated source code notebook for the best trial, edit the preprocessing code, and rerun the notebook
B) Edit the preprocessing options in the AutoML experiment configuration UI before rerunning
C) Edit the model's preprocessing parameters directly from the MLflow Model Registry
D) AutoML-generated models cannot have their preprocessing modified

---

**Q4.** A data scientist uses Hyperopt with SparkTrials to run distributed hyperparameter tuning across a cluster and wants each individual hyperparameter combination logged as its own MLflow run, grouped underneath a single parent run for the overall tuning job. Which MLflow capability enables this run hierarchy?

A) mlflow.autolog() enabled with no additional configuration
B) Creating a separate MLflow experiment for each hyperparameter combination
C) Nested runs, created with mlflow.start_run(nested=True)
D) Creating a new registered model version for each hyperparameter combination

---

**Q5.** A team wants to train a deep learning model with distributed GPU acceleration on a Databricks cluster, using preinstalled CUDA/cuDNN libraries and GPU-optimized versions of frameworks like TensorFlow and PyTorch. Which cluster configuration should they select?

A) Standard (non-ML) Databricks Runtime with a GPU-enabled instance type
B) Databricks Runtime for Machine Learning (CPU) with autoscaling enabled
C) Photon-enabled Databricks Runtime with a GPU-enabled instance type
D) Databricks Runtime for Machine Learning (GPU) with a GPU-enabled instance type

---

**Q6.** A company registers ML models in Unity Catalog under a three-level namespace (catalog.schema.model) and wants to restrict read/inference access to a specific registered model so that only the fraud-detection team can use it, without granting broader workspace-wide access. Which mechanism enforces this?

A) Unity Catalog GRANT statements applied at the catalog, schema, or model object level
B) Cloud provider IAM roles configured at the AWS/Azure account level
C) MLflow Model Registry stage transitions (Staging/Production)
D) Workspace-level access control lists (ACLs) only

---

**Q7.** A feature table 'transactions_features' includes a column 'txn_timestamp' recording when each feature value became valid. When calling FeatureEngineeringClient.create_training_set() and defining a FeatureLookup against this table, which parameter must be set to ensure the join only pulls feature values that existed at or before each training example's event time, preventing data leakage?

A) timestamp_lookup_key
B) lookup_key
C) feature_names
D) table_name

---

**Q8.** A data scientist launches a classification AutoML experiment on a dataset with several columns containing high percentages of missing values and skewed distributions. Before any model training trials begin, AutoML automatically generates which additional notebook to help the user understand the input data?

A) A model deployment notebook
B) A Feature Store registration notebook
C) A data exploration (EDA) notebook
D) A model monitoring dashboard notebook

---

**Q9.** Your organization has fully migrated its MLflow model registry to Unity Catalog. Since Unity Catalog registered models do not support the legacy Staging/Production stage transitions, how should a data scientist designate which model version is currently serving production traffic?

A) Set a tag named stage=Production on the model version
B) Call mlflow.transition_model_version_stage() with stage='Production'
C) Assign a model alias such as '@champion' to the desired version
D) Enable a webhook that marks the version as production

---

**Q10.** A team needs to train a deep learning model on GPU instances and wants TensorFlow, PyTorch, and Horovod pre-installed and pre-configured with GPU support so no manual library installation is required. Which cluster configuration should they select?

A) Standard Databricks Runtime with a GPU instance type, then %pip install the libraries
B) Databricks Runtime Light on a CPU instance type
C) Databricks Runtime for Machine Learning (GPU variant) on a GPU-enabled instance type
D) Photon-enabled standard Databricks Runtime on a GPU instance type

---

**Q11.** A data scientist enables mlflow.sklearn.autolog() with default settings and fits a RandomForestClassifier. Which of the following is NOT automatically captured as part of the resulting MLflow run?

A) The model's hyperparameters (e.g., n_estimators, max_depth)
B) The complete training dataset saved as a CSV artifact
C) The training accuracy metric
D) The serialized model artifact

---

**Q12.** A data scientist calls FeatureEngineeringClient.create_table() to register a new feature table in Unity Catalog. Which parameter is required to uniquely identify each row for later feature lookups during training set creation and online serving?

A) partition_columns
B) primary_keys
C) timestamp_lookup_key
D) description

---

**Q13.** A data scientist creates a training set using Feature Engineering in Unity Catalog. The label table has a 'transaction_time' column, and the feature table has features that change over time. The team wants each row's features to reflect only the values known at or before that row's transaction time, preventing future data from leaking into training. Which parameter must be set in the FeatureLookup object to achieve this?

A) feature_names
B) lookup_key only, since Feature Engineering always joins on the latest value
C) timestamp_lookup_key
D) rename_outputs

---

**Q14.** A team registers a fraud-detection model in the Unity Catalog Model Registry. They want to mark a specific model version as the one currently serving production traffic, following current Databricks-recommended practice (not the legacy workspace model registry workflow). What should they do?

A) Call mlflow.tracking.MlflowClient().transition_model_version_stage() with stage='Production'
B) Call mlflow.set_registered_model_alias() to assign an alias such as 'Champion' to that model version
C) Tag the model version with a custom tag key 'stage' and value 'production'
D) Delete all other model versions so only the production one remains

---

**Q15.** A data scientist launches a Classification AutoML experiment on a dataset with 250 columns. One column is a customer identifier string with roughly 200,000 unique values across 200,000 rows, and it is not the prediction target. What does AutoML do with this column by default?

A) It automatically flags the column's semantic type as unsupported (likely an identifier) and excludes it from the feature set used for training
B) It converts the column into a numeric hash and includes it as a training feature
C) It one-hot encodes the column, generating 200,000 new binary features
D) It stops the run with an error requiring the user to manually drop the column before retrying

---

**Q16.** A data scientist wraps a Hyperopt fmin() call using SparkTrials inside a single outer mlflow.start_run() block to tune a model's hyperparameters. No explicit nested=True logic is added inside the objective function. What happens to MLflow logging for each individual trial evaluated by SparkTrials?

A) Every trial overwrites the parent run's parameters and metrics with only the last trial's values
B) SparkTrials automatically logs each trial's parameters and metrics as separate nested child runs under the parent run
C) SparkTrials logs all trials into one flat, single run with indistinguishable metric history
D) No parameters or metrics are logged unless mlflow.log_param/log_metric is called manually inside the objective function

---

**Q17.** A data science team needs to give another team read-only access to a single Feature Engineering in Unity Catalog table for scoring purposes, without exposing any other tables in the same schema. Following the principle of least privilege, which grant statement best accomplishes this at the correct object level?

A) GRANT ALL PRIVILEGES ON CATALOG main TO `data_science_readers`
B) GRANT SELECT ON SCHEMA main.feature_store TO `data_science_readers`
C) GRANT SELECT ON TABLE main.feature_store.customer_features TO `data_science_readers`
D) GRANT USE CATALOG ON CATALOG main TO `data_science_readers`

---

**Q18.** A data scientist is building a churn model. Before splitting the data into train and test sets, they compute the mean target value for each category of a high-cardinality 'customer_segment' column across the FULL dataset, and use these means as a new numeric feature. The model achieves 98% AUC on the test set but performs poorly in production. What is the MOST likely cause and correct fix?

A) The model is overfitting due to too many trees; reduce max_depth and n_estimators to regularize it
B) The 'customer_segment' column should be dropped entirely since target encoding is never appropriate for categorical features
C) The test set is too small to be statistically significant; increase the test set size to reduce the inflated AUC
D) Target leakage occurred because the encoding was computed on the full dataset including test labels; recompute the encoding using only the training fold and apply it to validation/test data

---

**Q19.** A team needs to impute missing values in a 500-million-row Spark DataFrame for two numeric columns, replacing missing values with the column median computed at scale, and wants the fitted imputation logic reusable inside an MLflow pipeline stage. Which approach should they use?

A) Convert the DataFrame to pandas with .toPandas() and use pandas.DataFrame.fillna(df.median())
B) Use pyspark.sql.DataFrame.na.drop() to remove all rows containing nulls before training
C) Use pyspark.sql.functions.mean() to compute the column average and hardcode it with .na.fill()
D) Use pyspark.ml.feature.Imputer with strategy='median', fit it on the training data, and include it as a stage in a Pipeline

---

**Q20.** A data scientist converts a 50GB pandas DataFrame to pandas API on Spark using ps.from_pandas(df) so multiple analysts can run distributed transformations on it. They notice that a downstream groupBy-and-sort operation runs extremely slowly, funneling almost all computation through a single task. What Spark configuration is the most likely cause and correct adjustment?

A) spark.sql.shuffle.partitions is set too high; decrease it to 1 to avoid shuffle overhead
B) compute.default_index_type is set to 'sequence', which creates a single-partition monotonically increasing index; set it to 'distributed' before the conversion to enable parallel index creation
C) spark.sql.execution.arrow.pyspark.enabled is disabled; enabling it will parallelize the groupBy operation
D) The DataFrame should be repartitioned using .coalesce(1) to consolidate shuffle operations onto a single executor

---

**Q21.** A retailer wants to forecast next month's sales using two years of daily historical data with strong seasonal and trend patterns. An analyst uses sklearn.model_selection.train_test_split(df, test_size=0.2, random_state=42) with shuffle=True (the default) to create train and test sets. What is the primary problem with this approach for this modeling goal?

A) random_state=42 is not a valid seed value for time series problems and will throw an error
B) Random shuffling scatters future dates into the training set and past dates into the test set, allowing the model to learn from future information and producing an unrealistically optimistic evaluation
C) test_size=0.2 is too large for time series data; it should always be set below 0.05
D) train_test_split cannot be used on pandas DataFrames and requires converting to a NumPy array first

---

**Q22.** A team registers features in Databricks Feature Store for a fraud detection model. When constructing a training set with FeatureLookup, they must ensure that each labeled transaction only receives feature values that were known at the time the transaction occurred, not values computed afterward. Which mechanism enables this?

A) Setting the feature table's data type to STREAMING so features update continuously
B) Specifying a timestamp_lookup_key in the FeatureLookup so point-in-time correct joins are performed against the feature table's timestamp column
C) Enabling Unity Catalog lineage tracking on the feature table so past values are automatically versioned
D) Using merge_asof() manually inside a Spark UDF after the training set is created

---

**Q23.** Which statement BEST describes why fitting a StandardScaler (or any Spark ML feature transformer) only on the training set, then using .transform() with that already-fitted model on the validation and test sets, is the correct practice to avoid data leakage?

A) Fitting only on training data ensures scaling statistics (mean/standard deviation) come solely from data the model is allowed to learn from, so validation/test performance reflects generalization to unseen data rather than statistics derived from data the model shouldn't have access to
B) It is required only because Spark ML transformers cannot be refit on new data once created
C) Fitting only on the training set reduces the total compute cost since Spark caches the scaler parameters permanently
D) It ensures the validation and test sets have identical row counts to the training set

---

**Q24.** A data scientist is building a customer churn model. Before splitting the data, they compute the mean churn rate per 'customer_region' category using the ENTIRE dataset (including what will become the test set) and use this as a target-encoded feature. The model shows excellent test accuracy but performs poorly in production. What is the BEST fix to eliminate this data leakage while retaining the predictive value of target encoding?

A) Remove the customer_region column entirely, since any encoding derived from it will always leak information
B) Compute the target encoding after splitting, but use the actual test set churn labels to encode the test rows so the mapping matches production data distribution
C) Fit the target encoding only on the training fold (using cross-validation or out-of-fold encoding), then apply the learned category-to-value mapping to the validation and test sets
D) Replace target encoding with one-hot encoding for customer_region, since one-hot encoding is always the superior technique for categorical features

---

**Q25.** A Delta Lake table contains 500 million transaction rows. The ML team needs to perform feature engineering that includes custom Python groupby-apply logic (per customer, computing rolling aggregates with pandas-style syntax), and the transformation must scale across the full dataset without collecting it onto a single machine. Which approach BEST satisfies these requirements?

A) Use the pandas API on Spark (pyspark.pandas) to write pandas-like syntax that executes as distributed Spark jobs across the cluster
B) Call toPandas() on the Spark DataFrame to load the full dataset into a single pandas DataFrame, then use standard pandas groupby-apply
C) Rewrite all groupby-apply logic using only native PySpark DataFrame API and Spark SQL functions, avoiding any pandas syntax
D) Sample 10% of the rows into pandas, perform the groupby-apply transformations on the sample, then extrapolate the results to represent the full dataset

---

**Q26.** A dataset contains multiple transaction rows per customer_id, and the label (churn: yes/no) is defined at the customer level and duplicated across every transaction row belonging to that customer. A data scientist performs a standard random train_test_split directly on the transaction rows. What problem is most likely to occur, and what is the correct fix?

A) The split is invalid because an 80/20 train/test ratio must always be used for binary classification problems on Databricks
B) Random splitting works correctly for this grouped data as long as a fixed random_state is used for reproducibility
C) Rows belonging to the same customer will likely end up in both the train and test sets, causing data leakage; the fix is to perform a group-level split (e.g., splitting by unique customer_id) before generating the row-level train/test sets
D) Stratified splitting by the churn label is required to fix the imbalance, but the grouping structure of the data is unrelated to any leakage concern

---

**Q27.** A data scientist tunes a gradient boosting model using Hyperopt's fmin with SparkTrials on a 32-worker Databricks cluster. They set max_evals=100 and parallelism=32 to fully utilize the cluster. After tuning, the resulting model performs worse than a previous run that used parallelism=8. What is the MOST likely explanation?

A) With parallelism close to max_evals, the Tree-structured Parzen Estimator (TPE) algorithm has fewer completed trials to learn from before launching new trials, causing the search to behave more like random search
B) parallelism=32 exceeds the executor count in the cluster
C) SparkTrials requires parallelism to be a power of 2, so setting it to 32 caused evaluation errors
D) Higher parallelism disables early stopping in the objective function

---

**Q28.** A team is building a binary classifier to detect fraudulent transactions where only 2% of transactions are fraudulent. When comparing MLflow runs to select the best-performing model, which metric should be prioritized?

A) Accuracy
B) Area under Precision-Recall curve (AUC-PR)
C) Area under ROC curve (AUC-ROC)
D) R-squared

---

**Q29.** A dataset of 5 GB fits comfortably into the memory of the driver node on a Databricks cluster with 64 GB of RAM. The team needs to train a random forest classifier and wants the fastest development iteration with full access to scikit-learn's hyperparameter options. Which training approach is BEST suited for this scenario?

A) Train using Spark ML's RandomForestClassifier distributed across the cluster's worker nodes
B) Convert the dataset to a Spark DataFrame and use Pandas UDFs to parallelize scikit-learn training across partitions
C) Train using scikit-learn's RandomForestClassifier on the driver node, since the data fits in memory and single-node processing avoids distributed overhead
D) Use Hyperopt with SparkTrials to distribute the scikit-learn model training across executors for a single model fit

---

**Q30.** After running Hyperopt's fmin with SparkTrials with MLflow autologging enabled, a data scientist wants to programmatically retrieve the single best child run by validation F1 score using the MLflow Python API. Which approach correctly retrieves it?

A) mlflow.search_runs() returns only the parent run created by fmin; individual trial metrics must be manually parsed from the Hyperopt Trials object
B) SparkTrials does not integrate with MLflow autologging, so trials must be logged manually with mlflow.log_metric inside the objective function
C) The best run must be retrieved using mlflow.list_experiments(), since search_runs cannot filter by metric values
D) mlflow.search_runs(order_by=["metrics.val_f1 DESC"]).iloc[0], filtered to the parent experiment, since each Hyperopt trial is logged as a nested child run under the parent fmin run

---

**Q31.** Two regression models are compared for predicting house prices. Model A has a lower RMSE than Model B, but Model B has a lower MAE than Model A. Which statement correctly explains this discrepancy?

A) RMSE and MAE always agree on ranking; this scenario is not possible
B) RMSE is always smaller than MAE for the same dataset, so Model A's lower value is expected regardless of performance
C) MAE only applies to classification problems, so this comparison is invalid
D) RMSE squares errors before averaging, so it penalizes large errors (outliers) more heavily; Model A likely makes fewer large errors while Model B is more consistent on typical predictions

---

**Q32.** A data scientist defines a Hyperopt search space for the max_depth hyperparameter of a gradient boosted tree model, which must be an integer between 3 and 15. Which search space definition is CORRECT?

A) hp.uniform('max_depth', 3, 15)
B) hp.quniform('max_depth', 3, 15, 1)
C) hp.choice('max_depth', [3, 15])
D) hp.loguniform('max_depth', 3, 15)

---

**Q33.** A data scientist configures Hyperopt with SparkTrials for a hyperparameter search: max_evals=20 and parallelism=20 (matching the number of executor cores available). After running the search, the resulting best hyperparameters perform worse than a manually tuned baseline. What is the MOST likely cause?

A) Setting parallelism equal to max_evals forces every trial to run simultaneously with no prior results to learn from, degrading the adaptive (Bayesian) search behavior of TPE
B) SparkTrials cannot be used with the TPE algorithm, so results are effectively random
C) max_evals=20 is too high and caused the driver to run out of memory silently
D) SparkTrials requires max_evals to be a multiple of the number of executors, so 20/20 is an invalid configuration

---

**Q34.** A team has a 2 GB pandas DataFrame that fits comfortably in memory on a single node. They need to run a grid search over 200 hyperparameter combinations for a scikit-learn RandomForestClassifier as fast as possible using a multi-node Databricks cluster. What is the BEST approach?

A) Convert the data and model to Spark ML equivalents (e.g., RandomForestClassifier from pyspark.ml) so training distributes across the cluster
B) Increase the driver node size to the largest available instance and run scikit-learn's GridSearchCV with n_jobs=-1 on the driver only
C) Repartition the data into 200 partitions and run one scikit-learn model per partition, then average the resulting model coefficients
D) Keep the model training in scikit-learn on the pandas DataFrame, but distribute the 200 hyperparameter combinations across the cluster using Hyperopt with SparkTrials (or joblib with the Spark backend)

---

**Q35.** A Spark ML pipeline uses CrossValidator with a ParamGridBuilder that specifies 4 values for regParam and 3 values for elasticNetParam, and numFolds is set to 5. How many total model fits does CrossValidator perform?

A) 12
B) 20
C) 15
D) 60

---

**Q36.** A model is being trained to detect fraudulent transactions where only 0.8% of transactions in the dataset are fraudulent. Stakeholders want a single metric that best reflects performance on the minority (fraud) class without being misleadingly optimistic due to the class imbalance. Which metric should be prioritized?

A) Overall accuracy
B) Area under the ROC curve (ROC-AUC)
C) R-squared (R2)
D) Area under the Precision-Recall curve (PR-AUC / average precision)

---

**Q37.** A data scientist writes a Hyperopt objective function that calls mlflow.start_run() inside fmin's search loop for each trial, without specifying the nested parameter. When they later try to compare trial runs in the MLflow experiment UI, all trial runs appear as separate top-level runs with no parent-child grouping, making it hard to associate them with the single tuning job. What should be changed to fix this?

A) Call mlflow.start_run(nested=True) inside the objective function, wrapped within an outer mlflow.start_run() that represents the overall tuning job
B) Set MLflow's autologging to disabled='False' before calling fmin so nested runs are created automatically
C) Use mlflow.log_param() instead of mlflow.start_run() inside the objective function to avoid creating extra runs
D) Replace fmin with GridSearchCV, which automatically nests runs under a parent run in MLflow

---

**Q38.** Two candidate classification models were evaluated for a loan default prediction use case where missing an actual default (a false negative) is far more costly to the business than incorrectly flagging a good loan as risky (a false positive). Model A has accuracy 91% and recall 0.62. Model B has accuracy 88% and recall 0.85. Which model should be selected for deployment, and why?

A) Model A, because higher overall accuracy always indicates the better model regardless of business context
B) Model A, because a smaller gap between recall and accuracy indicates a more balanced and therefore more trustworthy model
C) Neither — F1 score should be calculated for both and whichever has the higher F1 automatically becomes the correct choice regardless of the stated business cost asymmetry
D) Model B, because it has a higher recall, meaning it correctly identifies more actual defaults, which aligns with minimizing costly false negatives even at the cost of some accuracy

---

**Q39.** A data scientist tunes a scikit-learn RandomForestClassifier using Hyperopt with SparkTrials on a cluster with 8 workers. They configure SparkTrials(parallelism=8) and max_evals=100 with the TPE (Tree of Parzen Estimators) algorithm. After tuning completes, the results are barely better than what plain random search would produce. What is the MOST likely cause?

A) TPE cannot be used with scikit-learn objective functions, only with Spark ML pipelines
B) parallelism=8 is too low; it should be increased to 100 to match max_evals for faster convergence
C) Setting parallelism close to max_evals limits the number of sequential batches TPE can use to learn from prior trials, reducing its adaptive advantage over random search
D) max_evals=100 exceeds the maximum number of evaluations SparkTrials supports, causing silent truncation

---

**Q40.** A team builds a Spark ML pipeline with CrossValidator to detect fraud, where only 2% of transactions are positive. They currently select the best hyperparameters using BinaryClassificationEvaluator with the areaUnderROC metric. The tuned model scores very well on this metric during cross-validation, but in production it misses the majority of actual fraud cases. Which change to the cross-validation setup would give a more reliable metric for hyperparameter selection on this dataset?

A) Increase numFolds in the CrossValidator from its default to 10 for a more stable ROC estimate
B) Switch the evaluator's metric to areaUnderPR (average precision), which is more sensitive to performance on the minority class than areaUnderROC
C) Replace BinaryClassificationEvaluator with RegressionEvaluator using RMSE, since it directly measures prediction error
D) Switch to MulticlassClassificationEvaluator using accuracy so the metric is easier for stakeholders to interpret

---

**Q41.** A fraud-detection team needs a Databricks-hosted model to score individual transactions with p99 latency under 50ms. Traffic is unpredictable, spiking from near-zero to thousands of requests per second during flash sales, and the team wants Databricks to manage infrastructure scaling automatically. Which deployment approach BEST meets these requirements?

A) Deploy the model to a Model Serving endpoint configured for real-time scoring with autoscaling compute
B) Schedule an hourly Databricks Job that runs mlflow.pyfunc.spark_udf over a Delta table of transactions
C) Wrap the model in a Structured Streaming job with a 10-minute trigger interval reading from a Delta table
D) Load the model on an all-purpose interactive cluster and expose it via a notebook-based REST wrapper

---

**Q42.** A retail company has a model registered in Unity Catalog as `retail.models.demand_forecast` version 4. They need to score 500 million rows in a Delta table every night as part of an existing scheduled Spark job, reusing the job's job cluster for distributed compute. Which approach correctly performs this scoring?

A) Call the Model Serving REST endpoint once per row inside a Python for-loop on the driver node
B) Load the model with mlflow.pyfunc.spark_udf(spark, model_uri='models:/retail.models.demand_forecast/4') and apply it as a column expression on the Spark DataFrame
C) Convert the Delta table to a single pandas DataFrame and call model.predict() on the driver
D) Create a real-time Model Serving endpoint and route the batch job's output through it using ai_query() for each partition

---

**Q43.** A Model Serving endpoint is experiencing intermittent latency spikes of several seconds on the first request after periods of inactivity, even though average latency is otherwise well within SLA. The team requires consistently low latency at all times, including immediately after idle periods. Which configuration change addresses this?

A) Set a minimum provisioned concurrency greater than zero so the endpoint does not scale to zero during idle periods
B) Increase the number of Delta table partitions feeding the model's training data
C) Switch the endpoint's compute type from CPU to GPU
D) Enable autoscaling on the endpoint with a higher maximum concurrency limit

---

**Q44.** A company ingests clickstream events continuously from a Kafka topic and needs to enrich each event with a fraud score from an MLflow-logged model as part of an always-on Delta Live Tables / Structured Streaming pipeline, writing scored results to a Delta table with near-real-time freshness. Which approach is most appropriate?

A) Export the model as an ONNX file and manually deploy it inside the Kafka broker configuration
B) Run a nightly batch job that reads the full Kafka topic history and applies mlflow.pyfunc.load_model on the driver
C) Create a Model Serving endpoint and have every downstream Kafka consumer issue an individual synchronous REST call per event before writing to Delta
D) Load the model with mlflow.pyfunc.spark_udf and apply it as a column transformation within a Structured Streaming readStream/writeStream pipeline

---

**Q45.** In Unity Catalog's Model Registry, a data scientist wants to load whichever model version currently has the `Champion` alias assigned for use in a production scoring job, so that promoting a new version later requires no code change. Which model URI reference achieves this?

A) models:/catalog.schema.model_name/Production
B) models:/catalog.schema.model_name/Staging
C) models:/catalog.schema.model_name/latest
D) models:/catalog.schema.model_name@Champion

---

