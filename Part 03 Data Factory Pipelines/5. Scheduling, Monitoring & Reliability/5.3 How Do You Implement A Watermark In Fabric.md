# How Do You Implement A Watermark In Fabric

Canonical documentation for How Do You Implement A Watermark In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
The implementation of a watermark addresses the requirement for **incremental data processing** within a unified data ecosystem. In large-scale data environments, performing a full refresh of data (Full Load) for every ingestion cycle is computationally expensive, time-consuming, and often unnecessary. 

A watermark serves as a state-tracking mechanism that allows a system to identify which data has already been processed and which data is new or modified since the last execution. This ensures data consistency, reduces resource consumption, and enables near real-time data availability.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative. While "Fabric" refers to the integrated data platform, the principles of watermarking described here apply to any stateful data movement or transformation engine within that ecosystem.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core logic of stateful ingestion and transformation.
* Metadata management for tracking progress.
* Strategies for identifying incremental changes (deltas).
* Theoretical boundaries of state persistence.

**Out of scope:**
* Specific syntax for Python, SQL, or proprietary expression languages.
* Configuration of specific UI-based connectors or third-party drivers.
* Performance tuning for specific hardware tiers.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Watermark** | A metadata value (typically a timestamp or a sequence number) that marks the boundary between processed and unprocessed data. |
| **High-Water Mark (HWM)** | The maximum value of the watermark column encountered during the most recent successful data processing cycle. |
| **Low-Water Mark (LWM)** | The starting point for a data pull, usually equal to the High-Water Mark of the previous cycle. |
| **Watermark Column** | The specific attribute in the source dataset (e.g., `LastModifiedDate`, `RowVersion`, `ID`) used to determine the delta. |
| **State Store** | A persistent storage location (table, file, or metadata service) where the current High-Water Mark is saved between executions. |
| **Monotonicity** | The property of a sequence where values only increase or stay the same, never decrease; essential for reliable watermarking. |

## Core Concepts
Implementing a watermark relies on three fundamental pillars:

1.  **State Persistence:** The system must "remember" where it left off. This requires a durable storage mechanism that survives the termination of the compute process.
2.  **Delta Identification:** The source data must possess an attribute that reliably indicates the order of arrival or modification. Without a reliable watermark column, incremental loading is impossible.
3.  **Idempotency:** The implementation should be designed such that if a process fails mid-way, re-running it with the same watermark does not result in duplicate data or corrupted states.

## Standard Model
The standard model for implementing a watermark follows a cyclical four-stage process:

1.  **Lookup:** Retrieve the current High-Water Mark from the State Store.
2.  **Extraction:** Query the source system for all records where the Watermark Column is greater than (or equal to, depending on logic) the retrieved High-Water Mark.
3.  **Ingestion/Transformation:** Process the retrieved records and move them to the destination.
4.  **Update:** Upon successful completion of the data movement, identify the new maximum value from the processed records and update the State Store with this new High-Water Mark.

## Common Patterns
### 1. Temporal Watermarking
Uses a timestamp column (e.g., `updated_at`). This is the most common pattern but is sensitive to clock skew between the source system and the processing engine.

### 2. Sequential/Identity Watermarking
Uses an incrementing integer (e.g., `transaction_id`). This is highly reliable for append-only logs but does not capture updates to existing records unless those updates generate a new ID.

### 3. Version-Based Watermarking
Uses a database-native versioning feature (e.g., SQL Server `rowversion`). This is the most robust method for capturing both inserts and updates, as the value is guaranteed to change and be unique across the database.

### 4. Look-Back Window
A pattern where the Low-Water Mark is set slightly earlier than the previous High-Water Mark (e.g., HWM minus 5 minutes). This accounts for "in-flight" transactions that may have been committed to the source just as the previous cycle was reading.

## Anti-Patterns
*   **Using Non-Monotonic Columns:** Attempting to watermark on a column that can be updated to a lower value, which causes the process to skip data.
*   **Updating State Before Success:** Updating the High-Water Mark in the State Store before the data has been successfully committed to the destination. This leads to data loss if the process fails.
*   **Hardcoding Watermarks:** Placing the watermark value directly in the code or pipeline logic rather than using a dynamic State Store.
*   **Ignoring Deletions:** Relying solely on watermarks to track changes. Watermarks generally cannot detect deleted rows; a separate "Soft Delete" or "Change Data Capture (CDC)" mechanism is required for deletions.

## Edge Cases
*   **Clock Skew:** If the source system's clock is ahead of the orchestrator's clock, data might be missed.
*   **Late-Arriving Data:** In distributed systems, a record with an older timestamp might arrive at the source after a newer record has already been processed by the watermark logic.
*   **Duplicate Values:** If multiple records share the exact same watermark value (e.g., the same millisecond), the logic must use `>=` and then handle potential duplicates at the destination to ensure no data is missed.
*   **Null Values:** Watermark columns containing NULLs will typically be excluded from standard comparison filters, leading to "silent" data loss.

## Related Topics
*   **Change Data Capture (CDC):** A more advanced method of tracking changes that includes deletes.
*   **Idempotent Consumers:** Design patterns for ensuring that processing the same data twice has no unintended side effects.
*   **Acid Properties:** Ensuring the State Store update and data load are treated with transactional integrity.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |