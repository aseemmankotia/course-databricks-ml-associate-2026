## Chapter 1: Databricks Runtime for ML and Cluster Selection — Practice Questions

**Q1.** A data scientist is training a scikit-learn RandomForestClassifier on a 2GB CSV that fits entirely in memory on a single VM. The team wants to minimize cost while developing the model. Which cluster configuration BEST meets this requirement?

A) A multi-node cluster with autoscaling enabled from 2 to 8 workers
B) A high-concurrency cluster shared across the team with 4 workers
C) A single-node cluster using Databricks Runtime for ML
D) A GPU-enabled multi-node cluster running Databricks Runtime for ML

<details>
<summary>Answer</summary>

**Correct: C**

scikit-learn is single-machine only; a single-node cluster (driver only, no workers) runs it efficiently and avoids paying for idle worker nodes.

**Why the others are wrong:**
- Autoscaling multi-node cluster wastes cost since scikit-learn cannot use worker nodes for distributed training
- High-concurrency clusters are meant for shared multi-user SQL/BI workloads, not cost-optimized single-model training
- GPU is unnecessary and costly for a CPU-bound RandomForest on a small in-memory dataset

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An ML team wants to train a PyTorch deep learning model using data-parallel distributed training across multiple GPU worker nodes. Which combination is required?

A) Databricks Runtime (standard) with a single-node GPU instance
B) Databricks Runtime for ML GPU on a multi-node cluster, using HorovodRunner
C) Databricks Runtime for ML on a multi-node CPU cluster with XGBoost
D) Databricks SQL warehouse with a GPU-optimized instance type

<details>
<summary>Answer</summary>

**Correct: B**

Distributed deep learning across GPU workers requires the ML GPU runtime (pre-installed CUDA/cuDNN/NCCL) on a multi-node cluster, using Horovod/HorovodRunner for data-parallel training.

**Why the others are wrong:**
- Standard runtime doesn't pre-install GPU deep learning libraries or Horovod, and single-node has no workers to distribute across
- XGBoost on CPU doesn't fulfill the GPU deep learning requirement
- SQL warehouses are for SQL analytics, not ML model training

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** Which library, pre-installed in Databricks Runtime for ML, is used to perform distributed deep learning training across multiple GPU workers?

A) MLflow
B) Delta Lake
C) Feature Store client
D) Horovod

<details>
<summary>Answer</summary>

**Correct: D**

Horovod (via HorovodRunner) is the distributed training framework pre-installed in Databricks Runtime ML for scaling TensorFlow, Keras, and PyTorch training across GPU workers.

**Why the others are wrong:**
- MLflow is for experiment tracking and model management, not distributed training
- Delta Lake is a storage/table format, unrelated to distributed model training
- Feature Store client manages feature tables, not training distribution

</details>

---

**Q4.** A data scientist needs to install a specific version of a Python package for testing in their current notebook only, without affecting other users on the shared cluster. What should they do?

A) Install the library cluster-wide via the Libraries UI
B) Write a cluster-scoped init script to install the library
C) SSH into the driver node and run pip install manually
D) Run %pip install <package>==<version> in the notebook

<details>
<summary>Answer</summary>

**Correct: D**

%pip install creates a notebook-scoped Python environment, isolated per notebook session, without impacting other notebooks or users on the shared cluster.

**Why the others are wrong:**
- Cluster-wide Libraries UI installs affect all users and require a cluster restart
- Init scripts apply at cluster startup for all attached notebooks, not scoped to one notebook
- Manually installing on the driver via SSH is not supported/managed and affects the whole cluster

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** What is the primary distinction between Databricks Runtime and Databricks Runtime for ML?

A) Databricks Runtime for ML supports Delta Lake while standard runtime does not
B) Databricks Runtime for ML only runs on GPU instances
C) Databricks Runtime for ML pre-installs ML/DL frameworks such as scikit-learn, TensorFlow, PyTorch, and XGBoost, plus Horovod for distributed training
D) Databricks Runtime for ML does not support Python, only R and Scala

<details>
<summary>Answer</summary>

**Correct: C**

Databricks Runtime for ML is built on top of the standard runtime and adds pre-installed, pre-configured ML/DL libraries and distributed training tooling.

**Why the others are wrong:**
- Delta Lake support exists in both standard and ML runtimes
- ML runtime works on both CPU and GPU instance types; GPU is a separate variant, not a requirement
- ML runtime fully supports Python, R, and Scala just like standard runtime

</details>

---

**Q6.** An admin wants to allow only the ML team to launch GPU instance types while preventing all other users from selecting expensive GPU nodes on any cluster they create. Which feature should the admin configure?

A) Workspace access control lists (ACLs)
B) Cluster autoscaling with a max worker limit
C) A cluster policy restricting available instance types by group
D) Instance pools shared across the workspace

<details>
<summary>Answer</summary>

**Correct: C**

Cluster policies are JSON-defined templates that admins attach to specific groups/users to restrict allowed instance types, node counts, and other attributes when creating clusters.

**Why the others are wrong:**
- Workspace ACLs control access to notebooks/folders, not cluster hardware options
- Autoscaling controls how many workers scale up/down, not which instance types are selectable
- Instance pools reduce cluster startup time by pre-warming VMs, but don't by themselves restrict who can pick GPU types

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A team wants Git-based version control, branching, and pull request workflows for their ML notebooks as part of a CI/CD pipeline. Which Databricks feature should they use?

A) DBFS root storage
B) Databricks Workspace folders
C) Databricks Repos
D) Unity Catalog volumes

<details>
<summary>Answer</summary>

**Correct: C**

Databricks Repos integrates with Git providers (GitHub, GitLab, Bitbucket, Azure DevOps) to provide branching, commits, and pull request support directly for notebooks and source files.

**Why the others are wrong:**
- DBFS is a distributed file system for data storage, not version control
- Workspace folders organize notebooks but have no native Git integration
- Unity Catalog volumes store files/data governed by Unity Catalog, unrelated to source control

</details>

---

**Q8.** A cluster is configured with autoscaling min_workers=2 and max_workers=8. During an overnight period with no active jobs, what is the minimum number of worker nodes the cluster will maintain?

A) 2 workers
B) 0 workers
C) 1 worker
D) 8 workers

<details>
<summary>Answer</summary>

**Correct: A**

Autoscaling never reduces the worker count below the configured min_workers value; here it stays at 2 regardless of idle load.

**Why the others are wrong:**
- The cluster does not scale to 0 workers unless min_workers is explicitly set to 0
- 1 worker is below the configured minimum and would violate the policy
- 8 is the max, used only under peak load, not idle periods

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

