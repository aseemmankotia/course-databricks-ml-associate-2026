## Chapter 2: AutoML: Baseline Models and Generated Notebooks — Practice Questions

**Q1.** A data scientist has a Delta table with 500,000 rows and a continuous target column representing house sale price. They want the fastest way to get a baseline model with generated source code. Which AutoML problem type should they select in the UI?

A) Classification
B) Regression
C) Forecasting
D) Clustering

<details>
<summary>Answer</summary>

**Correct: B**

The target is continuous (price), which is the defining signal for a regression problem type in AutoML.

**Why the others are wrong:**
- Classification is for categorical/discrete labels, not continuous price values.
- Forecasting requires a time column and produces future-period predictions, not a single-row price prediction.
- Clustering is unsupervised and is not one of the three AutoML-supported problem types.

</details>

---

**Q2.** A retail team has two years of daily sales with a valid timestamp column but several missing days. They want AutoML to forecast the next 30 days. What must be true before launching the AutoML forecasting experiment?

A) The time column must represent a regular interval, so gaps must be resampled/filled before training
B) The dataset must be converted to a pandas DataFrame first
C) AutoML forecasting automatically ignores missing timestamps with no data prep needed
D) Forecasting requires a categorical target column instead of a numeric one

<details>
<summary>Answer</summary>

**Correct: A**

AutoML forecasting expects a time column at a consistent frequency; irregular gaps must be handled (resampled/imputed) before launching the experiment or results will be unreliable.

**Why the others are wrong:**
- AutoML forecasting works directly on a Spark DataFrame/table; conversion to pandas is not a requirement.
- Gaps are not automatically handled correctly by AutoML — irregular frequency degrades the underlying Prophet/ARIMA models.
- Forecasting targets are numeric (e.g., sales volume), not categorical.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A team wants to use Databricks AutoML to train a deep learning image classifier for defect detection on product photos. What is the correct outcome?

A) AutoML will automatically select a CNN architecture and tune it
B) AutoML does not support deep learning or image classification tasks
C) AutoML supports it only if run on a GPU cluster
D) AutoML supports it only through the forecasting API

<details>
<summary>Answer</summary>

**Correct: B**

AutoML's search space is limited to classical ML libraries (sklearn, xgboost, lightgbm) plus Prophet/ARIMA for forecasting — it does not train neural networks or handle image data.

**Why the others are wrong:**
- AutoML never searches CNN or any deep learning architecture space.
- A GPU cluster does not unlock deep learning support in AutoML — the limitation is in the algorithm search space, not compute.
- Forecasting is for time series, unrelated to image classification.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** Which Python API call correctly launches an AutoML classification experiment with a 30-minute time budget?

A) automl.classify(train_df, target_col='label', timeout_minutes=30)
B) automl.regress(train_df, target_col='label', timeout_minutes=30)
C) mlflow.autolog(target_col='label', timeout=30)
D) automl.forecast(train_df, target_col='label', horizon=30)

<details>
<summary>Answer</summary>

**Correct: A**

automl.classify() is the correct function for a classification problem, and timeout_minutes is the parameter controlling the time budget.

**Why the others are wrong:**
- automl.regress() is for continuous targets, not classification.
- mlflow.autolog() is a generic MLflow logging utility, not the AutoML launcher API and doesn't take these parameters.
- automl.forecast() is for time series problems and uses horizon/time_col, not a plain target_col classification setup.

</details>

---

**Q5.** In the AutoML experiment UI, which generated artifact lets you see exactly what feature engineering and model code produced the baseline results for each trial?

A) The auto-generated source notebook linked from each leaderboard row
B) The MLflow model registry entry
C) The cluster event log
D) The Delta Lake transaction log for the input table

<details>
<summary>Answer</summary>

**Correct: A**

AutoML generates a full, editable source notebook for every trial, showing the exact feature engineering steps and model training code, accessible from the leaderboard.

**Why the others are wrong:**
- The model registry stores versioned model artifacts, not the feature engineering/training code.
- The cluster event log tracks cluster lifecycle events, not model code.
- The Delta transaction log tracks table commits, unrelated to model training code.

</details>

---

**Q6.** You need AutoML to ignore a customer_id column so it isn't used as a training feature. Which approach is correct?

A) Rename the column to 'id' and AutoML will automatically exclude it
B) Delete the column from the Delta table permanently before every run
C) Pass the column name in the exclude_cols parameter (or deselect it in UI Advanced Configuration)
D) Set primary_metric to 'exclude' for that column

<details>
<summary>Answer</summary>

**Correct: C**

exclude_cols (API) or deselecting the column checkbox under Advanced Configuration (UI) is the supported way to remove a column from feature consideration.

**Why the others are wrong:**
- Renaming to 'id' has no special exclusion behavior in AutoML.
- Permanently deleting the column removes it from the source data entirely, which is unnecessary and destructive.
- primary_metric controls the optimization metric, not column inclusion/exclusion.

</details>

---

**Q7.** What is the default primary metric AutoML uses to rank trials in a classification experiment's leaderboard?

A) R2
B) F1 score
C) SMAPE
D) Silhouette score

<details>
<summary>Answer</summary>

**Correct: B**

F1 (weighted) is the default primary metric for AutoML classification experiments.

**Why the others are wrong:**
- R2 is the default metric for regression experiments, not classification.
- SMAPE is the default metric for forecasting experiments.
- Silhouette score is used for clustering evaluation, which AutoML does not support.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** What does clicking 'Edit best model' (or exporting the best trial's notebook) in an AutoML experiment allow you to do?

A) Deploy the model directly to a REST endpoint with no code changes
B) Get an editable, cloned copy of the generated notebook so you can modify feature engineering or hyperparameters and re-run it
C) Automatically retrain the model on a larger dataset with no user action
D) Convert the model to a deep learning architecture for further tuning

<details>
<summary>Answer</summary>

**Correct: B**

The exported/cloned notebook contains the full, editable code for the best trial, letting you modify preprocessing, features, or hyperparameters and re-run training manually.

**Why the others are wrong:**
- Editing the notebook does not itself deploy anything — serving is a separate manual step.
- Retraining on new data requires you to explicitly change the notebook/data source and re-run it; it isn't automatic.
- AutoML-generated notebooks use classical ML libraries; there is no built-in conversion to deep learning.

</details>

---

