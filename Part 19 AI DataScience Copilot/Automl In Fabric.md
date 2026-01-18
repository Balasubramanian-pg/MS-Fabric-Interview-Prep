# [Automl In Fabric](Part 19 AI DataScience Copilot/Automl In Fabric.md)

Canonical documentation for [Automl In Fabric](Part 19 AI DataScience Copilot/Automl In Fabric.md). This document defines concepts, terminology, and standard usage.

## Purpose
AutoML (Automated Machine Learning) in Fabric exists to democratize the machine learning lifecycle by automating the labor-intensive, iterative tasks of model development. It addresses the complexity of algorithm selection, hyperparameter tuning, and feature engineering, allowing both citizen data scientists and professional practitioners to accelerate the transition from raw data in a unified lakehouse to actionable predictive insights.

The primary objective is to optimize the "time-to-model" while maintaining high standards of transparency, reproducibility, and integration within a broader data analytics ecosystem.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the architectural and conceptual framework of AutoML within the Fabric environment.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Automated Workflow:** The end-to-end process of data ingestion, preprocessing, model selection, and optimization.
* **Integration Framework:** How AutoML interacts with unified storage (Lakehouses) and tracking systems (MLflow).
* **Evaluation Metrics:** The standard mechanisms for assessing model performance across classification, regression, and forecasting.
* **Governance and Tracking:** The lifecycle management of experiments and versions.

**Out of scope:**
* **Manual Model Development:** Hand-coded neural network architectures or manual hyperparameter sweeps outside the AutoML engine.
* **General Data Engineering:** Standard ETL/ELT processes that do not specifically pertain to feature preparation for ML.
* **Third-party SaaS ML:** External automated ML services not natively integrated into the Fabric runtime.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Experiment** | The highest-level organizational unit for a specific machine learning problem, containing multiple runs and trials. |
| **Run** | A single execution of the AutoML process, resulting in a collection of tested models. |
| **Trial** | An individual iteration within a run where a specific algorithm and set of hyperparameters are tested. |
| **Featurization** | The automated process of transforming raw data into numerical features suitable for machine learning algorithms. |
| **Hyperparameter** | A configuration setting external to the model that governs the learning process (e.g., learning rate, tree depth). |
| **Primary Metric** | The specific performance measure (e.g., R2, Accuracy, F1 Score) that the AutoML engine seeks to optimize. |
| **MLflow** | The open-source framework used within Fabric to track experiments, package code, and manage model registries. |

## Core Concepts

### 1. Automated Feature Engineering
AutoML in Fabric automatically detects data types and applies relevant transformations. This includes handling missing values, encoding categorical variables (One-Hot, Target encoding), and scaling numerical features. This ensures the data is "model-ready" without manual intervention.

### 2. Algorithm Selection and Pruning
The engine evaluates a diverse library of algorithms (e.g., LightGBM, XGBoost, Random Forest, Linear Regression). It employs intelligent search strategies to prioritize high-performing algorithms and "prune" (discard) underperforming paths early in the process to save computational resources.

### 3. Hyperparameter Optimization (HPO)
Rather than an exhaustive grid search, AutoML utilizes sophisticated techniques (such as Bayesian Optimization or Tree-structured Parzen Estimators) to navigate the search space and find the optimal configuration for each algorithm.

### 4. Transparency and Explainability
Despite the automation, the process is not a "black box." Fabric provides model transparency through feature importance scores and the ability to review the specific parameters used in the winning trial.

## Standard Model

The standard model for AutoML in Fabric follows a linear progression within a circular feedback loop:

1.  **Data Selection:** Identifying the source table or dataframe within the Lakehouse.
2.  **Task Definition:** Specifying the ML task type (Classification, Regression, or Time-series Forecasting).
3.  **Optimization Goal:** Selecting the primary metric and defining the constraints (e.g., maximum training time, number of trials).
4.  **Execution:** The AutoML engine iterates through featurization, algorithm selection, and HPO.
5.  **Evaluation:** Comparing the results of all trials via a leaderboard.
6.  **Registration:** Selecting the "Best Model" and promoting it to the Model Registry for deployment or batch scoring.

## Common Patterns

*   **The Low-Code Wizard Pattern:** Utilizing the graphical user interface to trigger AutoML runs directly from a Lakehouse table. This is ideal for rapid prototyping and baseline establishment.
*   **The Notebook-Driven Pattern:** Using the Fabric SDK (often leveraging FLAML or SynapseML) within a Spark notebook to trigger AutoML. This allows for version-controlled, reproducible, and highly customized automation.
*   **The Champion-Challenger Pattern:** Using AutoML to periodically "challenge" a currently deployed model to see if a new run with updated data yields a superior version.

## Anti-Patterns

*   **Data Leakage:** Including features in the training set that contain information about the target variable which would not be available at prediction time.
*   **Over-Optimization:** Running AutoML for excessive durations on small datasets, leading to overfitting where the model memorizes noise rather than learning patterns.
*   **Ignoring Class Imbalance:** Running AutoML on highly skewed datasets without adjusting the primary metric (e.g., using Accuracy instead of Precision-Recall or F1-Score).
*   **"Set and Forget":** Assuming an AutoML model remains valid indefinitely without monitoring for data drift or concept drift.

## Edge Cases

*   **High-Cardinality Categorical Features:** Features with thousands of unique values (like User IDs) can degrade AutoML performance or cause memory issues if not handled via specific encoding strategies.
*   **Small Datasets:** When the dataset is extremely small, the cross-validation folds may become unrepresentative, leading to high variance in model performance.
*   **Cold-Start Forecasting:** Attempting time-series forecasting on new products or entities with little to no historical data, where traditional AutoML lag-based features cannot be generated.
*   **Wide Data:** Datasets with thousands of columns (features) may require a manual dimensionality reduction step before AutoML to prevent computational bottlenecks.

## Related Topics

*   **MLflow Integration:** The underlying framework for tracking and versioning.
*   **Fabric Lakehouse:** The primary data source and storage layer.
*   **Synapse Data Science:** The broader experience within Fabric that encompasses AutoML.
*   **Model Scoring:** The process of applying a trained AutoML model to new data for predictions.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |