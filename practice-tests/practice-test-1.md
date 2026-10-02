# Practice Test 1: Databricks ML Associate Certification: Exam Prep

- **Questions:** 45
- **Time limit:** 90 minutes
- **Passing score:** 70%%

> Answers are in `answer-key-1.md`. Track results in `score-tracker.md`.

---

**Q1.** A data scientist has a labeled classification dataset in a Delta table and wants to quickly generate a baseline model along with the full source code used to train it, so they can inspect and customize the winning approach. Which Databricks capability BEST meets this need?

A) Launch a Databricks AutoML classification experiment, which generates trial notebooks you can customize
B) Manually write scikit-learn training code from scratch in a notebook
C) Use Databricks Feature Store to automatically train a baseline model
D) Use MLflow Model Registry to auto-generate a baseline model

---

**Q2.** Several teams across an organization need to reuse the same curated customer features for multiple models, require point-in-time correct joins when building training sets, and want automatic lineage tracking from features back to source tables and forward to trained models. Which Databricks capability BEST satisfies these requirements?

A) Store features in a plain Delta Lake table with no additional tooling
B) Save features as MLflow artifacts inside each experiment run
C) Store features as files in a Unity Catalog volume
D) Create feature tables in Feature Engineering in Unity Catalog (Databricks Feature Store) for point-in-time lookups and lineage

---

**Q3.** A data scientist is training a scikit-learn model and wants MLflow to automatically capture parameters, metrics, and the trained model as an artifact without manually calling logging functions for each item. Which single line should be added before training starts?

A) mlflow.autolog()
B) mlflow.set_experiment()
C) mlflow.log_artifact()
D) mlflow.start_run()

---

**Q4.** A company runs Databricks in multiple workspaces and wants a single, centrally governed place to register model versions, enforce access controls, and track lineage across all teams. Which approach BEST meets this requirement?

A) Register models in each workspace's local MLflow Model Registry independently
B) Store model binaries directly in the DBFS root of one workspace
C) Register models in Unity Catalog using the three-level catalog.schema.model namespace for centralized governance
D) Register the model as a feature table in Feature Store

---

**Q5.** Which statement correctly describes the relationship between MLflow experiments and runs?

A) A run can contain multiple experiments, each with its own parameters
B) An experiment is a container for multiple runs, and each run logs its own parameters, metrics, tags, and artifacts
C) Experiments store only metrics while runs store only parameters
D) Each experiment can contain only a single run

---

**Q6.** A team needs to train a deep learning model using TensorFlow and PyTorch with GPU acceleration, and wants the cluster to come with these frameworks and drivers preinstalled to minimize setup. Which cluster configuration should they choose?

A) A standard Databricks Runtime cluster with CPU-only instances
B) A Databricks SQL warehouse
C) A Photon-enabled cluster running the standard (non-ML) Databricks Runtime
D) A Databricks Runtime for Machine Learning cluster with GPU instance types, which includes preinstalled TensorFlow, PyTorch, and Horovod

---

**Q7.** A data science team stores customer features in a Unity Catalog feature table named 'customer_features' with primary key 'customer_id'. They want to build a training set by joining these features to a labels DataFrame using the Feature Engineering client. Which approach correctly retrieves the features for training?

A) Manually write a Spark SQL JOIN between the labels DataFrame and the feature table on customer_id
B) Load the feature table with spark.read.table() and use pandas merge() locally on the driver
C) Use fe.create_training_set() with a FeatureLookup specifying table_name='customer_features' and lookup_key='customer_id', then call .load_df()
D) Register the feature table as an MLflow artifact and load it inside the training run

---

**Q8.** A team registers a model in the Unity Catalog Model Registry and wants to designate the current production-serving version without using workspace-model-registry 'Stages' (Staging/Production), which are not supported in Unity Catalog. What mechanism should they use instead?

