# What Languages Are Supported In Fabric Notebooks

Canonical documentation for What Languages Are Supported In Fabric Notebooks. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of language support in Fabric Notebooks is to provide a polyglot environment for data engineering, data science, and data exploration. By supporting multiple programming languages within a single interface, the platform addresses the need for diverse skill sets to collaborate on the same datasets. This multi-language architecture allows users to leverage the specific strengths of different ecosystems—such as the statistical capabilities of R, the machine learning libraries of Python, the performance of Scala, and the declarative power of SQL—without switching tools or environments.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the architectural support of languages within the Fabric ecosystem.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core programming languages supported by the Spark engine.
* Mechanisms for language switching and interoperability.
* Theoretical boundaries of language execution within a single session.
* The relationship between the notebook kernel and language runtimes.

**Out of scope:**
* Specific third-party library documentation (e.g., Pandas, ggplot2).
* Hardware-specific performance tuning.
* User interface (UI) navigation instructions.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Kernel** | The computational engine that executes the code contained in a notebook document. |
| **Magic Command** | Special commands (often prefixed with `%` or `%%`) used to switch language contexts or perform administrative tasks within a cell. |
| **Polyglot Programming** | The practice of writing code in multiple languages within the same application or notebook to leverage the strengths of each. |
| **Spark Session** | The entry point to programming with Spark, maintaining the state and configurations for the duration of the notebook execution. |
| **Interoperability** | The ability of different programming languages to share data structures (like DataFrames) within the same session. |

## Core Concepts

### The Multi-Language Kernel
Fabric Notebooks utilize a unified kernel architecture based on Apache Spark. Unlike traditional Jupyter notebooks that often bind one notebook to one specific kernel (e.g., a pure Python kernel), the Fabric environment supports a multi-language session. This allows the notebook to interpret and execute different languages in sequence while maintaining a persistent connection to the underlying compute cluster.

### Primary vs. Secondary Languages
Every notebook is assigned a **Default Language**. This language is used for any cell that does not explicitly specify a language override. However, the environment is fundamentally non-exclusive; secondary languages are invoked via cell-level identifiers.

### Data Abstraction Layer
The primary mechanism for language support is the **Spark DataFrame**. Because the underlying engine is Spark, data loaded into a DataFrame in one language (e.g., Scala) is accessible to other languages (e.g., Python or SQL) through the shared Spark session and temporary views.

## Standard Model

The standard model for language support in Fabric Notebooks consists of four primary languages integrated into the Spark ecosystem:

1.  **PySpark (Python):** The primary language for data science and general-purpose data processing. It provides access to the extensive Python ecosystem (NumPy, Scikit-learn, PyTorch).
2.  **Spark SQL:** A declarative language used for structured data processing. It is the standard for data analysts and those performing complex relational transformations.
3.  **Scala:** The native language of Apache Spark. It is used for high-performance data engineering tasks and complex type-safe transformations.
4.  **SparkR (R):** A language optimized for statistical analysis and visualization, providing a Spark-integrated version of the R programming language.

### Language Invocation
The standard model employs "Magic Commands" to define the execution context:
*   `%%pyspark`
*   `%%sql`
*   `%%sparkr`
*   `%%csharp` (where applicable via .NET for Spark)

## Common Patterns

### The SQL-First Transformation
Users often use **Spark SQL** for the initial heavy lifting of data (joining, filtering, and aggregating large datasets) because of its readability and optimization by the Catalyst optimizer, then switch to **PySpark** for specialized machine learning or custom logic.

### Cross-Language Data Sharing
A common pattern involves registering a DataFrame as a **Temporary View**. Once a view is registered in Python or Scala, it can be queried directly using a `%%sql` cell without moving data across the network, as the data remains in the Spark distributed memory.

### Visualization Hybridization
Using **PySpark** or **Scala** to process large-scale data, then using **SparkR** to generate high-quality, publication-ready statistical visualizations using libraries like `ggplot2`.

## Anti-Patterns

### Excessive Language Switching
Switching languages in every cell can lead to fragmented code logic and makes debugging difficult. It is discouraged to switch languages unless there is a specific functional requirement that the current language cannot meet efficiently.

### Local Data Conversion for Large Datasets
Converting a Spark DataFrame to a local object (e.g., a Pandas DataFrame or an R data.frame) to switch languages. This pulls data from the distributed cluster to the driver node, which can cause "Out of Memory" (OOM) errors for large datasets.

### Ignoring Session State
Assuming that variables defined in the local scope of one language (e.g., a Python variable `x = 10`) will be automatically available as a variable in another language (e.g., Scala). Only Spark-level objects (DataFrames, Tables) are shared across the session state.

## Edge Cases

### Library Dependency Conflicts
While the languages share the Spark session, they maintain separate package environments. A Python library update does not affect the R environment. However, conflicting versions of underlying Java/Scala JAR files can occasionally cause issues across the entire session.

### UDF Performance
User Defined Functions (UDFs) written in Python (PySpark) may incur a performance penalty when called from a Scala or SQL context due to the serialization required to move data between the JVM and the Python interpreter.

### Variable Shadowing
Defining a temporary view in SQL with the same name as a variable in Python can lead to confusion, although the system treats the SQL namespace and the Python namespace separately.

## Related Topics
*   **Apache Spark Architecture:** The underlying engine facilitating multi-language support.
*   **Delta Lake:** The storage layer often interacted with via these languages.
*   **V-Order Optimization:** How the engine optimizes data regardless of the language used to write it.
*   **Library Management:** The process of adding third-party capabilities to the supported languages.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |