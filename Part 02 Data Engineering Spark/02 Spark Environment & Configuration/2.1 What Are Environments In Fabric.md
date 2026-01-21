# What Are Environments In Fabric

Canonical documentation for What Are Environments In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
Environments serve as the foundational configuration layer for distributed compute and data processing. They address the critical need for **reproducibility**, **isolation**, and **standardization** across the data engineering and data science lifecycle. 

By decoupling the compute settings and software dependencies from the individual code assets (such as notebooks or jobs), environments ensure that execution remains consistent regardless of which user triggers a process. This prevents "configuration drift" and ensures that the transition from development to production is seamless and predictable.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the logical architecture of environments within a unified data platform.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Logical architecture of compute runtimes and software dependencies.
* Governance and inheritance models for environment settings.
* Lifecycle management of libraries and spark configurations.
* The relationship between environments, workspaces, and execution items.

**Out of scope:**
* Specific cloud provider hardware specifications.
* Step-by-step UI tutorials for creating environments.
* Pricing and licensing models.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Environment** | A logical container that encapsulates specific runtimes, software libraries, and compute configurations for data processing. |
| **Runtime** | The underlying execution engine version (e.g., Spark version) that defines the core capabilities and language support of the environment. |
| **Library** | A collection of pre-compiled code (Python, R, Java/Scala) that extends the functionality of the runtime. |
| **Compute Configuration** | The set of parameters that define how hardware resources are allocated, such as executor size, core counts, and memory limits. |
| **Artifact/Item** | A unit of work (e.g., a Notebook or Spark Job Definition) that attaches to an environment for execution. |
| **Public Library** | A package sourced from a global repository (e.g., PyPI, CRAN, Maven). |
| **Custom Library** | A proprietary or local package uploaded directly to the environment by a user. |

## Core Concepts

### 1. Encapsulation of State
An environment acts as a "snapshot" of the required state for a compute task. This includes the specific version of the operating engine, the exact versions of all third-party libraries, and the environment variables required for the code to function.

### 2. Decoupling
Environments decouple the *what* (the code) from the *how* (the infrastructure and dependencies). This allows developers to update libraries or upgrade runtimes without modifying the underlying source code of their notebooks or jobs.

### 3. Scoped Governance
Environments can be applied at different levels of granularity:
* **Workspace Level:** A default environment applied to all items within a collaborative space.
* **Item Level:** A specific environment tailored for a unique workload that requires specialized dependencies.

### 4. Persistence and Immutability
Once an environment is "published" or "finalized," it provides a stable foundation. Changes to an environment typically require a re-publishing phase to ensure that all compute nodes are synchronized with the new configuration.

## Standard Model
The standard model for environments follows a hierarchical attachment structure:

1.  **The Runtime Layer:** The base engine (e.g., Spark 3.x).
2.  **The Library Layer:** Layered on top of the runtime, consisting of public and custom packages.
3.  **The Configuration Layer:** Spark properties and environment variables that tune performance and connectivity.
4.  **The Attachment Layer:** The link between the Environment and the Item (Notebook/Job).

When an item is executed, the system instantiates a compute session based on the Environment's specifications, ensuring that the execution context is identical every time the item runs.

## Common Patterns

### The Tiered Environment Pattern
Organizations maintain separate environments for different stages of the lifecycle:
* **Dev Environment:** Contains experimental libraries and verbose logging.
* **Prod Environment:** A locked-down, optimized version with strictly versioned libraries for stability.

### The Shared Foundation Pattern
A single, robust environment is created at the workspace level. All notebooks in that workspace inherit these settings, ensuring that a team of data scientists is always working with the same set of tools and versions.

### The Specialized Workload Pattern
For tasks requiring high-memory or specific GPU-accelerated libraries (e.g., Deep Learning), a dedicated environment is created specifically for those high-intensity items, while standard ETL tasks use a "lightweight" environment.

## Anti-Patterns

*   **Inline Library Installation:** Using commands like `%pip install` within a notebook instead of defining them in an Environment. This leads to slow startup times and non-reproducible runs.
*   **The "Kitchen Sink" Environment:** Including every possible library in a single environment. This increases session start times and creates a higher risk of dependency conflicts.
*   **Hardcoding Configurations:** Placing Spark configurations directly in the code rather than utilizing the Environment’s configuration properties.
*   **Version Neglect:** Failing to pin library versions (e.g., using `library_x` instead of `library_x==1.2.3`), which can lead to breaking changes when the library is updated in public repositories.

## Edge Cases

*   **Dependency Conflicts:** When two required libraries require different versions of the same underlying dependency. This requires manual resolution or the use of "Conda" style environment files.
*   **Cold Start Latency:** Highly complex environments with numerous custom libraries may experience longer "cold start" times as the compute cluster must pull and install these dependencies across all nodes.
*   **Cross-Workspace Referencing:** In some architectures, an environment may be defined in one workspace but referenced in another. This can lead to governance challenges if the source environment is modified or deleted.
*   **Runtime Deprecation:** When the underlying engine (e.g., Spark 2.4) reaches end-of-life, environments built on that runtime must be migrated to a newer version, which may require code refactoring.

## Related Topics
* **Compute Contexts and Sessions:** How environments are instantiated during runtime.
* **Workspace Governance:** Managing permissions for who can create or modify environments.
* **CI/CD for Data Platforms:** Automating the deployment of environments across stages.
* **Library Management:** The specific mechanics of package resolution and installation.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |