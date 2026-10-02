# Chapter 6 Lab: Feature Engineering, Splitting, and Avoiding Data Leakage

**Estimated time:** 22 minutes

## Exercise

Build a Spark ML Pipeline with StringIndexer, OneHotEncoder, and StandardScaler, fitting the pipeline only on the training split

## Key takeaway

Always fit transformers only on training data after splitting, and use time-based splits for temporal data to prevent data leakage.
