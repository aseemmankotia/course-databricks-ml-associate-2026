## Chapter 9 Cheat Sheet: Cross-Validation & Evaluation Metrics

### Metric Selection by Problem Type
| Problem | Metric | When to Use |
|---|---|---|
| Classification (balanced) | Accuracy | Classes roughly equal, cost of errors symmetric |
| Classification (imbalanced) | F1, AUC-PR | Minority class matters more (fraud, disease, churn) |
| Classification (ranking) | AUC-ROC | Compare model discrimination across thresholds; misleading if classes very imbalanced |
| Regression (general) | RMSE | Default; penalizes large errors more (squared) |
| Regression (outlier-robust) | MAE | Outliers present, want linear penalty |
| Regression (variance explained) | R2 | Business wants % of variance explained; can be negative for bad models |

### Confusion Matrix Formulas
| Metric | Formula |
|---|---|
| Precision | TP / (TP + FP) |
| Recall (Sensitivity) | TP / (TP + FN) |
| F1 | 2·P·R / (P + R) |
| Accuracy | (TP + TN) / Total |

### ROC vs Precision-Recall
| Curve | Axes | Best For |
|---|---|---|
| ROC | TPR vs FPR | Balanced classes |
| PR | Precision vs Recall | Imbalanced classes (rare positive class) |

### Spark ML Evaluators
| Evaluator | Default Metric | Other Options |
|---|---|---|
| BinaryClassificationEvaluator | areaUnderROC | areaUnderPR |
| MulticlassClassificationEvaluator | f1 | accuracy, weightedPrecision, weightedRecall |
| RegressionEvaluator | rmse | mse, mae, r2, var |

### CrossValidator Workflow (Spark ML)
```python
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from pyspark.ml.evaluation import BinaryClassificationEvaluator

paramGrid = (ParamGridBuilder()
  .addGrid(model.maxDepth, [5, 10, 15])
  .addGrid(model.numTrees, [50, 100])
  .build())

cv = CrossValidator(
  estimator=model,
  estimatorParamMaps=paramGrid,
  evaluator=BinaryClassificationEvaluator(metricName="areaUnderPR"),
  numFolds=5,
  seed=42)

cvModel = cv.fit(train_df)
bestModel = cvModel.bestModel
```
Default `numFolds` = 3. Higher k = more reliable estimate, higher compute cost.

### scikit-learn Cross-Validation
```python
from sklearn.model_selection import cross_val_score
scores = cross_val_score(clf, X, y, cv=5, scoring='f1')
```

### mlflow.evaluate
```python
result = mlflow.evaluate(
  model="models:/my_model/1",
  data=eval_df,
  targets="label",
  model_type="classifier")  # or "regressor"
```
Auto-logs metrics dict + confusion matrix + ROC/PR plots as run artifacts.

### Exam Traps
- Accuracy on imbalanced data looks great but hides poor minority-class detection — exam expects F1/AUC-PR instead.
- ROC curve on heavily imbalanced data appears optimistic — PR curve is the safer default answer.
- R2 can be negative — don't assume it's always 0–1.
- CrossValidator's `numFolds` vs TrainValidationSplit's `trainRatio` — don't confuse the two tuning classes.
