## Chapter 4: MLflow Tracking, Model Registry, and Unity Catalog Governance — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What does mlflow.start_run() do? | Opens a context manager that creates (or resumes) an MLflow run, capturing params, metrics, tags, and artifacts logged inside the block. Exam tip: runs auto-close on exiting the 'with' block. |
| 2 | mlflow.sklearn.autolog() — what gets logged automatically? | Hyperparameters, training metrics, and the fitted model artifact for supported scikit-learn estimators, without manual log_* calls. Exam tip: custom metrics outside the framework's API still need explicit logging. |
| 3 | mlflow.log_param vs mlflow.log_metric — key difference? | log_param = static config value (string/number, set once per key). log_metric = numeric value that can be logged repeatedly with a 'step' argument (e.g., loss per epoch). |
| 4 | How do you log a file (e.g., a plot or CSV) to a run? | mlflow.log_artifact(local_path). Exam tip: artifacts are files/directories, distinct from params (config) and metrics (numbers). |
| 5 | How are models registered to Unity Catalog vs the legacy workspace registry? | UC models use a three-level namespace: catalog.schema.model_name, registered via mlflow.register_model() or the UC-aware client with a UC model URI. Legacy registry uses a flat model name only. |
| 6 | Champion / Challenger aliases — what are they? | Mutable pointers assigned to specific model versions in Unity Catalog (e.g., @Champion for current production model). They replace legacy Staging/Production/Archived stages, which are NOT supported in UC. |
| 7 | How do you set an alias on a registered model version? | client.set_registered_model_alias(name='catalog.schema.model', alias='Champion', version=3). Exam tip: reference the model later via models:/catalog.schema.model@Champion. |
| 8 | Why are stages deprecated for Unity Catalog model registry? | Stages were tied to the legacy workspace-scoped registry. UC uses aliases + tags for lifecycle management, aligning with UC's centralized, catalog-based governance model. |
| 9 | What is a Databricks Job used for in ML pipelines? | Orchestrating and scheduling notebooks/scripts (e.g., retraining, batch inference) with cron schedules, task dependencies, retries, and failure notifications. |
| 10 | What does a Databricks Repo provide? | Git-based integration (GitHub/GitLab/Bitbucket/Azure DevOps) inside the workspace for branching, committing, pulling, and pull requests on notebooks and code — not model versioning. |
| 11 | Nested runs in MLflow — when to use? | Use mlflow.start_run(nested=True) inside an outer run to group child runs (e.g., each hyperparameter trial) under one parent run for organized comparison in the Tracking UI. |
| 12 | What governance benefits does registering a model in Unity Catalog add over MLflow Tracking alone? | Centralized access control (GRANT/REVOKE), lineage across workspaces, and a unified catalog.schema.model namespace — Tracking alone only records experiment run history, not governed model versions. |
