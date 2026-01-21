# How Do You Handle Schema Drift

Canonical documentation for How Do You Handle Schema Drift. This document defines concepts, terminology, and standard usage.

## Purpose
Schema drift occurs when source systems—often managed by independent application teams—change their data structures without prior coordination with downstream data consumers. This topic addresses the problem of maintaining data integrity, pipeline stability, and analytical consistency in the face of evolving data contracts. It provides a framework for detecting, managing, and mitigating the impact of structural changes in data environments.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   Mechanisms for detecting structural changes in data.
*   Strategies for automated and manual schema evolution.
*   Architectural patterns for resilient data ingestion.
*   Classification of drift types (structural, semantic, and type-based).

**Out of scope:**
*   Specific vendor-specific syntax (e.g., specific Spark configurations or cloud-native ETL tool settings).
*   General data quality issues unrelated to schema structure (e.g., null values in a required field, unless the requirement itself changed).
*   Database migration scripts for application-level ORMs.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Schema Drift** | The phenomenon where source data metadata (fields, types, nesting) changes unexpectedly relative to the target system's expectations. |
| **Structural Drift** | Changes in the physical layout of data, such as added columns, removed columns, or renamed fields. |
| **Type Drift** | A change in the data type of an existing field (e.g., an Integer becoming a String) without a change in the field name. |
| **Semantic Drift** | A change in the meaning or context of data within a field, even if the structure and type remain identical. |
| **Schema-on-Read** | A data handling strategy where the schema is applied only when data is extracted from storage, allowing for high flexibility in drift. |
| **Schema-on-Write** | A strategy where data must conform to a predefined schema before it can be persisted in the target system. |
| **Data Contract** | A formal agreement between data producers and consumers defining the expected schema, quality, and semantics. |

## Core Concepts
The management of schema drift rests on three fundamental pillars:

1.  **Detection:** The ability of a system to identify a mismatch between the incoming data stream and the existing metadata registry. Detection can be proactive (validating against a contract) or reactive (failing a job or logging a warning).
2.  **Resilience:** The capacity of a data pipeline to continue processing despite drift. This often involves "graceful degradation" where unknown fields are preserved rather than discarded.
3.  **Evolution:** The process of updating the target schema to reflect the new state of the source. Evolution can be additive (non-breaking) or subtractive/modifying (breaking).

## Standard Model
The generally accepted model for handling schema drift follows a tiered lifecycle:

1.  **Ingestion Layer (The Buffer):** Data is ingested into a raw zone (Data Lake) using a schema-agnostic approach. This ensures that no data is lost even if the schema has drifted significantly.
2.  **Validation Layer (The Gatekeeper):** Incoming metadata is compared against a "Known Good" schema registry. 
3.  **Branching Logic:**
    *   **Compatible Changes:** If the drift is additive (e.g., a new optional column), the system automatically updates the metadata registry and proceeds.
    *   **Incompatible Changes:** If the drift is breaking (e.g., a column deletion or type change), the record or batch is diverted to a quarantine area.
4.  **Notification & Resolution:** Stakeholders are alerted. The schema is manually or programmatically updated, and quarantined data is reprocessed.

## Common Patterns
*   **The Catch-All Column (Shadowing):** Storing all incoming fields in a structured format (like JSON) alongside the primary columns. If drift occurs, the new data is captured in the "extra" column, preventing data loss.
*   **Schema Evolution Policy:** Defining explicit rules for the target (e.g., "Allow Additions," "Fail on Type Mismatch").
*   **Dead Letter Queues (DLQ):** Diverting records that fail schema validation to a separate storage location for later inspection and reprocessing.
*   **Versioned Schemas:** Maintaining multiple versions of a schema simultaneously to allow downstream consumers to migrate at their own pace.

## Anti-Patterns
*   **Hard-Coding Select Statements:** Using `SELECT *` or hard-coded column indices in pipelines, which leads to silent failures or misaligned data when columns are added or reordered.
*   **Silent Dropping:** Automatically discarding any fields not present in the target schema without logging or alerting.
*   **Auto-Migration of Breaking Changes:** Automatically deleting columns in a production warehouse because they were deleted in the source, leading to permanent data loss for historical analysis.
*   **Tight Coupling:** Building pipelines that require a manual code deployment for every upstream field addition.

## Edge Cases
*   **Renaming Fields:** Most automated systems detect a rename as one "Deletion" and one "Addition." This breaks historical continuity unless a mapping layer is introduced.
*   **Nested Structure Drift:** Changes deep within a JSON or Parquet hierarchy can be difficult to detect and often require recursive schema validation.
*   **Type Promotion vs. Demotion:** Changing an `Integer` to a `Long` (promotion) is usually safe, but changing a `String` to an `Integer` (demotion) requires complex casting and error handling for non-numeric strings.
*   **Reordered Fields:** In some file formats (like CSV), reordering columns is a catastrophic drift event, whereas in others (like JSON or Parquet), it is irrelevant.

## Related Topics
*   **Data Governance:** The overarching framework for managing data assets.
*   **Data Contracts:** The proactive method for preventing drift through inter-team agreements.
*   **Metadata Management:** The practice of cataloging and tracking changes to data structures over time.
*   **Idempotency in Data Pipelines:** Ensuring that reprocessing drifted data does not create duplicates.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |