# [How Do You Track Ml Experiments](Part 05 RealTime Science PowerBI/How Do You Track Ml Experiments.md)

Canonical documentation for [How Do You Track Ml Experiments](Part 05 RealTime Science PowerBI/How Do You Track Ml Experiments.md). This document defines concepts, terminology, and standard usage.

## Purpose
Machine Learning (ML) development is inherently iterative and non-deterministic. Unlike traditional software engineering, where the logic is explicitly defined in code, ML outcomes depend on the intersection of code, data, and stochastic optimization processes. 

Experiment tracking exists to provide a systematic record of these iterations. It addresses the problem of "reproducibility debt" by ensuring that any given model state can be reconstructed, audited, and compared against others. This practice transforms ML development from an ad-hoc trial-and-error process into a structured scientific discipline.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   **Metadata Management:** Logging of parameters, metrics, and environmental configurations.
*   **Artifact Versioning:** Management of model binaries, datasets, and visualization outputs.
*   **Lineage and Traceability:** Mapping the relationship between inputs (data/code) and outputs (models).
*   **Comparative Analysis:** Frameworks for evaluating multiple iterations.

**Out of scope:**
*   Specific vendor implementations (e.g., MLflow, Weights & Biases, Comet).
*   Model deployment and serving (MLOps downstream).
*   Data engineering pipelines (upstream of the experiment).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Experiment** | A high-level logical grouping of related iterations aimed at solving a specific problem or testing a specific hypothesis. |
| **Run** | A single execution of a training script or pipeline; the atomic unit of experiment tracking. |
| **Hyperparameter** | A configuration variable that is external to the model and whose value is set before the learning process begins (e.g., learning rate, batch size). |
| **Metric** | A quantitative measure used to evaluate the performance of a model during or after a run (e.g., Accuracy, F1-score, Loss). |
| **Artifact** | Any file-based output produced by a run, such as serialized model weights, plots, or processed data samples. |
| **Metadata** | Structured information about a run, including timestamps, user identity, hardware specifications, and software dependencies. |
| **Lineage** | The end-to-end record of the provenance of a model, including the specific data version and code commit used. |

## Core Concepts

### 1. Reproducibility
The ability to recreate the exact results of a previous run. This requires capturing the "Holy Trinity" of ML: the exact code version, the exact data version, and the exact environment (dependencies and hardware state).

### 2. Observability
The capacity to monitor the internal state of a model during training. This involves logging scalar values (loss curves) and system metrics (CPU/GPU utilization) in real-time to detect anomalies or convergence issues.

### 3. Comparability
The framework for evaluating different runs side-by-side. This requires standardized logging schemas so that metrics from Run A can be meaningfully compared to metrics from Run B, even if they occurred weeks apart.

### 4. Traceability
The audit trail that links a deployed model back to its training origin. If a model fails in production, traceability allows engineers to identify the specific experiment, hyperparameters, and dataset that produced it.

## Standard Model

The standard model for experiment tracking follows a four-stage lifecycle:

1.  **Initialization:** The experimenter defines the scope and objective. A unique identifier is assigned to the experiment.
2.  **Execution & Logging:** During the run, the system automatically or programmatically captures:
    *   **Inputs:** Code version (Git hash), Data version (DVC/Snapshot), Hyperparameters.
    *   **Environment:** Library versions, OS, Hardware (GPU/TPU).
    *   **Outputs:** Metrics (at specified intervals), Artifacts (model checkpoints).
3.  **Persistence:** Data is stored in a centralized repository (Metadata Store + Artifact Store) to ensure it survives the ephemeral nature of compute instances.
4.  **Analysis:** The experimenter uses a visualization layer to query the metadata store, compare runs, and select the "best" model based on predefined KPIs.

## Common Patterns

### The "Auto-Logging" Pattern
Integrating tracking hooks directly into the training framework (e.g., deep learning libraries) to automatically capture parameters and metrics without manual instrumentation.

### The "Tagging and Namespace" Pattern
Using structured tags (e.g., `staging`, `production-candidate`, `baseline`) to organize thousands of runs into manageable subsets.

### The "Centralized Metadata Store" Pattern
Decoupling the compute environment from the tracking environment. Regardless of where the model is trained (local laptop, cloud cluster, edge device), all logs are sent to a single, centralized source of truth.

### The "Configuration-as-Code" Pattern
Storing all hyperparameters in external configuration files (YAML/JSON) that are automatically logged as artifacts, ensuring the "recipe" is never lost.

## Anti-Patterns

*   **Manual Spreadsheet Logging:** Recording results in a manual document. This is prone to human error, lacks lineage, and cannot scale.
*   **The "Golden Model" Overwrite:** Saving model weights to a generic filename (e.g., `model_final.pkl`) and overwriting it with every run, destroying the history of progress.
*   **Implicit Versioning:** Relying on file timestamps or folder names (e.g., `v1`, `v2_final_v3`) instead of immutable identifiers like Git hashes or content-based checksums.
*   **Logging Only "Successes":** Failing to log failed runs or crashed experiments. Negative results are critical for understanding the search space and avoiding redundant work.

## Edge Cases

*   **Non-Deterministic Hardware:** In some high-performance computing environments, floating-point errors or GPU multi-threading can lead to different results even with the same seed. Tracking must include hardware-specific metadata to account for this.
*   **Distributed Training:** When training across multiple nodes, tracking must aggregate metrics from all workers while maintaining a single "Run" identity to avoid data duplication or fragmentation.
*   **Streaming/Online Learning:** In systems where models learn continuously from data streams, the concept of a "Run" is blurred. Tracking must shift to "Time-Windowed" evaluation.
*   **Large Artifacts:** When models or datasets are in the terabyte range, traditional artifact logging becomes a bottleneck. Tracking systems must use "Reference Logging" (storing a pointer/URI) rather than moving the actual data.

## Related Topics
*   **Model Registry:** The transition point where a tracked experiment becomes a managed asset.
*   **Data Version Control (DVC):** The methodology for tracking the data inputs that feed into experiments.
*   **Hyperparameter Optimization (HPO):** Automated processes that generate hundreds of runs to find optimal configurations.
*   **MLOps:** The broader discipline of operationalizing the ML lifecycle.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |