## Chapter 7 Cheat Sheet: scikit-learn vs Spark ML

### Choosing an Approach by Data Size
| Scenario | Best Choice |
|---|---|
| Data fits in driver memory | scikit-learn (single-node) |
| Data too large for driver memory | Spark ML (distributed) |
| Many small independent models (per-group) | Pandas UDF (`applyInPandas`) |
| One sklearn model, expensive tuning (GridSearchCV/CV), data fits driver | joblib-spark |
| sklearn model trained small, must score huge Spark DataFrame | `mlflow.pyfunc.spark_udf()` |

### Spark ML Pipeline API
| Component | Method | Description |
|---|---|---|
| Estimator | `.fit(df)` | Learns parameters, returns a Transformer/Model (e.g., `LogisticRegression`, `StringIndexer`) |
| Transformer | `.transform(df)` | Converts a DataFrame into another (e.g., `VectorAssembler`, a fitted `Model`) |
| Pipeline | `.fit(df)` | Chains stages; itself an Estimator; returns a `PipelineModel` |
| PipelineModel | `.transform(df)` | The fitted pipeline; a Transformer applied to new data |

### joblib-spark
```python
from joblibspark import register_spark
register_spark()
from sklearn.utils import parallel_backend
with parallel_backend('spark'):
    grid_search.fit(X, y)
```
- Distributes **independent joblib tasks** (CV folds, hyperparameter combos).
- Requires the dataset to fit in memory on **each worker** (not partitioned).
- Does NOT distribute the row-wise fit of a single model.

### Pandas UDF for Per-Group Training
```python
def train_group(pdf):
    model = LinearRegression().fit(pdf[['x']], pdf['y'])
    return pd.DataFrame({'store_id': [pdf['store_id'].iloc[0]], 'coef': [model.coef_[0]]})

result = df.groupBy('store_id').applyInPandas(train_group, schema='store_id long, coef double')
```
- Best when each group's data is small and independent.
- True data-parallelism: one model per group, trained on separate executors.

### MLflow Model Flavors
| Model Type | Log Function |
|---|---|
| scikit-learn | `mlflow.sklearn.log_model()` |
| Spark ML | `mlflow.spark.log_model()` |
| Generic/any | `mlflow.pyfunc.log_model()` |

### Scaling Inference for a Single-Node Model
```python
predict_udf = mlflow.pyfunc.spark_udf(spark, model_uri="runs:/<run_id>/model")
scored_df = big_spark_df.withColumn("prediction", predict_udf(*feature_cols))
```
- Converts a driver-trained model into a distributed Spark UDF for parallel batch scoring.

### Common Exam Traps
- Confusing joblib-spark (task-parallel tuning) with Spark ML (data-parallel training).
- Thinking Pandas UDFs distribute ONE model's training row-wise — they don't; they train separate models per group.
- Forgetting `register_spark()` before using the `'spark'` joblib backend.
- Using `mlflow.sklearn` to log a Spark ML model (wrong flavor) instead of `mlflow.spark`.
