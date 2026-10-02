## Chapter 9: Cross-Validation and Evaluation Metrics — Practice Questions

**Q1.** A Databricks ML Associate is building a fraud detection model where only 0.4% of transactions are fraudulent. The model achieves 99.6% accuracy but fails to catch most fraud cases. Which metric should be used INSTEAD of accuracy to properly evaluate this model?

A) Accuracy with a higher train/test split ratio
B) R2 score computed on the predicted probabilities
C) F1 score or area under the precision-recall curve
D) Root Mean Squared Error (RMSE) on the predicted labels

<details>
<summary>Answer</summary>

**Correct: C**

F1 score balances precision and recall, and area under the PR curve is specifically designed to evaluate performance on the minority (positive) class in highly imbalanced datasets — accuracy is misleading because predicting all-negative already yields 99.6%.

**Why the others are wrong:**
- Changing the split ratio does not fix the fact that accuracy rewards predicting the majority class.
- R2 is a regression metric that measures variance explained; it is not defined for classification labels.
- RMSE is also a regression metric and cannot be meaningfully applied to discrete class labels.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** You are tuning a Spark ML RandomForestClassifier and want to perform 5-fold cross-validation across a grid of maxDepth values [5, 10, 15] and numTrees values [50, 100]. Which combination of Spark ML classes correctly implements this?

A) TrainValidationSplit with trainRatio=5 to run 5 iterations of validation
B) ParamGridBuilder to define the grid, passed into a CrossValidator with numFolds=5
C) GridSearchCV imported from pyspark.ml.tuning with cv=5
D) ParamGridBuilder passed directly into the RandomForestClassifier constructor with folds=5

<details>
<summary>Answer</summary>

**Correct: B**

In Spark ML, ParamGridBuilder().addGrid(...).build() constructs the hyperparameter grid, which is passed as estimatorParamMaps to CrossValidator along with numFolds=5 to perform k-fold cross-validation.

**Why the others are wrong:**
- TrainValidationSplit performs a single train/validation split, not k-fold CV, and trainRatio is a fraction (e.g. 0.75), not a fold count.
- GridSearchCV is a scikit-learn class, not part of pyspark.ml.tuning.
- ParamGridBuilder output cannot be passed directly to an estimator constructor; it must go through CrossValidator or TrainValidationSplit.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A model predicts a rare disease that affects 1% of patients. The team wants to evaluate model performance using a curve that will not appear artificially optimistic due to class imbalance. Which curve should they plot?

A) ROC curve, because it plots true positive rate against false positive rate
B) Lift chart, because it ranks predictions by decile
C) Precision-recall curve, because it focuses on performance for the positive class
D) Calibration curve, because it compares predicted probability to observed frequency

<details>
<summary>Answer</summary>

**Correct: C**

When the negative class dominates, ROC curves can look deceptively good because the false positive rate stays low even with many false positives relative to true positives. The precision-recall curve isolates behavior on the minority positive class and is the recommended choice for imbalanced problems.

**Why the others are wrong:**
- ROC curves are the exact tool that becomes misleadingly optimistic on imbalanced data because the large true negative count keeps FPR low.
- A lift chart is used for ranking/targeting use cases (e.g., marketing), not for standard imbalanced classification evaluation on the exam.
- Calibration curves assess probability reliability, not discrimination ability, and are not the primary tool for imbalance.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** A data scientist runs `mlflow.evaluate(model=logged_model, data=eval_df, targets="label", model_type="classifier")` on a binary classification model. Which of the following is automatically produced and logged by this call?

A) Only the final F1 score as a single number, requiring manual computation of everything else
B) A fully retrained model with better hyperparameters selected automatically
C) A Delta table containing the raw training data used to fit the model
D) A metrics dictionary (accuracy, F1, AUC, etc.) plus artifacts like a confusion matrix and ROC/PR curve plots

<details>
<summary>Answer</summary>

**Correct: D**

mlflow.evaluate with model_type="classifier" automatically computes a standard set of classification metrics and logs visual artifacts such as the confusion matrix and ROC/precision-recall curves to the MLflow run, without requiring manual metric coding.