A) Model version aliases, e.g., client.set_registered_model_alias(name, 'champion', version)
B) Model tags applied via mlflow.set_tag()
C) The model's description field edited in the UI
D) A separate MLflow experiment named 'production'

---

**Q9.** A data scientist has a labeled tabular dataset and wants Databricks to automatically try multiple algorithms and hyperparameter configurations, then generate an editable source-code notebook for the best trial. Which Databricks capability should they use?

A) Feature Engineering in Unity Catalog
B) MLflow Model Registry
C) Databricks AutoML
D) Databricks SQL Alerts

---

**Q10.** A data scientist is training a scikit-learn model inside a Databricks notebook and wants parameters, metrics, and the model artifact automatically logged to MLflow without adding explicit mlflow.log_param or mlflow.log_metric calls. What should they add before training?

A) mlflow.set_experiment('/Shared/experiment')
B) mlflow.start_run(nested=True)
C) mlflow.log_model(model, 'model')
D) mlflow.autolog()

---

**Q11.** A team needs to train a deep learning model using GPU-accelerated PyTorch on a Databricks cluster. What is the primary reason they should select Databricks Runtime for Machine Learning (ML Runtime) instead of the Standard Runtime for this cluster?

A) ML Runtime clusters have unlimited autoscaling while Standard Runtime clusters do not
B) ML Runtime is required to access Unity Catalog tables, while Standard Runtime cannot query Unity Catalog
C) ML Runtime comes preinstalled with GPU drivers, CUDA libraries, and ML frameworks like PyTorch, TensorFlow, and XGBoost
D) ML Runtime clusters are billed at a lower DBU rate than Standard Runtime clusters

---

**Q12.** A feature table logs feature values with an 'as_of_timestamp' column, and features change over time. When creating a training set, the team must ensure each label row only receives feature values that were valid at or before that row's event time, to prevent data leakage. Which FeatureLookup parameter should they configure to achieve this?

A) Set timestamp_lookup_key to the column identifying each label row's event time for a point-in-time join
B) Set additional_feature_names to include as_of_timestamp explicitly
C) Filter the feature table manually with a WHERE clause before calling create_training_set
D) Use Delta Lake time travel with a VERSION AS OF clause on the feature table

---

**Q13.** A data science team wants to create a Feature Store table in Unity Catalog so that engineered features can be reused across multiple training pipelines and automatically joined at training time. Which requirement MUST be met when creating this feature table?

A) The table must define one or more primary key columns to enable feature lookups
B) The table must be created using only pandas DataFrames, never Spark DataFrames
C) The table must be stored in DBFS root and cannot be registered in Unity Catalog
D) The table must contain the label column so it can be used directly as training data

---

**Q14.** An ML engineer has a feature table registered in Unity Catalog and a labeled DataFrame containing raw training rows plus a primary key column. She wants to automatically join the corresponding feature values into the training data before fitting a model, and later have those same feature values retrieved automatically at inference time. Which Databricks capability should she use to define this join logic?

A) The FeatureLookup API combined with create_training_set()
B) MLflow Model Registry aliases
C) AutoML's data exploration notebook
D) A manual SQL JOIN executed in a separate notebook before every training run

---

**Q15.** A data scientist calls mlflow.autolog() at the top of a notebook and then trains a scikit-learn RandomForestClassifier. She wants MLflow to still automatically log parameters and metrics for each run, but she does NOT want MLflow to automatically log the trained model artifact itself, because she plans to log a custom-packaged model manually later in the same run. What should she change in her autologging call?

A) Call mlflow.autolog(log_models=False)
B) Call mlflow.end_run() immediately after mlflow.autolog()
C) Remove mlflow.autolog() entirely and only use mlflow.log_param()
D) Set mlflow.autolog(disable=True) and log everything manually including params and metrics

---

**Q16.** A team registers a model as a Unity Catalog model named 'prod.ml_models.churn_predictor' instead of using the legacy Workspace Model Registry. Which statement correctly describes how this model is referenced and governed?

