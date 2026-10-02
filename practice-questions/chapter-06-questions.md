## Chapter 6: Feature Engineering, Splitting, and Avoiding Data Leakage — Practice Questions

**Q1.** A data scientist builds a StandardScaler and fits it on the entire DataFrame before calling randomSplit() to create train and test sets. What is the primary problem with this approach?

A) StandardScaler cannot be fit on a DataFrame, only on a numpy array
B) randomSplit() will fail because the DataFrame already contains a scaled vector column
C) StandardScaler must be wrapped in a Pipeline or it will throw a runtime exception
D) The scaler learns mean and standard deviation using test data statistics, causing data leakage into the training process

<details>
<summary>Answer</summary>

**Correct: D**

Fitting any Estimator (scaler, encoder, imputer) before splitting means the learned statistics (mean, std, min/max) are influenced by rows that will later be used for testing, inflating validation performance — this is the textbook definition of data leakage.

**Why the others are wrong:**
- StandardScaler in Spark ML operates on DataFrame Vector columns natively; this is not a technical limitation
- randomSplit() works regardless of which columns exist in the DataFrame; it simply splits rows
- Pipeline wrapping is a best practice for reproducibility but a standalone scaler will run fine without it — the issue is leakage, not a crash

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A churn dataset has 95% negative class and 5% positive class. A data scientist needs a train/test split that preserves this class ratio in both sets using Spark. Which approach BEST achieves this?

A) Call df.randomSplit([0.8, 0.2], seed=42) directly on the full DataFrame
B) Split the DataFrame by class label using filter(), apply randomSplit() to each class subset separately, then union the resulting train and test pieces
C) Sort the DataFrame by the label column and split by row index
D) Increase the seed value in randomSplit() until the resulting ratio looks approximately correct

<details>
<summary>Answer</summary>

**Correct: B**

Spark's randomSplit() has no built-in stratify parameter (unlike sklearn's train_test_split). The standard pattern is to filter each class into its own DataFrame, split each independently with the same weights/seed, then union — guaranteeing the class ratio is preserved in both train and test.

**Why the others are wrong:**
- Plain randomSplit() on an imbalanced DataFrame can produce a test set with an even more skewed ratio purely by chance, especially with small minority classes
- Sorting by label then splitting by index would put entire classes into only one split, which is the opposite of stratification
- Seed-tuning to force a ratio is not reproducible, not scalable, and is not a recognized or exam-endorsed technique

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A team is forecasting monthly sales and has 3 years of historical data with a timestamp column. Which splitting strategy is MOST appropriate for evaluating the model realistically?

A) Use df.randomSplit([0.8, 0.2], seed=42) to randomly shuffle rows into train and test
B) Use stratified sampling on the sales amount column, binned into quartiles
C) Randomly split, then manually reorder the test set by timestamp after splitting
D) Sort the data by timestamp and use the earliest 80% of records as train and the most recent 20% as test

<details>
<summary>Answer</summary>

**Correct: D**

Time-based splits must respect chronological order — training only on past data and testing on future data — to simulate real-world deployment and avoid leaking future information into the model.

**Why the others are wrong:**
- Random splitting lets the model 'see' data points chronologically after the test period during training, causing leakage of future trends
- Stratifying on sales amount ignores the temporal structure entirely and doesn't solve the leakage problem
- Reordering the test set after a random split doesn't fix leakage — the training set still contains future rows relative to the test window

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** A dataset contains a categorical column 'region' with 8 distinct string values. Which sequence of Spark ML transformers is required to correctly prepare this column for a tree-agnostic linear model?

A) Apply VectorAssembler directly on the string column
B) Apply StringIndexer to convert strings to numeric indices, then apply OneHotEncoder on the indexed column
C) Apply OneHotEncoder directly on the raw string column
D) Apply StandardScaler on the string column after casting it to double

<details>
<summary>Answer</summary>

**Correct: B**

Spark ML's OneHotEncoder requires numeric category indices as input, not raw strings. StringIndexer must run first to map string labels to indices (ordered by descending frequency), and only then can OneHotEncoder produce sparse one-hot vectors.

**Why the others are wrong:**
- VectorAssembler only combines existing numeric/vector columns into a feature vector — it cannot parse raw strings
- OneHotEncoder throws a type error on string input; it strictly requires numeric indices
- Casting a categorical string like 'West' to double is not meaningful and StandardScaler is for continuous numeric features, not categories

</details>

---

**Q5.** A feature 'age' ranges from 18 to 90 and must be rescaled into a fixed [0, 1] range for a neural network that is sensitive to input magnitude. Which Spark ML transformer should be used?

A) StandardScaler with withMean=true and withStd=true
B) StringIndexer
C) OneHotEncoder
D) MinMaxScaler

<details>
<summary>Answer</summary>

**Correct: D**

MinMaxScaler rescales each feature to a specified range, [0, 1] by default, which is exactly the requirement described.

**Why the others are wrong:**
- StandardScaler centers data to zero mean and unit standard deviation but does not guarantee a bounded [0, 1] range
- StringIndexer is for converting categorical strings to numeric indices, not for rescaling continuous numeric features
- OneHotEncoder produces sparse binary vectors for categories and is unrelated to numeric rescaling

</details>

---

**Q6.** Which line of code correctly performs a reproducible 70/30 train-test split in Spark?

A) train_df, test_df = df.randomSplit([0.7, 0.3])
B) train_df, test_df = df.randomSplit([0.7, 0.3], seed=42)
C) train_df, test_df = df.split(0.7, 0.3, seed=42)
D) train_df, test_df = train_test_split(df, test_size=0.3, random_state=42)

<details>
<summary>Answer</summary>

**Correct: B**

randomSplit() accepts a list of weights and an explicit seed parameter; specifying the seed is required for reproducibility across runs, which the exam frequently tests.

**Why the others are wrong:**
- This omits the seed, so results are not reproducible across separate executions
- DataFrame has no .split() method in Spark ML — this is not valid syntax
- train_test_split() is an sklearn function that operates on pandas/numpy objects, not Spark DataFrames

</details>

---

**Q7.** A Pipeline containing a StringIndexer, OneHotEncoder, and LogisticRegression is fit only on the training DataFrame using pipeline.fit(train_df). What should be done to correctly evaluate the model on the test set?

A) Call pipeline.fit(test_df) separately to get test-specific transformations, then compare metrics
B) Manually refit the StringIndexer and OneHotEncoder on the combined train+test data before scoring
C) Call the fitted PipelineModel's .transform(test_df) method, reusing the exact transformations learned from the training data
D) Call pipeline.transform(test_df) directly without ever calling .fit()

<details>
<summary>Answer</summary>

**Correct: C**

The Pipeline's fit() call on training data returns a PipelineModel with fitted stages; calling .transform() on the test set applies those same learned parameters (indices, encoding mappings, model coefficients) without ever re-fitting, which prevents leakage and ensures consistent feature representation.

**Why the others are wrong:**
- Fitting again on test data would relearn indices/encodings from a different distribution, producing mismatched feature spaces and invalidating the evaluation
- Refitting on combined data leaks test set information into the transformers used during training-time decisions and evaluation is no longer independent
- A raw, unfit Pipeline object has no learned parameters — .transform() requires a fitted PipelineModel, not the Pipeline estimator itself

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** In Spark ML's Estimator/Transformer API, what is the fundamental distinction between fit() and transform()?

A) fit() and transform() are interchangeable and both apply a pre-learned transformation to a DataFrame
B) fit() is only used for models, while transform() is only used for feature engineering steps
C) fit() operates on RDDs and transform() operates on DataFrames
D) fit() learns parameters from data and returns a Model/Transformer, while transform() applies an already-learned transformation to produce a new DataFrame

<details>
<summary>Answer</summary>

**Correct: D**

In Spark ML, an Estimator's fit() method learns parameters (e.g., mean/std, category mappings, regression coefficients) and returns a Transformer/Model; that Transformer's transform() method then applies the learned parameters to any DataFrame, without relearning them.

**Why the others are wrong:**
- They are distinct steps with different purposes — conflating them ignores the core Estimator/Transformer design pattern
- Both patterns apply to models (e.g., LogisticRegression.fit()) and feature transformers (e.g., StandardScaler.fit()) alike
- Both fit() and transform() operate on Spark DataFrames in modern Spark ML; RDD-based MLlib is legacy and not the primary exam focus

</details>

---

