# Chapter 8 Lab: Hyperparameter Tuning: Hyperopt, SparkTrials, and Grid/Random Search

**Estimated time:** 26 minutes

## Exercise

Run a Hyperopt fmin search with SparkTrials across a cluster to tune an XGBoost model, with each trial logged automatically to MLflow

## Key takeaway

SparkTrials parallelizes independent single-node Hyperopt trials across a cluster — it is not the same as distributing a single Spark ML training job.
