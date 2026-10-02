# Feature Engineering in Unity Catalog — Cheat Sheet

## Client
`from databricks.feature_engineering import FeatureEngineeringClient`
`fe = FeatureEngineeringClient()`
(Supersedes legacy `databricks.feature_store.FeatureStoreClient` for UC tables.)

## Core Methods
| Method | Purpose | Key Params |
|---|---|---|
| `fe.create_table()` | Register a new feature table | `name`, `primary_keys`, `df`/`schema`, `description` |
| `fe.write_table()` | Add/update rows in existing table | `name`, `df`, `mode='merge'\|'overwrite'` |
| `FeatureLookup()` | Define a join for training/serving | `table_name`, `lookup_key`, `feature_names`, `timestamp_lookup_key` |
| `fe.create_training_set()` | Build joined training DataFrame | `df`, `feature_lookups`, `label`, `exclude_columns` |
| `fe.log_model()` | Log model + FeatureLookup metadata | `model`, `artifact_path`, `flavor`, `training_set` |
| `fe.score_batch()` | Auto-join features + batch predict | `model_uri`, `df` (PK columns only) |

## Point-in-Time Joins (Leakage Prevention)
- Requires a `timestamp_key` on the feature table.
- `FeatureLookup(timestamp_lookup_key=...)` matches each label row to the **most recent feature value at or before** the label's timestamp.
- Without `timestamp_lookup_key`, the join returns the **latest** feature value regardless of label time → leakage risk.

## Offline vs Online Store
| Aspect | Offline Store | Online Store |
|---|---|---|
| Backing | Delta table in UC | Published store (e.g., Databricks Online Tables) |
| Use case | Training, batch scoring | Real-time, low-latency serving |
| Access pattern | Large scans/joins | Single-key point lookups |
| Source of truth | Yes | Synced copy from offline table |

## Inference Pattern
1. Train model using `create_training_set()` → `load_df()`.
2. `fe.log_model()` to store FeatureLookups with the model.
3. `fe.score_batch(model_uri, df)` for batch — pass only primary keys.
4. For real-time, publish feature table to an online store; serving endpoint performs the low-latency lookup + inference.

## Common Traps
- Forgetting `timestamp_lookup_key` → silent leakage, not an error.
- Using `create_table()` on an already-existing table instead of `write_table()`.
- Not using `exclude_columns` → primary/timestamp keys leak into model features.
- Loading model with plain `mlflow.pyfunc` instead of `fe.score_batch()` → loses automated feature joins.
- Treating ad-hoc notebook feature logic as equivalent to a UC feature table — no lineage, no governance, no guaranteed train/serve consistency.