A) It is referenced only by a numeric model ID and has no catalog association
B) It must remain in the same workspace where it was trained and cannot be accessed from other workspaces
C) It is referenced using a three-level namespace (catalog.schema.model) and inherits Unity Catalog access controls and lineage
D) It is automatically converted to a Delta Live Table pipeline for governance

---

**Q17.** After running an AutoML classification experiment, a data scientist opens the notebook generated for the best trial to understand and potentially modify the winning model. Which of the following does this AutoML-generated notebook provide out of the box?

A) Editable source code reproducing the best trial's preprocessing and model training steps, along with logged MLflow run metrics
B) A fully deployed real-time serving endpoint with autoscaling already configured
C) A Unity Catalog Feature Store table containing only the features AutoML judged most important
D) A Delta Live Table pipeline definition for retraining the model on a schedule

---

**Q18.** A data scientist is preparing a dataset for a linear regression model on Databricks. They plan to scale all numeric features using StandardScaler before training. Which approach BEST avoids data leakage during this process?

A) Fit StandardScaler on the entire dataset, then split the scaled data into train and test sets
B) Fit and apply StandardScaler separately on the train set and again separately on the test set
C) Skip scaling since Databricks automatically normalizes all numeric columns during model training
D) Split the data into train and test sets first, fit StandardScaler only on the training set, then apply that same fitted transformation to both the training and test sets

---

**Q19.** A team has an existing pandas-based feature engineering pipeline that runs fine on a 5GB dataset but now needs to process a 500GB Delta Lake table on Databricks, with minimal rewriting of existing pandas-style code. Which approach BEST meets this requirement?

A) Convert the entire 500GB dataset to a pandas DataFrame using toPandas() and process it in batches on the driver node
B) Increase the driver node size until it can hold 500GB in memory and continue running the pandas pipeline unmodified
C) Use the pandas API on Spark (pyspark.pandas) to run the existing pandas-style code across a distributed cluster
D) Rewrite the entire pipeline from scratch using the native PySpark DataFrame API and Spark SQL functions

---

**Q20.** A dataset column 'income' has 12% missing values due to a data collection glitch (missing completely at random) and the column is right-skewed. The team wants to impute the missing values while minimizing distortion of the overall distribution. Which imputation strategy is BEST?

A) Impute missing values using the column mean
B) Impute missing values using the column median
C) Drop all rows containing missing values in the income column
D) Impute missing values using a randomly sampled value from the existing distribution

---

**Q21.** A classification dataset has a highly imbalanced target variable (95% class 0, 5% class 1). When splitting the data using scikit-learn's train_test_split, which parameter should be set to ensure both the train and test sets maintain the same class proportions?

A) Set shuffle=True when calling train_test_split
B) Set random_state=42 when calling train_test_split
C) Set stratify=y when calling train_test_split
D) Increase test_size to 0.5 when calling train_test_split

---

**Q22.** A dataset contains a categorical feature 'city' with 500 unique values. The team plans to train a gradient boosted tree model (e.g., LightGBM) using this feature. Which encoding approach is generally preferred to avoid an excessive increase in dimensionality?

A) One-hot encode the city column, creating one binary column per unique value
B) Use label/ordinal encoding or target encoding to represent city as a single numeric column
C) Leave the city column as raw text strings and let the model infer numeric relationships directly
D) Convert the city column into a fixed 8-bit binary string representation of each category

---

**Q23.** A company wants to build a model to forecast next month's sales using historical, time-ordered transaction data. Which train/test splitting approach avoids data leakage in this scenario?

A) Randomly shuffle all records and split 80/20 into train and test sets
B) Use k-fold cross-validation with shuffling enabled across all folds
C) Split the data chronologically, using earlier time periods for training and later time periods for testing
D) Split the data by randomly sampling records stratified on the target sales value

---

