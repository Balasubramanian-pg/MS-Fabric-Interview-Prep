# How Do You Perform An Upsert In Fabric

Canonical documentation for How Do You Perform An Upsert In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of an upsert (a portmanteau of "update" and "insert") in the Fabric ecosystem is to maintain data integrity and synchronization between source systems and the centralized data lake (OneLake). It addresses the need to reconcile incoming data streams with existing records, ensuring that new information is added while existing information is modified to reflect the most current state without creating duplicate entries.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the architectural patterns within the Fabric framework rather than specific code snippets.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Logical operations for merging datasets within Fabric Lakehouses and Warehouses.
* Theoretical frameworks for Change Data Capture (CDC) within a unified SaaS analytics platform.
* Consistency models and transaction boundaries relevant to upsert operations.

**Out of scope:**
* Specific syntax for third-party connectors not native to the Fabric environment.
* Performance tuning for specific hardware configurations (as Fabric is a managed SaaS).
* Legacy non-Delta storage formats.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Upsert** | A database operation that updates an existing row if a specified value already exists in a table, or inserts a new row if the value does not exist. |
| **Delta Lake** | The open-source storage layer that brings ACID (Atomicity, Consistency, Isolation, Durability) transactions to Fabric's OneLake. |
| **Merge** | The primary command/operation used in Spark SQL and T-SQL to perform upserts by joining a source and target table. |
| **OneLake** | The unified, logical data lake for the entire Fabric tenant, serving as the substrate for all data operations. |
| **ACID Compliance** | A set of properties that guarantee database transactions are processed reliably, essential for successful upsert operations. |
| **Staging Table** | A temporary storage structure used to hold incoming data before it is merged into the final destination table. |

## Core Concepts
The fundamental ideas governing upserts in Fabric revolve around the **Delta Lake** format and the **V-Order** optimization. 

1.  **Key-Based Matching:** Every upsert requires a deterministic key (or set of keys) to identify whether a record in the source already exists in the target.
2.  **Transaction Logs:** Fabric utilizes Delta logs to ensure that an upsert is atomic. If a merge fails halfway through, the system rolls back to the previous state, preventing data corruption.
3.  **Schema Evolution:** Upserts in Fabric can optionally handle changes in the source data structure (e.g., adding a new column) during the merge process.
4.  **Unified Storage:** Because Fabric uses a single storage format (Delta/Parquet), an upsert performed by a Spark engine is immediately visible to the SQL analytics endpoint.

## Standard Model
The standard model for performing an upsert in Fabric follows the **Load-to-Staging-then-Merge** pattern:

1.  **Ingestion:** Data is ingested from the source into a staging area (either a temporary table or a specific directory in OneLake).
2.  **Validation:** The staging data is validated for schema consistency and data quality.
3.  **The Merge Operation:** A `MERGE` statement is executed. This statement defines:
    *   The **Target**: The permanent table in the Lakehouse or Warehouse.
    *   The **Source**: The staging data.
    *   The **Join Condition**: The keys used to match records.
    *   **When Matched**: The logic to update existing columns.
    *   **When Not Matched**: The logic to insert new rows.

## Common Patterns
*   **T-SQL Merge:** Used primarily within the Fabric Data Warehouse or via the SQL Analytics Endpoint of a Lakehouse. It provides a familiar relational syntax for data engineers.
*   **Spark SQL/PySpark Merge:** Utilized within Fabric Notebooks. This pattern is preferred for high-volume data or when complex transformations are required during the upsert process.
*   **Dataflow Gen2 Upsert:** A low-code approach where the "Update" method is selected in the destination settings, allowing the Fabric orchestration engine to handle the underlying merge logic.
*   **SCD Type 1 vs. Type 2:** 
    *   *Type 1:* Overwrites existing data (standard upsert).
    *   *Type 2:* Maintains history by marking old records as inactive and inserting new ones (historical upsert).

## Anti-Patterns
*   **Row-by-Row Updates:** Attempting to loop through records and update them individually. This is highly inefficient in a distributed big data environment like Fabric.
*   **Full Table Overwrites:** Overwriting an entire partition or table to update a small percentage of records. This leads to excessive compute costs and storage churn.
*   **Ignoring Partition Pruning:** Performing a merge without filtering by partition keys (if the table is partitioned), which forces the engine to scan the entire dataset.
*   **Lack of Vacuuming:** Failing to manage old file versions created by upserts, leading to increased storage costs and slower metadata resolution.

## Edge Cases
*   **Duplicate Source Keys:** If the source data contains multiple records with the same key, the merge operation will fail or produce non-deterministic results. Deduplication must occur before the upsert.
*   **Schema Mismatch:** When the source has fewer or more columns than the target. This requires explicit handling via `autoMerge` options or manual schema mapping.
*   **Concurrent Writes:** Two different processes attempting to upsert into the same table simultaneously. Fabric handles this via optimistic concurrency control, but one process may fail and require a retry.
*   **Null Key Matching:** Upserts typically fail to match on `NULL` values. Standard practice involves coalescing nulls or filtering them out before the merge.

## Related Topics
*   **Change Data Capture (CDC):** The process of identifying and capturing changes made to a database.
*   **OneLake Shortcuts:** How virtualized data interacts with upsert logic.
*   **V-Order Optimization:** How Fabric optimizes the underlying Parquet files for faster read/write during merges.
*   **Medallion Architecture:** The framework (Bronze/Silver/Gold) where upserts typically occur at the Silver layer.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |