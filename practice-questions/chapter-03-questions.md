## Chapter 3: Feature Engineering in Unity Catalog — Practice Questions

**Q1.** A team creates a feature table for churn prediction and must guarantee that training data only uses feature values that existed at the time each label was recorded. Which FeatureLookup configuration is required?

A) Set lookup_key to the customer ID column only
B) Set feature_names to include every column in the feature table
C) Set timestamp_lookup_key to match a timestamp column present in both the label DataFrame and the feature table's timestamp_key
D) Set mode='merge' when calling create_table

<details>
<summary>Answer</summary>

**Correct: C**

timestamp_lookup_key tells create_training_set to match each label row to the most recent feature value as of that timestamp, preventing leakage from future feature updates.

**Why the others are wrong:**
- lookup_key alone joins on the primary key only, returning the latest feature value regardless of when the label occurred — this causes leakage.
- feature_names controls which columns are pulled, not the temporal correctness of the join.
- mode is a write_table parameter for upserting data, unrelated to point-in-time correctness.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** A feature table was originally created without a timestamp_key. The team now needs point-in-time correct training sets to avoid leakage during retraining. What must they do?

A) Add a timestamp column to the source data, then create (or recreate) the feature table specifying timestamp_key, and use timestamp_lookup_key in FeatureLookup
B) Call fe.score_batch with a date filter on the existing table
C) Set mode='overwrite' in write_table to reset the leakage
D) Add a version number to primary_keys

<details>
<summary>Answer</summary>

**Correct: A**

Point-in-time joins require the feature table to have a designated timestamp_key column; without it there is no way to filter by 'as of' time.

**Why the others are wrong:**
- score_batch performs inference lookups, not training-set point-in-time joins, and has no date filter parameter for this purpose.
- Overwriting data does not add temporal metadata; leakage is structural, not a data-freshness issue.
- Adding a version number to primary_keys changes the join granularity but does not enable time-aware lookups.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A company needs features available for both offline model training (large batch reads) and real-time serving with very low read latency. Which architecture best satisfies both requirements?

A) Store features only in a Delta table in Unity Catalog and query it with Spark SQL for both use cases
B) Use only an online store and load it into a DataFrame whenever training is needed
C) Maintain two completely separate, independently-updated tables with no relationship to each other
D) Register the feature table in Unity Catalog (offline store) for training, and publish it to an online store (e.g., Databricks Online Tables) for low-latency serving

<details>
<summary>Answer</summary>

**Correct: D**

The standard pattern is offline Delta table in UC for training/batch scoring and a published online store for millisecond-latency lookups during real-time inference, keeping both in sync from a single source of truth.

**Why the others are wrong:**
- Delta/Spark SQL reads are not optimized for the low-latency point lookups real-time serving requires.
- Online stores are optimized for key-based point lookups, not large-scale batch training reads, and are typically not the system of record for full history.
- Two unrelated tables break lineage and risk feature drift/inconsistency between training and serving.

</details>

---

**Q4.** Which FeatureEngineeringClient method is used to create a brand-new Unity Catalog feature table with a specified primary key?

A) fe.write_table()
B) fe.create_table()
C) fe.log_model()
D) fe.create_training_set()

<details>
<summary>Answer</summary>

**Correct: B**

create_table() registers a new feature table in Unity Catalog, requiring name, primary_keys, and either df or schema.

**Why the others are wrong:**
- write_table() writes/updates data in an already-existing table, it does not create the table definition.
- log_model() packages a trained model with its FeatureLookups for later automated inference joins.
- create_training_set() builds a training DataFrame by joining labels with feature lookups; it does not create a feature table.

</details>

---

**Q5.** Which call correctly performs an incremental upsert of new rows into an already-registered feature table without recreating it?

A) fe.create_table(name=..., primary_keys=..., df=new_df)
B) fe.write_table(name=..., df=new_df, mode='merge')
C) fe.create_training_set(df=new_df, feature_lookups=[...], label='y')
D) fe.score_batch(model_uri=..., df=new_df)

<details>
<summary>Answer</summary>

**Correct: B**

write_table with mode='merge' upserts rows into an existing feature table by primary key, which is the correct way to add or update feature values.

**Why the others are wrong:**
- create_table would attempt to re-register the table and fails or errors if it already exists with different schema/keys.
- create_training_set builds a joined training DataFrame; it does not write data into the feature table.
- score_batch performs inference lookups against a model, not writes to a feature table.

</details>

---

**Q6.** In fe.create_training_set(), which parameter is used to remove join/primary key columns from the final training DataFrame so they are not treated as model input features?

A) label
B) feature_lookups
C) exclude_columns
D) timestamp_lookup_key

<details>
<summary>Answer</summary>

**Correct: C**

exclude_columns explicitly drops specified columns (typically keys used only for joining) from the materialized training set.

**Why the others are wrong:**
- label identifies the target column, not columns to drop.
- feature_lookups defines what to join in, the opposite of exclusion.
- timestamp_lookup_key is a FeatureLookup attribute controlling point-in-time matching, not column exclusion.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A model was logged using fe.log_model() with FeatureLookups included. Which method should be used for batch inference so features are automatically joined without the caller providing feature columns?

A) mlflow.pyfunc.load_model() followed by a manual DataFrame join
B) spark.read.table() followed by a manual merge on primary key
C) fe.score_batch(model_uri=..., df=primary_keys_df)
D) fe.create_table() using the same primary keys as the model

<details>
<summary>Answer</summary>

**Correct: C**

score_batch reads the FeatureLookup metadata stored with the model to automatically retrieve and join the correct feature values, requiring only the primary key columns as input.

**Why the others are wrong:**
- Loading with plain mlflow.pyfunc bypasses the stored FeatureLookup metadata, requiring manual and error-prone joins.
- Manual Spark merges lose the guarantee of matching exactly the feature version/logic used at training time.
- create_table registers a new feature table; it has no role in inference-time feature retrieval.

</details>

---

**Q8.** What is the primary advantage of Feature Engineering in Unity Catalog compared to ad-hoc feature engineering repeated independently in each notebook?

A) It provides centralized feature discovery, governance/access control, automatic lineage tracking, and consistent, point-in-time correct reuse across training and serving
B) It removes the need to use Delta Lake for storage
C) It eliminates the requirement to define a primary key
D) It automatically improves model accuracy without any additional tuning

<details>
<summary>Answer</summary>

**Correct: A**

Registering features in UC turns them into governed, discoverable, lineage-tracked assets that can be reused consistently across multiple training and inference workloads, avoiding duplicated and inconsistent ad-hoc logic.

**Why the others are wrong:**
- Feature tables in UC are still Delta tables under the hood; Delta Lake is still required.
- A primary key is mandatory when creating a feature table, not optional.
- Feature Engineering improves data consistency and process efficiency, not model accuracy directly — accuracy still depends on modeling choices.

</details>

---

