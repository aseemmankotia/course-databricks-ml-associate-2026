## Chapter 11 Cheat Sheet — Batch, Streaming, Real-Time Serving

### Choosing the Right Inference Method
| Requirement | Tool | Why |
|---|---|---|
| Millisecond, per-record REST response | Databricks Model Serving | Autoscaling managed REST endpoint, built for interactive apps |
| Millions of rows at rest, no latency SLA | `mlflow.pyfunc.spark_udf` | Distributes scoring across cluster; high throughput |
| Continuous arrival of new records/files | Structured Streaming + `spark_udf` | Auto Loader ingests new files; UDF scores incrementally, low end-to-end latency |
| Single-node/local Python inference | `mlflow.pyfunc.load_model` | Loads plain Python model object, no Spark distribution |

### Core Commands
```python
# Batch scoring with Spark UDF
import mlflow
predict_udf = mlflow.pyfunc.spark_udf(spark, model_uri="models:/main.schema.model/3")
df_scored = df.withColumn("prediction", predict_udf(*feature_cols))

# Streaming scoring — identical UDF, streaming source/sink
stream_df = spark.readStream.format("delta").load(source_path)
scored = stream_df.withColumn("prediction", predict_udf(*feature_cols))
scored.writeStream.format("delta").option("checkpointLocation", ckpt).start(sink_path)

# Plain Python load (non-distributed)
model = mlflow.pyfunc.load_model("models:/main.schema.model/3")
model.predict(pandas_df)
```

### Unity Catalog Model URI Format
```
models:/<catalog>.<schema>.<model_name>/<version>
models:/<catalog>.<schema>.<model_name>@<alias>   # e.g. @champion, @challenger
```
- UC model registry uses **aliases** instead of legacy Staging/Production stages.
- Same URI format works for `load_model` and `spark_udf`.

### Model Serving Endpoint Basics
- REST endpoint auto-created from a registered model version/alias.
- **Autoscaling**: compute scales with traffic; can **scale to zero** when idle (adds cold-start latency on next request).
- Endpoint config requires: model reference, workload/compute size, CPU vs GPU (GPU for heavier deep learning models).
- Best fit: low-latency, single-record, synchronous prediction requests from apps.

### Latency vs Throughput — The Core Exam Distinction
| Method | Latency | Throughput | Best Use |
|---|---|---|---|
| Model Serving | Low (ms) | Lower per node | Real-time apps, single predictions |
| `spark_udf` (batch) | Higher per request | High (parallel cluster) | Large offline/bulk scoring |
| Structured Streaming + `spark_udf` | Near-real-time (seconds) | Moderate-high, incremental | Continuous data arrival, near-live scoring |

### Common Exam Traps
- Using Model Serving REST calls row-by-row for millions of records → wrong; use `spark_udf`.
- Assuming Structured Streaming needs a special inference API → it reuses the same `spark_udf`.
- Confusing `load_model` (non-distributed) with `spark_udf` (distributed Spark UDF).
- Forgetting scale-to-zero causes cold-start delay on the first request after idling.
- Using `runs:/` URI instead of `models:/` when referencing a Unity Catalog registered model.