**Q24.** A data scientist is preparing a dataset for a regression model. Before splitting the data, they fit a StandardScaler on the entire dataset and then use the scaled features to create train, validation, and test sets. The model performs very well on the test set but poorly in production. What is the MOST likely cause of this discrepancy?

A) StandardScaler is not compatible with regression models and should only be used for classification
B) The StandardScaler was fit on the full dataset before splitting, causing data leakage from the test set into training
C) The model was trained on too few features, causing it to underfit the production data
D) The train/validation/test split ratio was incorrect for the size of the dataset

---

**Q25.** A data scientist has an existing pandas script that performs feature engineering (groupby aggregations, string operations, and rolling window calculations) on a dataset that now needs to scale to 500GB stored in Delta Lake. The team wants to reuse as much of the existing pandas syntax as possible while distributing the computation across a Spark cluster. Which approach should they use?

A) Rewrite all pandas operations using the PySpark DataFrame API and its native functions
B) Convert the Delta table to a single pandas DataFrame using toPandas() and run the script unmodified
C) Use the pandas API on Spark (pyspark.pandas) to run the existing logic with minimal code changes
D) Use pandas UDFs exclusively to wrap the entire script as one large distributed function

---

**Q26.** A data scientist is using Spark MLlib's Imputer transformer to fill in missing values in a numeric column using the median of the observed values. Which value should the 'strategy' parameter be set to?

A) mode
B) median
C) most_frequent
D) mean

---

**Q27.** A data scientist is training a binary classifier to detect fraudulent transactions where only 0.5% of transactions are fraudulent. The model achieves 99.4% accuracy but fails to catch almost any fraud cases. Which evaluation metric should the data scientist prioritize instead to properly assess this model?

A) R-squared, since it accounts for variance explained by the model
B) F1 score or area under the precision-recall curve, since they account for class imbalance
C) Accuracy, but measured on a larger held-out test set
D) Mean absolute error, since it penalizes large prediction errors

---

**Q28.** A data scientist is running Hyperopt to tune hyperparameters for a scikit-learn model and wants each hyperparameter trial to run in parallel across the worker nodes of a Databricks cluster. Which Hyperopt component should be passed as the `trials` argument to `fmin()`?

A) Trials()
B) GridSearchCV()
C) SparkTrials()
D) ParamGridBuilder()

---

**Q29.** A data scientist wants each hyperparameter combination evaluated during a Hyperopt sweep to be logged as its own separate MLflow run, grouped under a single parent run that represents the entire tuning job. Which MLflow configuration accomplishes this?

A) Call mlflow.autolog() once before fmin() and take no further action
B) Create a new MLflow experiment for every trial evaluated by Hyperopt
C) Set mlflow.start_run(nested=True) inside the objective function while an outer run is active
D) Log all trial metrics into one run using mlflow.log_metrics() with a step parameter

---

**Q30.** Which statement correctly describes the difference between grid search and random search for hyperparameter tuning?

A) Grid search always converges to a better model than random search regardless of the number of hyperparameters
B) Grid search exhaustively evaluates every combination in a predefined parameter grid, while random search samples a fixed number of combinations from specified distributions
C) Random search requires a validation set while grid search does not
D) Grid search can only be used with Spark ML pipelines, while random search can only be used with scikit-learn

---

**Q31.** A dataset contains 500 million rows and does not fit into the memory of a single driver node. The data scientist wants to train a regression model directly on this data without subsampling. Which approach is most appropriate on Databricks?

A) Load the data into a pandas DataFrame using pandas-on-Spark and train with scikit-learn's LinearRegression
B) Build a Spark ML pipeline with a distributed estimator such as LinearRegression from pyspark.ml.regression, which trains directly on the distributed DataFrame
C) Randomly subsample the data to fit in driver memory, then train with scikit-learn
D) Collect the full DataFrame to the driver using .toPandas() and then train with scikit-learn

---

