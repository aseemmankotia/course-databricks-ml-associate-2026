# Chapter 7 Lab: Training Classical Models: scikit-learn vs Spark ML

**Estimated time:** 24 minutes

## Exercise

Train a Spark ML RandomForestClassifier pipeline and compare it to a scikit-learn RandomForestClassifier trained on a pandas sample, logging both to MLflow

## Key takeaway

Choose single-node scikit-learn for datasets that fit in memory, and distributed Spark ML — or pandas UDFs for many independent models — when data or model count exceeds single-node capacity.
