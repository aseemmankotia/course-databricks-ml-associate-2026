## AutoML Cheat Sheet

### Supported Problem Types
| Type | Target | Default Primary Metric | Underlying Libraries |
|---|---|---|---|
| Classification | Categorical | F1 (weighted) | sklearn, xgboost, lightgbm |
| Regression | Continuous | R2 | sklearn, xgboost, lightgbm |
| Forecasting | Continuous, time-indexed | SMAPE | Prophet, ARIMA |

**Not supported:** clustering, deep learning, image classification, NLP/text generation.

### Launching an Experiment
| Method | Example |
|---|---|
| UI | Experiments → Create AutoML Experiment → pick problem type, table, target |
| API | `from databricks import automl`<br>`summary = automl.classify(train_df, target_col="label", timeout_minutes=30)`<br>`automl.regress(...)` / `automl.forecast(...)` |

### Key Parameters
| Parameter | Purpose |
|---|---|
| `target_col` | Column to predict (classification/regression) |
| `time_col` | Timestamp column (forecasting only) |
| `horizon` | Number of future periods to predict (forecasting) |
| `timeout_minutes` | Time budget for the whole experiment |
| `exclude_cols` | Columns to omit from feature engineering (e.g., IDs) |
| `primary_metric` | Override default optimization metric |

### Data Prep Requirements
- Input must be a Spark DataFrame or Delta table (not raw files).
- Forecasting needs a regular time interval — resample/fill gaps before launch.
- Very large datasets are **sampled** to fit driver memory; not distributed-trained.
- Exclude leakage-prone or identifier columns manually via `exclude_cols`.

### Generated Artifacts
| Artifact | What It Contains |
|---|---|
| Data Exploration Notebook | Profiling: nulls, distributions, cardinality (generated once, before training) |
| Per-Trial Source Notebook | Full feature engineering + model training code for that trial |
| Leaderboard | All trials ranked by primary metric, links to notebooks + MLflow runs |
| MLflow Experiment | Auto-logged params, metrics, model artifacts for every trial |

### Editing the Best Model
1. Open leaderboard → click best trial's notebook link.
2. Clone/export the notebook into your workspace.
3. Edit feature engineering or hyperparameters as plain Python/sklearn code.
4. Re-run manually; register the resulting model to MLflow yourself.

### Key Limitations (exam traps)
- No deep learning / neural architecture search — even with a GPU cluster.
- No native image or unstructured-text modeling (only light text featurization for short text columns).
- Large datasets get sampled, not fully trained on.
- Forecasting requires consistent time frequency — irregular gaps break results.
- AutoML is a baseline generator, not a replacement for full hyperparameter tuning.
