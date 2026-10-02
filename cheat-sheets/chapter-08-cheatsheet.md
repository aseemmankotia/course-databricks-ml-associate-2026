## Chapter 8 Cheat Sheet — Hyperparameter Tuning

### Hyperopt Core API
| Function | Purpose |
|---|---|
| `fmin(fn, space, algo, max_evals, trials)` | Runs the search, minimizes `fn` |
| `hp.choice(label, list)` | Categorical parameter |
| `hp.uniform(label, low, high)` | Linear continuous range |
| `hp.loguniform(label, log(low), log(high))` | Log-scale continuous range (pass log-transformed bounds) |
| `hp.quniform(label, low, high, q)` | Quantized linear range |
| `space_eval(space, best)` | Decodes `hp.choice` index into actual value |

### Trials vs SparkTrials
| | Trials() | SparkTrials() |
|---|---|---|
| Execution | Sequential, on driver | Distributed across Spark workers |
| Best for | MLlib models (already distributed), small searches | Single-node libs: scikit-learn, single-node TF/Keras, XGBoost (non-distributed) |
| Key arg | none | `parallelism` (max concurrent trials) |
| MLflow logging | Manual or autologged | Autologged: each trial = nested child run |

### algo Options
| algo | Behavior |
|---|---|
| `tpe.suggest` | Bayesian optimization (TPE) — adaptive, learns from past trials |
| `rand.suggest` | Pure random search — no learning between trials |

### Key Gotcha: parallelism vs max_evals
- Recommended: `parallelism <= max_evals`.
- If `parallelism` ≈ `max_evals` (near-full parallel), TPE degrades toward random search since it lacks sequential feedback.
- Never wrap an already-distributed MLlib model in `SparkTrials` — use `Trials()` to avoid double-parallelization/resource contention.

### scikit-learn Search Comparison
| Method | Search Strategy | Best When |
|---|---|---|
| `GridSearchCV` | Exhaustive, all combos | Small param grids |
| `RandomizedSearchCV(n_iter=N)` | Random sample of N combos | Large grids, no adaptivity needed |
| Hyperopt (`tpe.suggest`) | Bayesian, adaptive | Large grids + expensive training, want sample efficiency |

### MLflow Integration
- Databricks autologs Hyperopt trials to MLflow automatically — no need to wrap objective function in `mlflow.start_run()`.
- Each trial → nested/child run with hyperparameters + metric.
- Parent run summarizes overall `fmin()` call and best params.

### Exam Notes
- SparkTrials = parallel execution of *single-node* model training across workers, one model per task.
- TPE (Tree of Parzen Estimators) = Bayesian method modeling good vs. bad regions from history — NOT exhaustive, NOT purely random.
- Log-scale hyperparameters (learning rate, regularization strength) → use `hp.loguniform`, not `hp.uniform`.
