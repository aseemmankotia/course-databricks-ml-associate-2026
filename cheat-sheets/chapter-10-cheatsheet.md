## Chapter 10 Cheat Sheet — Experiment Tracking & Model Selection

### Nested Runs Pattern (Hyperparameter Sweeps)
```python
with mlflow.start_run(run_name="sweep_parent") as parent:
    for params in param_grid:
        with mlflow.start_run(nested=True, run_name=f"child_{params}"):
            mlflow.log_params(params)
            mlflow.log_metric("f1_score", score)
```
- Child run auto-gets tag `mlflow.parentRunId` → drives UI grouping.
- `nested=True` **only** works inside an active parent `start_run()` block.

### Programmatic Run Comparison
```python
df = mlflow.search_runs(
    experiment_ids=[exp_id],
    filter_string="tags.candidate = 'champion'",
    order_by=["metrics.f1_score DESC"],
    max_results=1
)
best_run_id = df.loc[0, "run_id"]
```
| Default order_by | start_time DESC (NOT metric-based!) |
|---|---|
| filter_string prefixes | `metrics.`, `params.`, `tags.` |

### Logging Reference
| API | Purpose | Mutability |
|---|---|---|
| `mlflow.log_param` | Hyperparameter/config | Immutable — error on overwrite |
| `mlflow.log_metric(key, val, step=)` | Numeric, time-series capable | Repeatable |
| `mlflow.set_tag` | String label (e.g. "champion") | Mutable, searchable via `tags.` |
| `mlflow.log_artifact(path)` | File (plot, CSV, model) | N/A |
| `mlflow.log_figure(fig, name)` | In-memory plot object | N/A |

### Reproducibility Artifacts (auto-logged with `log_model`)
| File | Contains |
|---|---|
| `conda.yaml` | Full conda env spec |
| `requirements.txt` | Pip package versions |
| `python_env.yaml` | Python version + pip env |
| `MLmodel` | Flavor metadata (not env info) |

### Registering Best Model to Unity Catalog
```python
mlflow.set_registry_uri("databricks-uc")
mlflow.register_model(
    model_uri=f"runs:/{best_run_id}/model",
    name="ml_prod.churn_models.best_model"  # catalog.schema.model
)
```
| Registry | Name format |
|---|---|
| Workspace (legacy) | `"model_name"` |
| Unity Catalog | `"catalog.schema.model_name"` |

### UI Comparison Tools
- **Compare Runs → Parallel Coordinates Plot**: hyperparameter vs metric correlation across sweep.
- **Scatter Plot / Contour Plot**: pairwise metric-param relationships.
- Sort/filter columns directly in the runs table by metric column headers.

### Exam Traps
- Assuming `search_runs` sorts by metric by default — it sorts by `start_time`.
- Forgetting `mlflow.set_registry_uri('databricks-uc')` before `register_model` → registers to wrong registry.
- Using a single-level name with UC registry URI set → registration error (needs 3-level namespace).
- Confusing `log_param` (immutable, config) with `log_metric` (repeatable, numeric) for custom scores.
