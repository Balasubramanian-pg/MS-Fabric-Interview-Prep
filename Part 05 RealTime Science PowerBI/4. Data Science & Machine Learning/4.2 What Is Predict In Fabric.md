# [What Is Predict In Fabric](Part 05 RealTime Science PowerBI/What Is Predict In Fabric.md)

Canonical documentation for [What Is Predict In Fabric](Part 05 RealTime Science PowerBI/What Is Predict In Fabric.md). This document defines concepts, terminology, and standard usage.

## Purpose
The `PREDICT` function in the Fabric ecosystem exists to bridge the gap between machine learning model development and large-scale data processing. Its primary purpose is to provide a scalable, high-performance mechanism for batch inference, allowing users to apply trained models to large datasets stored within a unified data lakehouse or warehouse environment.

By abstracting the complexities of model loading, environment configuration, and distributed execution, `PREDICT` enables data professionals to generate insights from predictive models using familiar syntax (such as SQL or Spark-based APIs) without requiring deep expertise in the underlying machine learning frameworks.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the architectural role and functional behavior of the prediction capability within the Fabric environment.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Batch Inference:** The application of models to large datasets in a non-real-time, distributed manner.
* **Model Integration:** The relationship between the model registry and the execution engine.
* **Schema Mapping:** The translation of data columns to model input features.
* **Scalability:** The theoretical behavior of the function across distributed compute nodes.

**Out of scope:**
* **Real-time Scoring:** Low-latency, single-request/response APIs (e.g., REST endpoints).
* **Model Training:** The process of creating the model weights and parameters.
* **Specific Library Syntax:** Detailed code snippets for specific versions of Scikit-learn, PyTorch, or TensorFlow.

## Definitions
| Term | Definition |
|------|------------|
| **Inference** | The process of using a trained machine learning model to make predictions on new, unseen data. |
| **Batch Scoring** | The execution of inference on a collection of data points simultaneously, optimized for throughput rather than latency. |
| **Model Registry** | A centralized repository for managing the lifecycle of machine learning models, including versioning and metadata. |
| **MLflow** | An open-source platform for the machine learning lifecycle that serves as the standard format for models used by the `PREDICT` function. |
| **Transformer** | A functional component that maps input data to output predictions within a data pipeline. |
| **Hydration** | The process of loading a serialized model from storage into active memory for execution. |

## Core Concepts

### 1. Native Integration
The `PREDICT` function is designed as a first-class citizen of the compute engine. Unlike user-defined functions (UDFs) which may suffer from serialization overhead, native prediction functions are optimized for the underlying distributed architecture, ensuring that data movement between the storage layer and the inference engine is minimized.

### 2. Model Encapsulation
The function relies on the concept of a "packaged model." The model must contain not only the learned parameters but also the necessary metadata regarding input signatures (expected columns and types) and output formats. This encapsulation ensures that the execution engine can validate data compatibility before processing begins.

### 3. Distributed Execution
In a Fabric context, `PREDICT` leverages the distributed nature of the compute cluster. The input dataset is partitioned across multiple nodes, and the model is replicated to each node. Inference occurs in parallel, allowing the system to handle petabyte-scale datasets by scaling horizontally.

## Standard Model
The standard model for utilizing `PREDICT` follows a linear lifecycle:

1.  **Registration:** A model is trained and saved to the Model Registry, typically using the MLflow format. This step defines the "Contract" (inputs and outputs).
2.  **Reference:** The user invokes the `PREDICT` function, referencing the specific model name and version (or stage, such as "Champion" or "Production").
3.  **Mapping:** The system maps the columns of the source table/dataframe to the input features required by the model.
4.  **Computation:** The engine performs the mathematical operations defined by the model against the input data.
5.  **Materialization:** The resulting predictions are appended as new columns to the dataset, which can then be persisted to storage or used in downstream analytics.

## Common Patterns

### The "Gold Layer" Enrichment
In a medallion architecture, `PREDICT` is frequently used when moving data from the Silver (cleaned) layer to the Gold (aggregated/insight) layer. This pattern ensures that business-ready tables contain both historical facts and predicted future values.

### Scheduled Batch Scoring
Models are applied to new data on a recurring schedule (e.g., nightly or hourly). This is the standard pattern for scenarios like churn prediction, demand forecasting, or lead scoring where immediate response is not required.

### SQL-Based Inference
Using `PREDICT` within a SQL interface allows analysts who are not proficient in Python or Scala to leverage advanced machine learning models directly within their reporting workflows.

## Anti-Patterns

### Real-Time Request/Response
Attempting to use the batch `PREDICT` function for sub-second, single-row web application responses. The overhead of initializing the distributed compute environment makes it unsuitable for low-latency requirements.

### Over-Partitioning
Applying `PREDICT` to a dataset with an excessive number of small partitions. This can lead to "shuffle" overhead and inefficient model hydration across the cluster, degrading performance.

### Ignoring Schema Drift
Failing to validate that the input data schema matches the model's expected signature. Because `PREDICT` is often part of an automated pipeline, schema drift can lead to silent failures or nonsensical predictions if not explicitly handled.

## Edge Cases

### Large Model Artifacts
When models are exceptionally large (e.g., several gigabytes), the time required to distribute and load the model into the memory of every worker node can exceed the time taken for the actual inference. This requires specific memory management strategies.

### Missing Feature Handling
If the input dataset contains null values for features required by the model, the behavior of `PREDICT` depends on the underlying model's implementation. Some models may fail, while others may produce `NaN` outputs. Standardizing "imputation" before the `PREDICT` step is a critical boundary concern.

### Version Shadowing
When a model is updated in the registry but the `PREDICT` call uses a generic "Latest" tag, downstream dependencies may break due to changes in the output schema or model behavior.

## Related Topics
* **MLflow Integration:** The standard for model logging and loading.
* **Medallion Architecture:** The data organizational framework where `PREDICT` is typically applied.
* **Distributed Computing:** The underlying architecture (e.g., Spark) that enables scalable inference.
* **Model Governance:** The policies surrounding model versioning, auditing, and deployment.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |