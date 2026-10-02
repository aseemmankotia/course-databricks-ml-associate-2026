## Chapter 4 Cheat Sheet: MLflow Tracking, Model Registry, Unity Catalog

### MLflow Tracking Core APIs
| API | Purpose | Notes |
|---|---|---|
| `mlflow.start_run()` | Opens/closes a run (context manager) | Use `nested=True` for child runs inside a parent |
| `mlflow.log_param(key, value)` | Log static config value | Same key can't be set twice in one run |
| `mlflow.log_metric(key, value, step=None)` | Log numeric value | Supports multiple values per key over `step` |
| `mlflow.log_artifact(path)` | Log a file/directory | Plots, CSVs, model files |
| `mlflow.log_model(model, artifact_path)` | Log a model as an MLflow artifact | Enables later registration |
| `mlflow.<flavor>.autolog()` | Auto-log params/metrics/model for supported frameworks | sklearn, xgboost, tensorflow, pytorch, etc. |

### Model Registry: Legacy vs Unity Catalog
| Feature | Legacy Workspace Registry | Unity Catalog Registry |
|---|---|---|
| Namespace | flat model name | `catalog.schema.model_name` |
| Lifecycle markers | Stages (Staging/Production/Archived) | Aliases (e.g., Champion, Challenger) + Tags |
| Access control | Workspace ACLs | Unity Catalog GRANT/REVOKE, cross-workspace |
| Lineage | Limited | Full UC lineage across catalogs |
| Status | Deprecated for new projects | Recommended / current standard |

### Alias Management (Unity Catalog)
```python
client = mlflow.MlflowClient()
client.set_registered_model_alias(
    name="main.ml_models.churn_predictor",
    alias="Champion",
    version=4
)
# Load by alias:
model = mlflow.pyfunc.load_model("models:/main.ml_models.churn_predictor@Champion")
```
Exam note: Aliases are mutable and reassignable; a version can hold multiple aliases at once (e.g., Champion + Challenger cannot point to same version by default use case but is allowed if reassigned).

### Registering a Model to Unity Catalog
```python
mlflow.set_registry_uri("databricks-uc")
mlflow.register_model(
    model_uri="runs:/<run_id>/model",
    name="main.ml_models.churn_predictor"
)
```

### Jobs for ML Orchestration
- Schedule via cron expression (e.g., weekly retraining).
- Multi-task Jobs support dependencies (task B runs after task A succeeds).
- Notifications: email/webhook on start, success, or failure.
- Use Jobs, not interactive clusters, for repeatable, automated pipelines.

### Repos for Git Collaboration
- Supports GitHub, GitLab, Bitbucket, Azure DevOps.
- Enables branch creation, commit, pull, and PR workflows on notebooks in-workspace.
- Not a substitute for Model Registry — Repos version code, Registry versions models.

### Common Exam Traps
- Confusing legacy **stages** with Unity Catalog **aliases** — UC does NOT support Staging/Production stages.
- Assuming `autolog()` captures every custom metric — it only captures what the framework's API exposes.
- Forgetting the three-level namespace requirement (`catalog.schema.model`) when registering to UC.
- Using Repos for scheduling instead of Jobs — Repos = version control only.
