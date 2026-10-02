# Chapter 6 Cheat Sheet: Feature Engineering, Splitting, Data Leakage

## Golden Rule
**Split → Fit (train only) → Transform (train & test)**
Fitting any transformer before splitting = data leakage. This is the single most tested concept in this domain.

## Split Strategies

| Scenario | Method | Notes |
|---|---|---|
| Standard random split | `df.randomSplit([0.8,0.2], seed=42)` | Always set seed for reproducibility |
| Imbalanced classes | Filter by class → randomSplit each → union | Spark has NO native `stratify` param |
| Time-series data | Sort by timestamp, split by cutoff date/index | Never randomSplit temporal data |
| Train/Val/Test | `df.randomSplit([0.7,0.15,0.15], seed=42)` | Val used for tuning, test used once at the end |

## Categorical Encoding Pipeline Order

```
StringIndexer (string → numeric index, most frequent = 0)
    ↓
OneHotEncoder (index → sparse binary vector, dropLast=True by default)
    ↓
VectorAssembler (combine all features into one 'features' vector column)
```

**Exam trap:** OneHotEncoder cannot take raw strings directly — it requires numeric indices from StringIndexer first.

## Scaling Comparison

| Transformer | Output Range | Default Params | Best For |
|---|---|---|---|
| `StandardScaler` | Mean 0 (if withMean=True), unit variance | withMean=False, withStd=True | Linear/logistic regression, distance-based models |
| `MinMaxScaler` | Fixed range, default [0,1] | min=0.0, max=1.0 | Neural networks, bounded-input algorithms |

## Estimator vs Transformer

| Type | Method | Behavior |
|---|---|---|
| Estimator | `.fit(df)` | Learns parameters, returns a Model/Transformer |
| Transformer | `.transform(df)` | Applies learned parameters, no learning |

## Pipeline Pattern (Reproducible Transforms)

```python
from pyspark.ml import Pipeline
from pyspark.ml.feature import StringIndexer, OneHotEncoder, VectorAssembler, StandardScaler
from pyspark.ml.classification import LogisticRegression

indexer = StringIndexer(inputCol="region", outputCol="regionIdx")
encoder = OneHotEncoder(inputCol="regionIdx", outputCol="regionVec")
assembler = VectorAssembler(inputCols=["regionVec","age"], outputCol="rawFeatures")
scaler = StandardScaler(inputCol="rawFeatures", outputCol="features")
lr = LogisticRegression(featuresCol="features", labelCol="label")

pipeline = Pipeline(stages=[indexer, encoder, assembler, scaler, lr])
train_df, test_df = df.randomSplit([0.8, 0.2], seed=42)

model = pipeline.fit(train_df)      # fit ONLY on train
predictions = model.transform(test_df)  # transform reuses learned params
```

## Common Exam Traps

1. Fitting `StandardScaler`/`OneHotEncoder` on full dataset before splitting → leakage.
2. Using `randomSplit()` on time-series data → leaks future into training.
3. Assuming Spark's `randomSplit()` stratifies automatically → it does not; must filter-by-class manually.
4. Calling `.fit()` again on the test set instead of reusing the `PipelineModel.transform()`.
5. Forgetting `OneHotEncoder` requires numeric indices (from `StringIndexer`), not raw strings.
6. Omitting `seed` in `randomSplit()`, breaking reproducibility across runs.