**Why the others are wrong:**
- mlflow.evaluate returns a rich EvaluationResult object with many metrics, not a single score.
- mlflow.evaluate does not perform hyperparameter tuning or retraining — that is the role of CrossValidator or a tuning library.
- mlflow.evaluate logs evaluation metrics and plots, not a copy of the training dataset as a Delta table.

</details>

---

**Q5.** A binary classifier produces this confusion matrix on the test set: TP=40, FP=10, FN=60, TN=890. What is the recall of the model?

A) 0.80
B) 0.89
C) 0.40
D) 0.29

<details>
<summary>Answer</summary>

**Correct: C**

Recall = TP / (TP + FN) = 40 / (40 + 60) = 40/100 = 0.40.

**Why the others are wrong:**
- 0.80 would result from mistakenly computing TP/(TP+FP) using the wrong denominator combination.
- 0.89 is the overall accuracy ((TP+TN)/total = 930/1000), not recall.
- 0.29 does not correspond to any standard metric derivable from this matrix and reflects a calculation error.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** A regression model for predicting house prices occasionally sees a few extreme outlier listings (mansions worth 50x the median price) in the test set. The business wants an error metric that is less sensitive to these outliers so typical prediction quality is not distorted. Which metric should be prioritized?

A) RMSE, because it directly penalizes the largest errors
B) MAE, because it weights all errors linearly instead of squaring them
C) R2, because it explains variance regardless of scale
D) AUC-ROC, because it is scale-invariant for continuous targets

<details>
<summary>Answer</summary>

**Correct: B**

MAE (Mean Absolute Error) averages absolute errors linearly, so a few extreme outliers contribute proportionally rather than being squared, making it more robust to outliers than RMSE.

**Why the others are wrong:**
- RMSE squares errors, so it is exactly the metric that becomes dominated and distorted by a few extreme outliers.
- R2 measures variance explained but is still affected by the same squared-error outlier sensitivity as RMSE since it derives from sum of squared residuals.
- AUC-ROC is a classification metric and is not defined for continuous regression targets.

</details>

---

**Q7.** In scikit-learn, which code correctly performs 10-fold cross-validation of a classifier using F1 score as the evaluation metric?

A) cross_val_score(clf, X, y, folds=10, metric='f1')
B) CrossValidator(clf, X, y, numFolds=10, scoring='f1')
C) cross_val_score(clf, X, y, cv=10).score('f1')
D) cross_val_score(clf, X, y, cv=10, scoring='f1')

<details>
<summary>Answer</summary>

**Correct: D**

sklearn.model_selection.cross_val_score takes cv (number of folds) and scoring (metric name or scorer object) as parameters, so cv=10, scoring='f1' correctly configures 10-fold CV scored by F1.

**Why the others are wrong:**
- cross_val_score does not accept 'folds' or 'metric' as parameter names — these are invalid keyword arguments.
- CrossValidator with numFolds is a pyspark.ml.tuning class, not a scikit-learn function, and this mixes APIs incorrectly.
- cross_val_score returns a NumPy array of scores directly; it has no chained .score() method.

</details>

---

**Q8.** Increasing the number of folds (k) in k-fold cross-validation from 3 to 20 primarily results in which trade-off?

A) Lower computational cost but a less reliable estimate of model performance
B) No change in computation time, only a change in which evaluator is used
C) Higher computational cost but a more reliable, lower-variance estimate of model performance
D) Guaranteed improvement in the final model's test accuracy

<details>
<summary>Answer</summary>

**Correct: C**

More folds means training and evaluating the model more times on larger training subsets, increasing compute cost, but it generally produces a more stable and reliable estimate of generalization performance since more data is used for training in each fold.

**Why the others are wrong:**
- Compute cost increases, not decreases, as k increases because k models must be trained instead of fewer.
- The evaluator choice (RMSE, F1, etc.) is independent of the number of folds; k only affects how the data is split.
- Cross-validation estimates performance and tunes hyperparameters — it does not itself guarantee improved final test accuracy on unseen production data.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

