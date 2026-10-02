## Chapter 10: Experiment Tracking and Model Selection in MLflow — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | How do you create a nested child run in MLflow for a hyperparameter sweep? | Open a parent run with mlflow.start_run(), then inside the loop call mlflow.start_run(nested=True) for each child. Exam tip: nested=True must be called while a parent run is still active. |
| 2 | Which function programmatically compares runs and returns a pandas DataFrame? | mlflow.search_runs(experiment_ids=[...], filter_string=..., order_by=[...]). Exam tip: default sort is start_time DESC, not by metric value — always set order_by explicitly to find the 'best' run. |
| 3 | Which system tag links a child run to its parent in a nested run hierarchy? | mlflow.parentRunId — automatically set, visible in the run's Tags section in the UI. |
| 4 | Function to log a matplotlib/plotly figure directly as an artifact? | mlflow.log_figure(fig, 'plot.png'). Exam tip: works with figure objects directly, no need to save to disk first (unlike log_artifact which needs a local file path). |
| 5 | Which auto-generated files enable environment reproducibility for a logged model? | conda.yaml, requirements.txt, and python_env.yaml, written into the model artifact directory by mlflow.<flavor>.log_model(). |
| 6 | What must be set before mlflow.register_model() targets Unity Catalog? | mlflow.set_registry_uri('databricks-uc'). Exam tip: without this, register_model() targets the legacy Workspace Model Registry instead. |
| 7 | Model naming convention: Workspace Registry vs Unity Catalog Registry? | Workspace: single string name (e.g. 'best_model'). Unity Catalog: three-level namespace catalog.schema.model (e.g. 'ml_prod.churn_models.best_model'). Exam tip: UC registration fails without the full namespace. |
| 8 | log_metric vs set_tag — when to use each? | log_metric(key, value, step=None): numeric, queryable, supports step-based time series. set_tag(key, value): string metadata/labels, used for filtering (e.g. 'champion') but not numeric comparison. |
| 9 | Syntax to filter search_runs for runs tagged as champion? | mlflow.search_runs(filter_string="tags.candidate = 'champion'"). Exam tip: filter_string uses SQL-like syntax with metrics./params./tags. prefixes. |
| 10 | Which UI view visually compares hyperparameters vs metrics across many sweep runs at once? | Parallel Coordinates Plot inside the Compare Runs view. Exam tip: best for spotting which hyperparameter ranges drive better metric outcomes. |
| 11 | Default sort order of mlflow.search_runs() when order_by is omitted? | start_time DESC (most recent run first) — NOT sorted by any metric. Exam tip: this is a common trap when selecting the 'best' run programmatically. |
| 12 | log_param vs log_metric behavior on repeated calls with the same key? | log_param: logging the same key twice with a different value raises an error (immutable). log_metric: the same key can be logged multiple times (optionally with a step) to build a time series. |
