## Chapter 8: Hyperparameter Tuning: Hyperopt, SparkTrials, and Grid/Random Search — Practice Questions

**Q1.** A data scientist wants to tune hyperparameters for a scikit-learn RandomForestClassifier using Hyperopt on a Databricks cluster with 1 driver and 8 worker nodes, and wants trials to run concurrently across the worker nodes. Which configuration should be used?

A) trials = SparkTrials(parallelism=8); fmin(fn=objective, space=space, algo=tpe.suggest, max_evals=50, trials=trials)
B) trials = Trials(); fmin(fn=objective, space=space, algo=tpe.suggest, max_evals=50, trials=trials)
C) Use GridSearchCV() wrapped inside a Spark UDF
D) Use CrossValidator with a ParamGridBuilder

<details>
<summary>Answer</summary>

**Correct: A**

SparkTrials distributes the evaluation of individual trials across Spark executors, enabling true cluster-wide parallelism for single-node libraries like scikit-learn.

**Why the others are wrong:**
- Trials() runs sequentially on the driver only — no cluster parallelism.
- GridSearchCV is a scikit-learn API, not a Hyperopt trials object, and doesn't integrate with fmin/SparkTrials.
- CrossValidator/ParamGridBuilder are Spark MLlib tools, not Hyperopt, and are used for distributed MLlib models, not scikit-learn.

</details>

---

**Q2.** A team is tuning a Spark MLlib GBTRegressor model, which already trains in a distributed manner across the cluster. They want to use Hyperopt to search hyperparameters. What is the recommended trials configuration?

A) Trials() — the standard single-node sequential trials object
B) SparkTrials(parallelism=1) to force sequential Spark-level execution
C) SparkTrials(parallelism=number_of_executors) to maximize speed
D) SparkTrials with the CrossValidator wrapped inside the objective function

<details>
<summary>Answer</summary>

**Correct: A**

Because MLlib algorithms already parallelize training across the cluster, Databricks recommends using Trials() (not SparkTrials) to avoid double-parallelizing — nesting SparkTrials around an already-distributed algorithm causes resource contention and is unsupported for this pattern.

**Why the others are wrong:**
- SparkTrials(parallelism=1) still attempts to launch trials via Spark tasks, conflicting with MLlib's internal distributed execution.
- High parallelism SparkTrials would launch multiple concurrent distributed MLlib jobs, causing severe resource contention and is explicitly discouraged.
- This option describes a nonstandard, unsupported nesting pattern that isn't how SparkTrials or CrossValidator are meant to be combined.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A data scientist has 6 hyperparameters with 10 possible values each (1,000,000 total combinations) and each model training run takes several minutes. They need to find good hyperparameters within a limited time budget. Which approach BEST meets this requirement?

A) Hyperopt with algo=tpe.suggest to adaptively search the space using Bayesian optimization
B) GridSearchCV to exhaustively evaluate all 1,000,000 combinations
C) RandomizedSearchCV with default n_iter and no time budget
D) Manually test hyperparameter combinations based on intuition

<details>
<summary>Answer</summary>

**Correct: A**

TPE-based Hyperopt search uses results from prior trials to focus on promising regions of the search space, making it far more sample-efficient than exhaustive or purely random search when training is expensive and the space is huge.

**Why the others are wrong:**
- GridSearchCV over 1,000,000 combinations is computationally infeasible within a limited time budget.
- RandomizedSearchCV samples independently with no learning from past results, making it less efficient than TPE for costly objective functions.
- Manual tuning does not scale and is not systematic or reproducible, and isn't a valid exam answer for this scenario type.

</details>

---

**Q4.** You need to define a Hyperopt search space for a learning rate hyperparameter that should be sampled log-uniformly between 0.001 and 0.1. Which definition is correct?

A) hp.uniform('lr', 0.001, 0.1)
B) hp.choice('lr', [0.001, 0.01, 0.1])
C) hp.quniform('lr', 0.001, 0.1, 0.001)
D) hp.loguniform('lr', np.log(0.001), np.log(0.1))

<details>
<summary>Answer</summary>

**Correct: D**

hp.loguniform requires bounds passed as natural log values, and it samples such that the resulting value is uniform in log-space — the correct choice for learning-rate-style parameters that span orders of magnitude.

**Why the others are wrong:**
- hp.uniform samples linearly, oversampling large values and undersampling small ones across orders of magnitude.
- hp.choice restricts sampling to a fixed discrete list, not a continuous range.
- hp.quniform samples a quantized uniform (linear) distribution, not log-uniform, and doesn't match the requirement.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** A data scientist configures SparkTrials(parallelism=32) with fmin(..., max_evals=32, algo=tpe.suggest, trials=spark_trials) on a 32-worker cluster to maximize throughput. What is the tradeoff of this configuration?

A) There is no tradeoff — this is the optimal configuration for TPE
B) fmin() will throw an error because parallelism cannot equal max_evals
C) The MLflow autologging will log duplicate parent runs
D) TPE effectiveness decreases because all trials run fully in parallel with no sequential feedback between them

<details>
<summary>Answer</summary>

**Correct: D**

TPE is an adaptive/Bayesian method that improves suggestions using results of completed trials. When parallelism is set equal (or near) to max_evals, all trials launch essentially at once, so TPE has no prior results to learn from — degrading it toward random search behavior.

**Why the others are wrong:**
- There is a real tradeoff: high parallelism relative to max_evals reduces the adaptive benefit of TPE.
- This configuration is technically valid and will run without error — parallelism can equal max_evals.
- MLflow autologging correctly logs one child run per trial regardless of parallelism; it does not create duplicate parent runs from this setting.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** What distinguishes the TPE (Tree of Parzen Estimators) algorithm used by Hyperopt from grid search and pure random search?

A) TPE evaluates every possible combination in the search space exhaustively
B) TPE is a Bayesian optimization method that uses results of past trials to prioritize more promising regions of the search space
C) TPE samples hyperparameters independently of any previous trial results
D) TPE can only be used when trials=SparkTrials(), never with Trials()

<details>
<summary>Answer</summary>

**Correct: B**

TPE builds probabilistic models of good vs. bad hyperparameter regions based on past trial outcomes, then samples new candidates more likely to improve the objective — this is the core distinction from grid/random search.

**Why the others are wrong:**
- Exhaustive evaluation describes grid search, not TPE.
- Independent sampling with no learning describes random search (rand.suggest), not TPE.
- TPE (algo=tpe.suggest) works identically with either Trials() or SparkTrials() as the trials object.

</details>

---

**Q7.** Which Hyperopt function is responsible for driving the hyperparameter search by minimizing an objective function over a defined search space?

A) hp.choice()
B) fmin()
C) trials.run()
D) model.fit()

<details>
<summary>Answer</summary>

**Correct: B**

fmin(fn, space, algo, max_evals, trials) is the core Hyperopt entry point that executes the search loop, calling the objective function repeatedly and returning the best hyperparameters found.

**Why the others are wrong:**
- hp.choice() defines a categorical parameter within the search space; it does not run the search.
- trials.run() is not a valid Hyperopt API call — Trials/SparkTrials objects are passed into fmin(), not invoked directly.
- model.fit() trains a model but has no role in orchestrating the hyperparameter search itself.

</details>

---

**Q8.** A data scientist runs fmin() with SparkTrials on Databricks but never calls mlflow.start_run() explicitly. What happens to the results of each individual trial?

A) Nothing is logged to MLflow unless mlflow.start_run() is called manually inside the objective function
B) Only the single best-performing trial is logged; all other trials are discarded
C) Databricks automatically logs each trial as a nested child run under a parent run, capturing hyperparameters and metrics
D) Trial results are stored only in the SparkTrials object in memory and never reach MLflow

<details>
<summary>Answer</summary>

**Correct: C**

Databricks automatically integrates Hyperopt with MLflow autologging: each trial fmin() evaluates is logged as a child run with its hyperparameters and metrics, nested under a parent run representing the overall fmin() call — no manual mlflow.start_run() wrapping is required.

**Why the others are wrong:**
- Autologging is enabled by default on Databricks; manual mlflow.start_run() calls are not required to capture trial-level logging.
- All trials are logged as child runs, not just the best one — the best trial's parameters are typically summarized in the parent run.
- Results are logged to MLflow (persisted in the tracking server), not just held in the in-memory SparkTrials object.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

