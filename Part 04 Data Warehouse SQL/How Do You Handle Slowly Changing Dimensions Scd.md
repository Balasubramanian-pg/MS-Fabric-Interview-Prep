# How Do You Handle Slowly Changing Dimensions (SCD)

Canonical documentation for How Do You Handle Slowly Changing Dimensions (SCD). This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of Slowly Changing Dimensions (SCD) management is to provide a systematic methodology for capturing, storing, and managing data that changes unpredictably over time. In dimensional modeling, attributes of a dimension (e.g., a customer's address or a product's category) are not static. SCD techniques ensure that the data warehouse maintains historical integrity, allowing analysts to report on data as it exists today or as it existed at the time a specific transaction occurred.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the logical architectural patterns rather than specific database engine syntax.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core methodologies for tracking attribute changes (Types 0 through 6).
* Temporal logic and record versioning.
* Maintenance of dimensional integrity and historical accuracy.
* Relationship between surrogate keys and natural keys in the context of SCD.

**Out of scope:**
* Specific ETL/ELT tool configurations (e.g., Informatica, dbt, Talend).
* Physical storage optimization (e.g., partitioning, indexing).
* Real-time streaming data architectures (unless specifically applied to SCD logic).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Natural Key** | The identifier assigned to an entity in the source operational system (e.g., Employee ID). |
| **Surrogate Key** | A unique, system-generated identifier used as the primary key in the dimension table, independent of the natural key. |
| **Effective Date** | The timestamp or date marking the beginning of a record's validity. |
| **End Date** | The timestamp or date marking the conclusion of a record's validity. |
| **Current Flag** | A boolean or indicator used to quickly identify the most recent version of a dimension member. |
| **Point-in-Time (PIT)** | The ability to reconstruct the state of a dimension as it appeared at any specific moment in history. |
| **Grain** | The level of detail or the definition of what a single row in the dimension table represents. |

## Core Concepts
The management of SCDs rests on three fundamental pillars:

1.  **Identity vs. Attribute:** Distinguishing between the entity itself (the Natural Key) and the characteristics of that entity (Attributes). While the identity remains constant, attributes evolve.
2.  **Temporal Context:** Every fact (transaction) in a data warehouse is associated with a point in time. To ensure accuracy, that fact must be linked to the version of the dimension that was true when the fact occurred.
3.  **History Preservation:** The decision-making process regarding whether to overwrite existing data (losing history) or create new records (preserving history).

## Standard Model
The standard model for handling SCDs is based on the Kimball Methodology. It requires a dimension table structure that supports both the current state and historical snapshots. 

In a standard historical model (Type 2), the table must include:
*   A **Surrogate Key** (Primary Key).
*   The **Natural Key** (to group versions of the same entity).
*   **Version Metadata** (Effective Date, End Date, and Current Flag).
*   **Descriptive Attributes** (The data being tracked).

## Common Patterns

### Type 0: Fixed
Attributes are never updated. The value captured at the time the record is first loaded remains permanent. This is used for "Original" values (e.g., `Original_Start_Date`).

### Type 1: Overwrite
The old value is replaced by the new value. No history is kept. This is used when historical accuracy is not required or when correcting data errors.
*   **Pros:** Simple, saves space.
*   **Cons:** Destroys historical context for existing facts.

### Type 2: Add New Row (The Gold Standard)
A new record is created for every change. The previous record is "closed" by updating its `End Date` and `Current Flag`.
*   **Pros:** Full historical tracking; allows for accurate point-in-time reporting.
*   **Cons:** Increases table size; requires surrogate key management.

### Type 3: Add New Column
The current value and the "previous" value are stored in the same row.
*   **Pros:** Allows comparing current vs. immediate previous state.
*   **Cons:** Limited history (only two versions); rarely used in modern architectures.

### Type 4: History Table
The main dimension table only holds current data (Type 1 style), but every change is logged to a separate "History" or "Shadow" table.
*   **Pros:** Keeps the primary dimension table small and performant.

### Type 6: Hybrid (1 + 2 + 3)
Combines techniques. A row has an "Effective Date" (Type 2) but also contains a "Current Value" column that is updated across all historical rows for that natural key (Type 1).
*   **Pros:** Allows reporting on historical facts using current attribute values without complex joins.

## Anti-Patterns
*   **Using Natural Keys as Primary Keys:** This prevents the use of Type 2 SCDs, as you cannot have multiple rows with the same primary key.
*   **Null End Dates:** Using `NULL` for the current record's `End Date` can lead to logic errors in join conditions. A "High Date" (e.g., `9999-12-31`) is the standard practice.
*   **Over-versioning:** Applying Type 2 logic to high-volatility attributes (e.g., a "Current Balance" that changes every minute) leads to massive table bloat. These should be moved to Fact tables or "Mini-dimensions."
*   **Ignoring Time Zones:** Failing to standardize the timezone of `Effective_Date` and `End_Date` across different source systems.

## Edge Cases
*   **Late-Arriving Data:** When a change event from three months ago arrives today. The system must retroactively insert the record, update the "End Date" of the record that *should* have preceded it, and potentially re-link historical facts.
*   **Retroactive Changes:** When a source system corrects a value that was "wrong" for a period of time. This requires "deleting" or "invalidating" a version in the history.
*   **Deletions in Source:** Handling records that are deleted in the source system. Standard practice is a "Soft Delete" where the record is marked as inactive or ended, rather than physically removed.
*   **Multiple Changes in One Batch:** If an attribute changes twice between ETL cycles, the intermediary state may be lost unless the source system provides a full audit log/CDC (Change Data Capture).

## Related Topics
*   **Surrogate Key Management:** The process of generating and mapping keys.
*   **Change Data Capture (CDC):** Techniques for identifying changed data in source systems.
*   **Dimensional Modeling:** The broader framework of Star and Snowflake schemas.
*   **Bitemporal Modeling:** Tracking both when an event happened in the real world and when it was recorded in the database.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial canonical documentation |