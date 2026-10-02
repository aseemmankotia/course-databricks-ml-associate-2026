## Chapter 10: Experiment Tracking and Model Selection in MLflow — Practice Questions

**Q1.** A data scientist runs a hyperparameter sweep of 50 Random Forest configurations and wants all 50 runs grouped under a single parent run in the MLflow UI so the sweep can be evaluated as one unit. Which implementation achieves this?

A) Call mlflow.start_run() independently for each of the 50 configurations
B) Log all 50 configurations into 50 separate MLflow experiments
C) Open an outer mlflow.start_run(), then call mlflow.start_run(nested=True) inside the loop for each configuration
D) Manually add a tag 'parent_run_id' to each run after training completes

<details>
<summary>Answer</summary>

**Correct: C**

nested=True inside an already-open parent mlflow.start_run() context is the documented pattern for hyperparameter sweeps; MLflow auto-sets the mlflow.parentRunId tag on each child.

**Why the others are wrong:**
- Independent start_run() calls with no active parent context produce 50 unrelated top-level runs, not a nested hierarchy
- Separate experiments break the ability to compare all sweep runs together in one Compare Runs view
- Manually setting a tag after the fact doesn't build the actual parent/child relationship MLflow uses for UI grouping and search filtering

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** After a 50-run hyperparameter sweep, an engineer needs the single run with the highest logged 'f1_score' metric to promote it. Which code correctly retrieves it?

A) mlflow.search_runs(experiment_ids=[exp_id])
B) mlflow.get_run(run_id='latest')
C) mlflow.search_runs(experiment_ids=[exp_id], order_by=['metrics.f1_score DESC'], max_results=1)
D) mlflow.list_run_infos(experiment_id=exp_id)

<details>
<summary>Answer</summary>

**Correct: C**

search_runs accepts order_by with a metrics.<name> DESC/ASC clause and max_results to directly return the top-scoring run as a DataFrame row.

**Why the others are wrong:**
- Without order_by, results default to sorting by start_time, not by the metric value, so the top row isn't guaranteed to be the best F1
- mlflow.get_run requires a known run_id and has no 'latest' keyword — it does not search or rank runs
- list_run_infos is not a valid MLflow tracking API for ranking runs by metric

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A colleague needs to recreate the exact Python package versions used to train a model that was logged with mlflow.sklearn.log_model(). Which auto-generated artifact should they use?

A) MLmodel
B) input_example.json
C) metrics.json
D) conda.yaml (or requirements.txt)

<details>
<summary>Answer</summary>

**Correct: D**

MLflow automatically writes conda.yaml and requirements.txt into the model artifact directory, capturing the exact library versions needed to reproduce the training environment.

**Why the others are wrong:**
- MLmodel is the model flavor/metadata descriptor, not an environment spec
- input_example.json (when present) only stores a sample input schema, not dependencies
- MLflow does not generate a metrics.json artifact by default; metrics are stored in the run's metric store, not as an environment file

</details>

---

**Q4.** A team wants to register the best run's model directly into the Unity Catalog schema 'ml_prod.churn_models' instead of the legacy Workspace Model Registry. Which setup is required before calling mlflow.register_model()?

A) Call mlflow.set_registry_uri('databricks-uc') and use name='ml_prod.churn_models.best_model'
B) Use name='best_model' with no registry URI change
C) Call mlflow.log_model(name='ml_prod.churn_models.best_model')
D) Create the model manually in the Databricks UI first, then call register_model with no name argument

<details>
<summary>Answer</summary>

**Correct: A**

Registering to Unity Catalog requires setting the registry URI to 'databricks-uc' and supplying the three-level namespace catalog.schema.model as the model name.

**Why the others are wrong:**
- A single-string name without the registry URI set registers to the legacy Workspace Model Registry, not Unity Catalog
- log_model logs the artifact but does not perform model registry registration; register_model is the correct API
- register_model always requires an explicit name argument; there is no auto-detection from a manually created UI entry

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** Which MLflow UI feature is BEST suited for visually identifying which hyperparameter values across dozens of sweep runs correlate with higher accuracy?

A) The Data Profile tab
B) The Model Registry stage transition log
C) Parallel Coordinates Plot in the Compare Runs view
D) The experiment's description text box

<details>
<summary>Answer</summary>

**Correct: C**

The Parallel Coordinates Plot in Compare Runs is designed to plot multiple params against a chosen metric across many runs simultaneously, revealing patterns.

**Why the others are wrong:**
- Data Profile tab summarizes dataset statistics, not run/hyperparameter comparisons
- The stage transition log only tracks registry lifecycle events (Staging/Production), not metric comparisons
- The description text box is free-form notes, not a comparison visualization

</details>

---

**Q6.** Within a training script, which call correctly logs a custom evaluation metric named 'business_score' with value 87.5 for the current run?

A) mlflow.log_param('business_score', 87.5)
B) mlflow.set_tag('business_score', 87.5)
C) mlflow.log_metric('business_score', 87.5)
D) mlflow.log_artifact('business_score', 87.5)

<details>
<summary>Answer</summary>

**Correct: C**

log_metric is the correct API for numeric, potentially time-series values; it also enables sorting/filtering via search_runs with metrics.<name>.

**Why the others are wrong:**
- log_param is meant for immutable configuration values, not evaluative numeric results, and doesn't support step-based logging
- set_tag stores string metadata and is not queryable via metrics.<name> in search_runs order_by clauses
- log_artifact takes a file path argument, not a key/value pair, and would raise a type error here

</details>

---

**Q7.** A team wants to mark one run as the 'champion' model so it can later be filtered with mlflow.search_runs(filter_string=...). Which logging call is correct?

A) mlflow.log_metric('champion', 1)
B) mlflow.set_tag('candidate', 'champion')
C) mlflow.log_param('champion', True)
D) mlflow.log_artifact('champion.txt')

<details>
<summary>Answer</summary>

**Correct: B**

Tags are the correct mechanism for arbitrary string labels used for later filtering, e.g. filter_string="tags.candidate = 'champion'".

**Why the others are wrong:**
- log_metric expects a numeric value tracked as a performance measure, not a categorical label, and can't hold string values
- log_param is meant to be set once at run start for immutable configuration, not for post-hoc labeling of run status
- log_artifact creates a file in storage but is not searchable via search_runs filter_string syntax

</details>

---

**Q8.** In an MLflow nested run hierarchy, which mechanism links a child run back to its parent run?

A) A manually logged param called parent_id
B) The metric parent_run logged as 1
C) The experiment_id matching between parent and child
D) The system tag mlflow.parentRunId, automatically set on the child run

<details>
<summary>Answer</summary>

**Correct: D**

MLflow automatically sets the mlflow.parentRunId system tag on every child run created with nested=True, and this tag drives the parent/child grouping shown in the UI.

**Why the others are wrong:**
- MLflow does not require or use a manual param named parent_id for hierarchy tracking
- There is no built-in metric convention for linking parent/child runs
- Sharing the same experiment_id only means runs live in the same experiment; it does not by itself establish a parent/child relationship

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

