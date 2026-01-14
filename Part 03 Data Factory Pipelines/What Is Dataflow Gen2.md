# What Is Dataflow Gen2

Canonical documentation for What Is Dataflow Gen2. This document defines concepts, terminology, and standard usage.

## Purpose
Dataflow Gen2 represents the evolution of cloud-based, low-code data transformation engines. It addresses the requirement for a unified data preparation experience that bridges the gap between self-service business intelligence and enterprise-scale data engineering. 

The primary problem space addressed by Dataflow Gen2 is the need for a scalable ETL (Extract, Transform, Load) mechanism that can handle high-volume data processing while providing native connectivity to diverse output destinations. Unlike its predecessors, which were primarily designed for internal consumption within specific reporting tools, Dataflow Gen2 is designed to function as a foundational component of a modern data estate, enabling the movement and transformation of data into structured analytical stores.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the architectural paradigm of "Generation 2" dataflows.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The architectural shift from "Gen1" (internal storage) to "Gen2" (external destination) models.
* The role of staging and high-scale compute engines in data transformation.
* The conceptual framework of low-code data integration for data warehousing.
* Lifecycle management of data within the transformation pipeline.

**Out of scope:**
* Specific UI navigation or button-click tutorials for vendor-specific platforms.
* Detailed syntax for underlying formula languages (e.g., Power Query M).
* Pricing models or licensing tiers.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Mashup Engine** | The core execution environment responsible for evaluating scripts and performing data transformations. |
| **Output Destination** | A target data store (e.g., Lakehouse, Warehouse, SQL Database) where the transformed data is persisted. |
| **Staging** | The process of temporarily storing intermediate data in a high-performance environment to optimize complex transformations. |
| **Compute Engine** | An auxiliary high-scale processing layer (often Spark-based) used to accelerate data operations by offloading work from the primary mashup engine. |
| **Dataflow Gen2** | A low-code, cloud-native ETL service that supports high-scale compute and multi-destination output. |
| **Refresh Orchestration** | The automated management of data ingestion, transformation, and loading cycles. |

## Core Concepts

### 1. Destination-Centric Architecture
The defining characteristic of Dataflow Gen2 is its ability to write data to external destinations. While previous iterations focused on "linked entities" or internal caches, Gen2 treats the dataflow as a pipeline that delivers data to a permanent, accessible storage layer.

### 2. Decoupled Compute and Storage
Dataflow Gen2 separates the logic of the transformation (the "Mashup") from the physical storage of the data. By utilizing staging environments, the system can leverage high-scale compute engines to perform joins, aggregations, and filters more efficiently than a standard row-based engine.

### 3. Low-Code Extensibility
It provides a graphical interface for complex ETL tasks, allowing non-developers to build sophisticated data pipelines. However, it remains extensible through underlying scripting languages, ensuring that complex logic can be codified when necessary.

### 4. Background Execution
Unlike interactive data preparation, Dataflow Gen2 is designed for asynchronous, scheduled, or triggered execution. This allows for the processing of large datasets without requiring an active user session.

## Standard Model
The standard model for Dataflow Gen2 follows a three-stage lifecycle:

1.  **Ingestion & Extraction:** Data is pulled from source systems (SaaS, On-premises, Cloud Databases) using a library of connectors.
2.  **Transformation & Staging:** 
    *   Data is initially loaded into a staging area.
    *   The Compute Engine evaluates the transformation logic.
    *   If "High-Scale Compute" is enabled, the engine translates the logic into a format optimized for distributed processing.
3.  **Loading:** The final, transformed dataset is written to the defined Output Destination. This stage includes schema mapping and the creation of physical tables in the target system.

## Common Patterns

*   **The Bronze-to-Silver Pipeline:** Using Dataflow Gen2 to ingest raw "Bronze" data from a landing zone, apply cleaning and normalization, and write it to a "Silver" layer in a Lakehouse.
*   **Incremental Refresh:** Only processing data that has changed since the last execution to reduce compute load and execution time.
*   **Modular Dataflows:** Breaking complex logic into multiple smaller dataflows that feed into one another, improving maintainability and troubleshooting.
*   **Data Type Standardization:** Using a centralized dataflow to enforce enterprise-wide data types and naming conventions before data reaches the warehouse.

## Anti-Patterns

*   **Row-by-Row Logic:** Attempting to perform complex, iterative row-level calculations that are better suited for procedural code or specialized compute.
*   **Neglecting Staging for Small Sets:** Enabling high-scale compute and staging for very small datasets, which can introduce unnecessary overhead and latency.
*   **Monolithic Dataflows:** Building a single dataflow with hundreds of queries, making it difficult to debug, version control, or optimize.
*   **Direct Source Querying for Heavy Joins:** Performing massive joins against a production source system rather than staging the data first, potentially impacting source system performance.

## Edge Cases

*   **Schema Evolution:** When the source schema changes (e.g., a column is renamed), the dataflow may fail or produce null values depending on the destination's schema enforcement policy.
*   **Binary Large Objects (BLOBs):** Processing extremely large unstructured files (images, videos) through a mashup engine can lead to memory exhaustion; these are typically better handled by specialized data movement tools.
*   **Gateway Bottlenecks:** When accessing on-premises data, the throughput of the Dataflow Gen2 is limited by the capacity and latency of the local data gateway.

## Related Topics

*   **Data Factory Pipelines:** Often used to orchestrate the execution of multiple Dataflow Gen2 activities.
*   **Lakehouse Architecture:** The primary storage paradigm often used as a destination for Dataflow Gen2.
*   **Power Query (M):** The underlying functional language used to define transformations.
*   **Data Mesh:** An organizational framework where Dataflow Gen2 can serve as the "Data Product" creation tool for domain teams.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |