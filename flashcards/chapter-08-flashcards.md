## Chapter 8: Hyperparameter Tuning: Hyperopt, SparkTrials, and Grid/Random Search — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Trials() vs SparkTrials() — key difference? | Trials() runs sequentially on the driver (single machine). SparkTrials() distributes trial evaluations across Spark cluster workers in parallel. Exam tip: use SparkTrials only for single-node model libraries (scikit-learn, single-node TF/Keras), not for MLlib. |
| 2 | Should you wrap a Spark MLlib model (e.g., GBTRegressor) in SparkTrials for Hyperopt tuning? | No. MLlib already distributes training across the cluster. Wrapping it in SparkTrials double-parallelizes and causes resource contention. Use Trials() instead. |
| 3 | fmin() required arguments | fn (objective function), space (hp search space), algo (tpe.suggest or rand.suggest), max_evals (int), trials (Trials() or SparkTrials()). |
| 4 | hp.loguniform(label, low, high) — units of low/high? | Natural log values, not raw bounds. E.g. hp.loguniform('lr', np.log(0.001), np.log(0.1)) samples log-uniformly between 0.001 and 0.1. |
| 5 | Which hp function samples from a discrete list of categorical options? | hp.choice(label, options_list) — e.g. hp.choice('optimizer', ['sgd','adam','rmsprop']). |
| 6 | algo=tpe.suggest vs algo=rand.suggest in fmin() | tpe.suggest = Bayesian optimization (Tree of Parzen Estimators), learns from past trials to suggest better candidates. rand.suggest = pure random search, no learning between trials. |
| 7 | SparkTrials parallelism vs max_evals — recommendation? | Keep parallelism ≤ max_evals. If parallelism approaches max_evals (near-full parallelism), TPE loses effectiveness because it needs completed trial results to adaptively suggest better hyperparameters. |
| 8 | GridSearchCV vs RandomizedSearchCV (scikit-learn) | GridSearchCV exhaustively evaluates every combination in param_grid — slow for large spaces. RandomizedSearchCV samples n_iter random combinations — faster for large spaces. Neither uses Bayesian optimization (unlike Hyperopt TPE). |
| 9 | Does Hyperopt + SparkTrials autolog to MLflow on Databricks? | Yes, by default. Each trial is logged as a nested child run (params + metrics) under a parent run for the fmin() call — no manual mlflow.start_run() wrapping needed. |
| 10 | SparkTrials() key optional constructor argument for concurrency control | parallelism — the max number of trials evaluated concurrently across the cluster. If unset, defaults based on cluster's available executors/cores. |
| 11 | What does fmin() return? | A dict of the best hyperparameter values found. For hp.choice parameters, it returns the index, not the actual value — use hyperopt.space_eval(space, best) to decode. |
| 12 | When to prefer Hyperopt/TPE over RandomizedSearchCV? | When the objective function (model training) is expensive and you want the search to adaptively focus on promising regions using prior trial results — TPE is more sample-efficient than random search. |
