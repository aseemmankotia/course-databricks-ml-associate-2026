## Chapter 4: MLflow Tracking, Model Registry, and Unity Catalog Governance — Practice Questions

**Q1.** A data scientist is running 50 hyperparameter tuning trials for a random forest model and wants to compare accuracy and F1 score across all trials in one place, along with the exact parameters used for each trial. Which approach BEST meets this requirement?

A) Wrap each trial in mlflow.start_run(), logging params with mlflow.log_param and metrics with mlflow.log_metric
B) Print the metrics to the notebook output cell for each trial
C) Save all results to a single CSV file using pandas at the end of the loop
D) Use mlflow.autolog() only, since it captures everything automatically with no code changes

<details>
<summary>Answer</summary>

**Correct: A**

Explicitly starting a run per trial and logging params/metrics gives a queryable, comparable record in the MLflow Tracking UI for each trial, which is the intended workflow the exam tests.

**Why the others are wrong:**
- Printed output is not persisted, queryable, or comparable across runs.
- A CSV file loses the tight coupling to code version, environment, and artifacts that MLflow tracking provides.
- Autologging works for supported frameworks and hyperparameters passed to fit(), but does not guarantee custom metrics like a manually computed F1 score are captured unless logged explicitly.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A team registers a model in Unity Catalog and needs to designate the current production-serving version so downstream jobs always reference the latest approved version without hardcoding a version number. What should they do?

A) Move the model version to the 'Production' stage
B) Set the 'Champion' alias on the approved model version using set_registered_model_alias
C) Rename the model version to include the word 'production'
D) Tag the model version with a 'prod=true' key-value pair only

<details>
<summary>Answer</summary>

**Correct: B**

Unity Catalog model registry uses mutable aliases (e.g., Champion, Challenger) instead of legacy stages. Referencing models by alias (models:/catalog.schema.model@Champion) lets downstream code always point to the current approved version.

**Why the others are wrong:**
- Stages (Staging/Production/Archived) are part of the legacy workspace Model Registry and are not supported for models registered in Unity Catalog.
- Renaming does not create a stable, programmatically referenceable pointer and breaks naming conventions.
- Tags are useful metadata but are not resolved by the model URI syntax the way aliases are, so downstream jobs cannot use them to fetch a version automatically.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A company wants a model retraining notebook to run automatically every Sunday at 2 AM and send an alert if the run fails. Which Databricks feature should they configure?

A) A Databricks Job with a cron-based schedule and email/webhook notifications on failure
B) A Databricks Repo with a scheduled Git pull
C) An always-running all-purpose interactive cluster with a manual trigger
D) mlflow.autolog() configured with a retry policy

<details>
<summary>Answer</summary>

**Correct: A**

Databricks Jobs support cron scheduling for tasks and built-in notification settings (email, webhook) for start/success/failure events, exactly matching the requirement.

**Why the others are wrong:**
- Repos handle Git-based version control and collaboration, not scheduled execution.
- An always-running interactive cluster with manual triggering does not satisfy the automatic weekly schedule requirement and wastes cost.
- mlflow.autolog() only controls logging behavior during training; it has no scheduling or notification capability.

</details>

---

**Q4.** A team of three data scientists needs to collaboratively edit notebooks, create feature branches, and merge changes via pull requests, mirroring standard software engineering workflows, directly within the Databricks workspace. Which Databricks feature should they use?

A) DBFS mounted storage
B) Unity Catalog volumes
C) Databricks Repos
D) MLflow Model Registry

<details>
<summary>Answer</summary>

**Correct: C**

Repos integrate Git providers (GitHub, GitLab, Azure DevOps, Bitbucket) directly into the workspace, enabling branching, committing, pulling, and pull requests on notebooks and code.

**Why the others are wrong:**
- DBFS is a distributed file system for data storage, not source control.
- Unity Catalog volumes govern access to non-tabular files, unrelated to Git collaboration.
- Model Registry manages model lifecycle and versions, not source code collaboration.

</details>

---

**Q5.** A governance team needs centralized access control, lineage tracking, and a three-level namespace (catalog.schema.model) for all machine learning models across the organization. Which capability provides this?

A) The legacy workspace Model Registry
B) MLflow Tracking Server experiments
C) DBFS root storage for pickled models
D) Models in Unity Catalog

<details>
<summary>Answer</summary>

**Correct: D**

Registering models in Unity Catalog places them under the catalog.schema.model three-level namespace, inheriting Unity Catalog's centralized access controls, auditing, and lineage across workspaces.

**Why the others are wrong:**
- The legacy workspace registry is workspace-scoped, lacks Unity Catalog governance and cross-workspace lineage.
- MLflow Tracking experiments store runs, params, and metrics, not governed, versioned model artifacts.
- DBFS root storage has no built-in access control model, versioning, or lineage tracking.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** A data scientist adds a single line, mlflow.sklearn.autolog(), before calling model.fit() on a RandomForestClassifier. Which of the following is automatically logged as a result?

A) Only the final trained model file, with no parameters or metrics
B) Only custom business metrics defined by the user
C) Nothing, because autologging requires an active mlflow.start_run() wrapped in a separate try/except block
D) Model hyperparameters, training metrics, and the fitted model artifact, without any manual mlflow.log_* calls

<details>
<summary>Answer</summary>

**Correct: D**

autolog() for supported libraries like scikit-learn automatically captures model hyperparameters, training metrics, and the model artifact, and it implicitly starts a run if one isn't already active.

**Why the others are wrong:**
- It captures far more than just the model file, including params and metrics from fit().
- Custom business metrics require explicit mlflow.log_metric calls; autolog does not know about metrics you define outside the framework's API.
- autolog automatically manages the run lifecycle; an explicit try/except is not required.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** Which code snippet correctly assigns the 'Challenger' alias to version 4 of a model registered as 'main.ml_models.churn_predictor' in Unity Catalog?

A) mlflow.transition_model_version_stage(name='main.ml_models.churn_predictor', version=4, stage='Challenger')
B) client.set_registered_model_alias(name='main.ml_models.churn_predictor', alias='Challenger', version=4)
C) mlflow.set_tag('challenger', 'main.ml_models.churn_predictor', 4)
D) client.rename_registered_model(name='main.ml_models.churn_predictor', new_name='Challenger_v4')

<details>
<summary>Answer</summary>

**Correct: B**

The MlflowClient.set_registered_model_alias(name, alias, version) method is the correct API for assigning aliases like Champion or Challenger to a specific model version in Unity Catalog.

**Why the others are wrong:**
- transition_model_version_stage is a legacy workspace registry API and does not apply to Unity Catalog models, nor does it accept arbitrary alias strings like 'Challenger' as a stage.
- mlflow.set_tag applies tags to active runs, not aliases to registered model versions.
- rename_registered_model changes the model's name entirely, which is destructive and unrelated to alias assignment.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** What is the fundamental difference between mlflow.log_param and mlflow.log_metric?

A) log_param records static, non-numeric configuration inputs to a run, while log_metric records numeric values that can be logged multiple times over training steps
B) log_param stores artifacts like plots, while log_metric stores only scalar numbers
C) log_param can only be called once per run, while log_metric can be called unlimited times
D) There is no functional difference; both are aliases for the same underlying API call

<details>
<summary>Answer</summary>

**Correct: A**

Params are key-value inputs (e.g., learning_rate=0.01, max_depth=5) typically set once and used to describe run configuration, while metrics are numeric and support a step parameter, allowing multiple values over time (e.g., loss per epoch).

**Why the others are wrong:**
- log_artifact, not log_param, is used for files like plots; log_param is for scalar/string config values.
- log_param can technically be called multiple times with different keys, but the same key can only be set once per run; the distinguishing feature is data type and intent, not call count restriction alone.
- The two APIs are distinct and serve different purposes; they are not interchangeable aliases.

</details>

---

