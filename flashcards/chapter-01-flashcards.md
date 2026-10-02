## Chapter 1: Databricks Runtime for ML and Cluster Selection — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | What ML frameworks are pre-installed in Databricks Runtime for ML? | scikit-learn, TensorFlow, Keras, PyTorch, XGBoost, MLflow, and Horovod (for distributed DL). Standard DBR does not include these frameworks by default. |
| 2 | Single-node cluster architecture | Driver only, no worker nodes; Spark runs in local mode. Best for small-data ML with pandas/scikit-learn or lightweight dev/testing. No distributed Spark and no fault tolerance. |
| 3 | When is a multi-node cluster required for ML workloads? | When running distributed Spark MLlib jobs, distributed deep learning via Horovod/HorovodRunner, or processing data too large for the driver alone. |
| 4 | Which tool enables distributed deep learning across GPU workers in Databricks? | Horovod, invoked via HorovodRunner. Pre-installed in Databricks Runtime ML GPU. Works with TensorFlow, Keras, and PyTorch. |
| 5 | How do you select a GPU-enabled Databricks runtime? | Pick a runtime version tagged GPU, e.g. '13.3 LTS ML (GPU, Scala 2.12, Spark 3.4.1)'. Requires GPU-backed instance types; comes with CUDA, cuDNN, NCCL pre-installed. |
| 6 | Command to install a library scoped only to the current notebook session | %pip install <package>. Notebook-scoped — does not affect other notebooks and does not persist after cluster restart. Use cluster libraries or init scripts for permanent installs. |
| 7 | What is a Databricks cluster policy? | A JSON-defined template admins apply to users/groups to restrict cluster attributes (instance types, max workers, autotermination) for cost control and compliance. |
| 8 | Databricks Runtime vs Databricks Runtime for ML — exam distinction | Both include MLflow. Only ML runtime pre-installs scikit-learn, TensorFlow, PyTorch, XGBoost, and Horovod for distributed deep learning. |
| 9 | What does Databricks Repos provide for ML workflows? | Git integration (GitHub, GitLab, Bitbucket, Azure DevOps) for notebooks/source files, enabling branching, commits, and pull-request-based CI/CD for ML code. |
| 10 | Autoscaling cluster: min_workers=2, max_workers=8, idle workload — how many workers remain? | 2 workers (the configured minimum). Cluster never scales below min_workers unless min_workers is explicitly set to 0. |
| 11 | Best cluster choice for XGBoost training on data that fits on one machine | Single-node cluster — non-distributed XGBoost and scikit-learn run efficiently on one machine's driver; avoids paying for unused worker nodes. |
| 12 | Which pre-installed tool tracks ML experiments and runs in Databricks? | MLflow — tracks parameters, metrics, artifacts, and models, with autologging support. Included in both standard and ML runtimes. |
