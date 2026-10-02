## Chapter 1 Cheat Sheet: Databricks Runtime for ML & Cluster Selection

### Databricks Runtime (DBR) vs Databricks Runtime for ML
| Feature | Standard DBR | Databricks Runtime ML |
|---|---|---|
| Delta Lake, Spark core | Yes | Yes |
| MLflow | Yes | Yes |
| scikit-learn, TensorFlow, PyTorch, XGBoost | No | Yes (pre-installed) |
| Horovod (distributed DL) | No | Yes |
| GPU driver stack (CUDA/cuDNN/NCCL) | No | Yes, on GPU variant only |

**Exam note:** ML runtime = standard runtime + ML/DL libraries. GPU support requires selecting the GPU-tagged ML runtime version, e.g. `13.3 LTS ML (GPU, ...)`.

### Single-Node vs Multi-Node Clusters
| Aspect | Single-Node | Multi-Node |
|---|---|---|
| Workers | None (driver only) | Driver + N workers |
| Spark mode | Local mode | Distributed cluster mode |
| Use case | scikit-learn, pandas, small XGBoost, dev/test | Distributed MLlib, Horovod distributed DL, large data |
| Fault tolerance | None | Yes (worker recovery) |
| Cost | Lowest (1 VM) | Scales with worker count |

**Gotcha:** scikit-learn/XGBoost (non-distributed mode) cannot use extra workers — choosing multi-node wastes money.

### GPU Clusters
- Use GPU-tagged Databricks Runtime ML (e.g., ML GPU) on GPU instance types.
- CUDA, cuDNN, NCCL pre-installed — no manual driver setup needed.
- Horovod + HorovodRunner enables data-parallel distributed training across GPU workers (TensorFlow, Keras, PyTorch).
- Single-node GPU cluster = one GPU VM, no distributed training possible.

### Pre-installed ML Libraries (DBR ML)
scikit-learn, TensorFlow, Keras, PyTorch, XGBoost, MLflow, Horovod, pandas, numpy.

### Cluster Policies & Autoscaling
- **Cluster policy:** admin-defined JSON limiting instance types, max workers, autotermination — enforces cost/compliance per group.
- **Autoscaling:** define `min_workers` / `max_workers`; scales within bounds based on load; never drops below `min_workers` unless set to 0.

### Notebook-Scoped Python Environments
- `%pip install <pkg>` — scoped to current notebook session only; does not persist after cluster restart; does not affect other notebooks/users.
- For permanent, cluster-wide installs use the Libraries UI or an init script.

### Databricks Repos
- Native Git integration (GitHub, GitLab, Bitbucket, Azure DevOps).
- Supports branching, commits, pull requests for notebooks/source files.
- Enables version-controlled, CI/CD-ready ML code workflows.

### Quick Decision Rules
- Small in-memory data + single-machine library (scikit-learn/pandas) → **Single-node**
- Distributed Spark MLlib or Horovod distributed DL → **Multi-node**
- Deep learning training speed-up → **GPU runtime (ML GPU)**
- Temporary package for one notebook → **%pip install**
- Enforce cost/hardware restrictions → **Cluster policy**
- Git-based ML code workflow → **Repos**