**Q32.** A data scientist wants to perform k-fold cross-validation over a grid of hyperparameters for a Spark ML pipeline that includes a VectorAssembler and a classifier. Which combination of Spark ML classes should be used to build and execute this tuning workflow?

A) TrainValidationSplit alone, without a parameter grid, using a single fixed train/validation split
B) ParamGridBuilder to define the hyperparameter grid, combined with CrossValidator wrapping the Pipeline and an Evaluator
C) Hyperopt's SparkTrials combined with mlflow.spark.autolog()
D) GridSearchCV from scikit-learn applied directly to the Spark ML Pipeline object

---

**Q33.** A data scientist is tuning hyperparameters for a scikit-learn model using Hyperopt on a Databricks cluster with 8 worker nodes. The model training itself runs quickly on a single node, but the data scientist wants to evaluate many hyperparameter configurations in parallel across the cluster. Which approach should be used?

A) Use fmin with the default Trials class, which automatically distributes trials across worker nodes
B) Use scikit-learn GridSearchCV with n_jobs=-1 to parallelize across the cluster
C) Convert the model to Spark ML and use CrossValidator with parallelism
D) Use fmin with SparkTrials, specifying parallelism to distribute hyperparameter evaluations across the cluster

---

**Q34.** When using Hyperopt's SparkTrials for hyperparameter tuning, what does the parallelism parameter control, and what is a key tradeoff of setting it too high relative to max_evals?

A) It controls the number of Spark executors used; setting it higher than available executors causes the job to fail
B) It controls the number of cross-validation folds; setting it too high increases training time linearly
C) It controls the number of Hyperopt algorithm restarts; setting it too high causes the search to converge prematurely
D) It controls how many trials are evaluated concurrently; setting it too high reduces the benefit of adaptive algorithms like TPE, since fewer completed trials are available to inform subsequent choices

---

**Q35.** A binary classification model is being built to detect fraudulent transactions, where only 0.5% of transactions are fraudulent. Which evaluation metric should be prioritized when comparing MLflow runs for this model?

A) Area under the precision-recall curve (AUC-PR), since it better reflects performance on the minority class
B) Accuracy, since it directly measures overall correctness
C) R-squared, since it measures the proportion of variance explained by the model
D) Area under the ROC curve (AUC-ROC), since it is unaffected by class imbalance

---

**Q36.** In Spark ML, what is the primary difference between CrossValidator and TrainValidationSplit when tuning hyperparameters?

A) CrossValidator only supports classification models, while TrainValidationSplit supports both classification and regression
B) CrossValidator can only be used with a ParamGridBuilder, while TrainValidationSplit requires Hyperopt's SparkTrials
C) CrossValidator runs on a single node, while TrainValidationSplit distributes computation across the cluster
D) CrossValidator evaluates each parameter combination using k-fold cross-validation, while TrainValidationSplit evaluates each parameter combination using a single train/validation split, making TrainValidationSplit faster but less robust

---

**Q37.** A data scientist trains multiple scikit-learn models with different hyperparameters inside a Databricks notebook and wants MLflow to automatically log parameters, metrics, and the model artifact for each run without adding explicit mlflow.log_param or mlflow.log_metric calls. What should be done at the start of the notebook?

A) Call mlflow.sklearn.autolog() once before training the models
B) Set the environment variable MLFLOW_TRACKING_URI to enable automatic logging
C) Manually wrap each training call with mlflow.start_run() and rely on default parameter logging
D) Use mlflow.log_model() inside a loop for each model configuration

---

**Q38.** A team is training a gradient boosted tree model using scikit-learn's GradientBoostingClassifier on a dataset that has grown to 500 GB and no longer fits in the memory of a single driver node, causing out-of-memory errors. Which approach should the team take to continue training on this data within Databricks?

A) Increase n_jobs in GradientBoostingClassifier to use all cores on the driver node
B) Switch to Spark ML's GBTClassifier, which distributes training across the cluster and can handle data that does not fit on a single node
C) Repartition the DataFrame to more partitions and continue using GradientBoostingClassifier as-is
D) Sample 10% of the dataset randomly and continue using GradientBoostingClassifier so it fits in driver memory

