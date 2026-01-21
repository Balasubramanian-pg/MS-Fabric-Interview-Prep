# What Is Incremental Load

Canonical documentation for What Is Incremental Load. This document defines concepts, terminology, and standard usage.

## Purpose
Incremental load is a data integration strategy designed to synchronize data between a source system and a target system by processing only the records that have changed (created, updated, or deleted) since the last successful execution. 

The primary purpose of incremental loading is to optimize resource utilization. As datasets grow, performing a "Full Load" (extracting and replacing the entire dataset) becomes computationally expensive, time-consuming, and taxing on network bandwidth. Incremental loading addresses these constraints by reducing the volume of data transferred, thereby enabling higher frequency updates and minimizing the performance impact on both source and target environments.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Theoretical frameworks for identifying and capturing data changes.
* Logic for merging delta records into existing target datasets.
* State management and synchronization persistence.
* Strategies for maintaining data integrity during partial updates.

**Out of scope:**
* Specific vendor implementations (e.g., Snowflake Streams, AWS Glue bookmarks, Informatica mappings).
* Physical hardware optimization for data transfer.
* Specific programming language syntax for ETL/ELT.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Delta** | The set of data representing the difference between the current state of the source and the last synchronized state of the target. |
| **High-Water Mark (HWM)** | A metadata pointer or value (usually a timestamp or ID) that records the last successfully processed record to determine the starting point for the next load. |
| **Change Data Capture (CDC)** | A set of software design patterns used to determine and track the data that has changed so that action can be taken using the changed data. |
| **Idempotency** | The property of a process where multiple executions with the same input result in the same output without unintended side effects. |
| **Upsert** | A hybrid operation that updates a record if it already exists in the target or inserts it if it does not. |
| **Soft Delete** | A practice where a record is marked as "deleted" via a flag or status column rather than being physically removed from the database. |
| **Full Load** | The process of extracting the entire source dataset and overwriting the target, serving as the baseline for subsequent incremental loads. |

## Core Concepts

### 1. Change Detection
The fundamental requirement of an incremental load is the ability to identify which records are new or modified. This is achieved through three primary methods:
*   **Metadata-based:** Utilizing system columns like `last_updated_at` or `created_at`.
*   **Log-based:** Reading the transaction logs of the source database.
*   **Difference-based:** Comparing the source and target datasets in their entirety (often used as a last resort).

### 2. State Management
To ensure continuity, the integration system must maintain "state." This state tracks the progress of the synchronization. If the state is lost or corrupted, the system cannot reliably determine the next delta, often necessitating a re-initialization (Full Load).

### 3. Idempotency and Reliability
Incremental loads must be designed to handle failures. If a process fails mid-execution, re-running the process should not result in duplicate records or inconsistent data states. This is typically managed through unique constraints and atomic transactions.

## Standard Model
The standard model for an incremental load follows a specific lifecycle:

1.  **Initialization:** A one-time Full Load is performed to establish a baseline in the target system. The initial High-Water Mark is recorded.
2.  **Extraction:** The system queries the source for records where the change identifier (e.g., timestamp) is greater than the stored High-Water Mark.
3.  **Transformation:** Data is cleaned or reshaped as required by the target schema.
4.  **Loading:** The delta is applied to the target using "Append" (for new records) or "Upsert" (for modified records) logic.
5.  **Commitment:** Upon successful load, the High-Water Mark is updated to the maximum value found in the extracted delta.

## Common Patterns

### Append-Only
Used for immutable data, such as logs or telemetry. New records are simply added to the end of the target table. No updates to existing records are performed.

### Upsert (Merge)
Used for mutable entities (e.g., Customer profiles). The system checks for the existence of a primary key; if found, the record is updated; if not, it is inserted.

### Change Data Capture (CDC)
A sophisticated pattern that intercepts changes at the database engine level (usually via redo/transaction logs). This captures not only the final state of a record but also the intermediate changes and physical deletes.

## Anti-Patterns

*   **Over-reliance on Source Timestamps:** Trusting a `last_updated` column that is not automatically managed by the database engine, leading to missed updates if the application fails to update the timestamp.
*   **Ignoring Deletes:** Only capturing inserts and updates, resulting in "ghost" records in the target system that no longer exist in the source.
*   **Hard-Coding High-Water Marks:** Manually entering dates or IDs in code rather than using a dynamic state management system.
*   **Lack of Reconciliation:** Failing to periodically run a Full Load or a checksum validation to ensure the incremental logic hasn't drifted over time.

## Edge Cases

*   **Late-Arriving Data:** Records that are back-dated or arrive in the source system with a timestamp earlier than the current High-Water Mark (often due to distributed system latency).
*   **Clock Drift:** Discrepancies between the system clocks of the source, target, and integration server, which can cause records to be skipped.
*   **Schema Evolution:** When the structure of the source data changes (e.g., a new column is added) between incremental runs.
*   **Bulk Updates:** A single transaction in the source that updates millions of rows, potentially creating a delta so large it crashes the incremental process.

## Related Topics
*   **Extract, Transform, Load (ETL)**
*   **Data Synchronization**
*   **Idempotency in Data Pipelines**
*   **Event-Driven Architecture**
*   **Data Lakehouse Architecture**

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |