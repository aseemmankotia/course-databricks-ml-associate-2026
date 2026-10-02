## Chapter 9: Cross-Validation and Evaluation Metrics — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Spark ML CrossValidator default numFolds value? | 3. Exam tip: numFolds must be set explicitly (commonly 3, 5, or 10) — ParamGridBuilder().build() supplies estimatorParamMaps to CrossValidator alongside numFolds. |
| 2 | F1 score formula? | F1 = 2 * (Precision * Recall) / (Precision + Recall). Exam tip: F1 is the harmonic mean, so it penalizes models that are strong on only one of precision or recall — used for imbalanced classification. |
| 3 | ROC curve vs Precision-Recall curve — which for imbalanced data? | Precision-Recall curve. Exam tip: ROC (TPR vs FPR) can look overly optimistic when negatives dominate; PR curve isolates minority-class performance and is preferred for imbalanced classification. |
| 4 | RMSE vs MAE — which is more robust to outliers? | MAE. Exam tip: RMSE squares errors so large outliers dominate; MAE weighs all errors linearly, giving a more 'typical error' view. |
| 5 | ParamGridBuilder syntax for Spark ML? | ParamGridBuilder().addGrid(model.maxDepth, [5,10,15]).addGrid(model.numTrees, [50,100]).build() — returns a list of parameter maps consumed by CrossValidator's estimatorParamMaps argument. |
| 6 | Key constructor params for pyspark.ml.tuning.CrossValidator? | estimator, estimatorParamMaps, evaluator, numFolds, seed, parallelism. Exam tip: evaluator must match problem type (BinaryClassificationEvaluator, MulticlassClassificationEvaluator, or RegressionEvaluator). |
| 7 | BinaryClassificationEvaluator metricName options? | "areaUnderROC" (default) or "areaUnderPR". Exam tip: switch to areaUnderPR for imbalanced binary classification problems. |
| 8 | MulticlassClassificationEvaluator metricName options? | "f1" (default), "accuracy", "weightedPrecision", "weightedRecall". Exam tip: f1 is default because accuracy alone is misleading on multi-class imbalance. |
| 9 | RegressionEvaluator metricName options? | "rmse" (default), "mse", "mae", "r2", "var". Exam tip: pick mae when outlier robustness matters; r2 when explaining variance is the business question. |
| 10 | scikit-learn syntax for k-fold CV scoring by F1? | cross_val_score(estimator, X, y, cv=5, scoring='f1') — returns an array of per-fold scores. Exam tip: 'cv' takes the fold count; 'scoring' takes the metric name string. |
| 11 | What does mlflow.evaluate(model, data, targets, model_type="classifier") automatically produce? | A metrics dict (accuracy, F1, AUC, log-loss, etc.) plus logged artifacts like confusion matrix and ROC/PR curve plots to the active MLflow run — no manual metric coding needed. |
| 12 | R2 score range and interpretation? | Typically 0 to 1 for a reasonable model (1 = perfect fit), but can go negative if the model performs worse than predicting the mean. Exam tip: negative R2 is a valid exam-trap answer indicating a poor model, not an error. |