---

**Q39.** A data scientist is tuning a scikit-learn RandomForestClassifier using Hyperopt on a Databricks cluster with 8 worker nodes. The model training function itself runs quickly on a single node, but the data scientist wants to evaluate many hyperparameter combinations in parallel across the cluster to reduce total tuning time. Which Hyperopt configuration should be used?

A) Use fmin with SparkTrials, specifying parallelism equal to the number of desired concurrent evaluations
B) Use fmin with the default Trials class and increase max_evals
C) Wrap the RandomForestClassifier in a Spark ML Pipeline so Hyperopt distributes it automatically
D) Use fmin with the default Trials class and set the tpe.suggest algorithm to run in parallel mode

---

**Q40.** A team trains a binary classification model to detect fraudulent transactions. The dataset has 99,000 legitimate transactions and 1,000 fraudulent transactions. After training, the model achieves 99% accuracy but catches almost none of the actual fraud cases. Which evaluation metric should the team prioritize instead to properly assess the model for this use case?

A) R-squared (R2)
B) Root Mean Squared Error (RMSE)
C) Mean Absolute Error (MAE)
D) F1 score or recall on the fraud (minority) class

---

**Q41.** A retail company needs to score credit-card transactions for fraud in under 100 milliseconds as each transaction occurs, so a decision can be returned to the point-of-sale system synchronously. Which Databricks deployment approach BEST meets this requirement?

A) Register the model in Unity Catalog and run a nightly batch job with mlflow.pyfunc.spark_udf to score all transactions
B) Load the model into a Structured Streaming job that microbatches every 5 minutes
C) Attach the model to an all-purpose cluster and query it interactively through a notebook
D) Deploy the model to a Databricks Model Serving endpoint and call it via the REST API for each transaction

---

**Q42.** A data scientist has a registered MLflow model and needs to apply it to score 500 million rows stored in a Delta table as part of a scheduled daily job, distributing the work across a cluster. Which approach should be used?

A) Wrap the model with mlflow.pyfunc.spark_udf and apply it as a column expression on the Spark DataFrame
B) Deploy a Model Serving endpoint and send each row as an individual REST API call from the driver
C) Load the model with pandas and call model.predict() on the driver after collecting the DataFrame
D) Convert the model to ONNX and run it exclusively on the driver node in a single Python process

---

**Q43.** In Unity Catalog, which URI pattern correctly loads a model version tagged with the alias 'champion' for use with mlflow.pyfunc.load_model?

A) models:/catalog.schema.model_name/champion
B) models:/catalog.schema.model_name/Production
C) runs:/champion/model
D) models:/catalog.schema.model_name@champion

---

**Q44.** Which statement correctly distinguishes batch inference from streaming inference on Databricks?

A) Batch inference always requires a Model Serving endpoint, while streaming inference never does
B) Batch inference can only be performed using pandas, while streaming inference can only be performed using Spark
C) Streaming inference guarantees lower latency than real-time Model Serving endpoints in all cases
D) Batch inference processes a finite, bounded dataset on a schedule or on-demand, while streaming inference continuously processes new data as it arrives using Structured Streaming

---

**Q45.** A company continuously ingests IoT sensor readings into a Delta table and wants predictions generated automatically and incrementally as new files land, without re-scoring data that was already processed. Which approach BEST fits this requirement?

A) Schedule a full-table batch job with mlflow.pyfunc.spark_udf that re-reads and re-scores the entire table every hour
B) Use Structured Streaming with Auto Loader to read new files incrementally and apply the model as a UDF within the streaming query
C) Deploy a Model Serving endpoint and manually POST each new sensor reading from an external script
D) Load the entire table into a pandas DataFrame on the driver each time new files arrive and call model.predict()

---

