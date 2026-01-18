# [Can You Use Dax In Fabric](Part 05 RealTime Science PowerBI/Can You Use Dax In Fabric.md)

Canonical documentation for [Can You Use Dax In Fabric](Part 05 RealTime Science PowerBI/Can You Use Dax In Fabric.md). This document defines concepts, terminology, and standard usage.

## Purpose
The integration of Data Analysis Expressions (DAX) within a unified data platform addresses the requirement for high-performance, multi-dimensional analytical modeling. This topic exists to define the role of DAX as the primary calculation engine for semantic layers within a distributed data architecture. It establishes how a functional expression language interacts with modern storage formats (such as Parquet/Delta) and unified compute engines.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the architectural placement of DAX within the Fabric ecosystem.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The role of DAX in defining business logic and metrics.
* Interaction between DAX and the unified storage layer (OneLake).
* The conceptual boundaries between DAX and other query languages (SQL, KQL).
* Theoretical application of DAX in Direct Lake, Import, and DirectQuery modes.

**Out of scope:**
* Step-by-step tutorials for writing specific DAX formulas.
* Comparison of DAX vs. Python for machine learning.
* Specific licensing or pricing tiers for Fabric capacity.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| DAX (Data Analysis Expressions) | A library of functions and operators that can be combined to build formulas and expressions for data modeling. |
| Semantic Model | A logical layer that abstracts underlying data sources, providing a business-friendly view of data through tables, relationships, and DAX measures. |
| Direct Lake | A storage mode that allows the analytical engine to read Delta tables directly from a data lake without importing or duplicating data. |
| Evaluation Context | The environment in which a DAX formula is operated, consisting of Filter Context and Row Context. |
| OneLake | The unified, multi-cloud data lake that serves as the single source of truth for all data within the platform. |

## Core Concepts
The use of DAX in a unified platform is centered on three fundamental pillars:

1.  **The Analytical Engine:** DAX is the native language of the Tabular engine. In the context of a modern data fabric, this engine acts as the compute layer for high-speed aggregations and complex business logic.
2.  **Logic Centralization:** DAX allows for "write once, use everywhere" logic. By defining a measure in a semantic model, the calculation remains consistent regardless of which reporting tool or interface consumes it.
3.  **Direct Access to Open Formats:** Unlike traditional implementations where DAX required proprietary data formats, the modern architecture allows DAX to query open-source Delta tables directly, bridging the gap between data engineering and business intelligence.

## Standard Model
The standard model for utilizing DAX within a unified fabric involves the following hierarchy:

*   **Storage Layer:** Data resides in OneLake as Delta/Parquet files.
*   **Abstraction Layer:** A Lakehouse or Warehouse provides a SQL endpoint, but the **Semantic Model** is the primary host for DAX.
*   **Execution:** When a user interacts with a report, the analytical engine generates a DAX query. This query is resolved by either accessing the data in-memory (Import), querying the Delta files directly (Direct Lake), or passing a translated query to the source (DirectQuery).

## Common Patterns
*   **Measure-Based Modeling:** Defining all business logic as measures rather than calculated columns to ensure maximum flexibility and performance.
*   **DAX Query View:** Utilizing DAX as a query language (rather than just a calculation language) to inspect data and validate logic directly against the semantic model.
*   **Direct Lake Integration:** Leveraging the ability of DAX to perform at "Import-like" speeds by reading directly from the lakehouse without the latency of data refresh cycles.

## Anti-Patterns
*   **DAX for ETL:** Using DAX (specifically calculated columns) to perform heavy data transformation or cleansing. These operations should be pushed "upstream" to the Data Engineering layer (Spark or SQL).
*   **Over-Complexity in Row Context:** Creating deeply nested iterators (e.g., `FILTER` inside `SUMX`) that can lead to performance degradation on large-scale datasets.
*   **Bypassing the Semantic Layer:** Attempting to replicate complex DAX logic in SQL views for reporting purposes, which leads to "logic drift" and maintenance overhead.

## Edge Cases
*   **Large-Scale Direct Lake Fallback:** When a semantic model exceeds the memory limits of its assigned capacity, the engine may "fall back" from Direct Lake to DirectQuery mode. This changes the execution from DAX-native to SQL-translated, which may impact performance and supported DAX functions.
*   **Cross-Workspace Dependencies:** Using DAX to query a semantic model that resides in a different workspace or region, introducing potential latency and security permission complexities.
*   **DAX on Non-Relational Data:** While DAX is optimized for star schemas, it can be applied to flat files or document-style data, though this often requires significant modeling overhead to maintain performance.

## Related Topics
*   **Direct Lake Storage Mode:** The technical mechanism for DAX-to-Delta communication.
*   **Tabular Object Model (TOM):** The API used to manage the structures that DAX operates upon.
*   **Power BI Semantic Models:** The primary implementation vehicle for DAX.
*   **OneLake Integration:** The underlying storage architecture.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |