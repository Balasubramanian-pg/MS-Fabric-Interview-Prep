# Can You Use Lookup Activities

Canonical documentation for Can You Use Lookup Activities. This document defines concepts, terminology, and standard usage.

## Purpose
The Lookup Activity exists to facilitate data-driven orchestration within automated workflows and data pipelines. Its primary purpose is to retrieve external datasets or metadata to inform the execution logic of subsequent activities. By bridging the gap between static pipeline definitions and dynamic source data, Lookup Activities enable workflows to adapt to the state of the environment they operate within.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core functionality of retrieving data for control-flow decision-making.
* Theoretical boundaries regarding data volume and execution context.
* Logical integration with iterative and conditional structures.

**Out of scope:**
* Specific vendor implementations (e.g., Azure Data Factory, AWS Step Functions, Informatica).
* Performance tuning for specific database engines.
* Syntax-specific query languages (SQL, KQL, NoSQL).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Lookup Activity** | A discrete unit of work in a pipeline that queries a data source and returns the result set to the pipeline's execution context. |
| **Singleton Result** | A configuration where the activity is restricted to returning only the first row or a single value from the result set. |
| **Collection Result** | A configuration where the activity returns an array of objects representing multiple rows or records. |
| **Watermark** | A value retrieved via a lookup (often a timestamp or ID) used to determine the starting point for incremental data processing. |
| **Source Dataset** | The external repository (database, file system, API) from which the Lookup Activity retrieves information. |

## Core Concepts
The Lookup Activity operates on three fundamental principles:

1.  **External Awareness**: Unlike static variables, a Lookup Activity allows a pipeline to "ask a question" of its environment at runtime.
2.  **Output Binding**: The data retrieved is stored in the pipeline's memory (JSON format typically) and must be accessible via expressions by downstream activities.
3.  **Synchronous Execution**: The pipeline execution generally pauses until the Lookup Activity completes its retrieval and returns the data to the control plane.

## Standard Model
The standard model for a Lookup Activity follows a Request-Response pattern:

1.  **Configuration**: The user defines a connection (linked service) and a specific query or file path.
2.  **Execution**: The orchestration engine executes the query against the source system.
3.  **Serialization**: The source system's native format is converted into a standardized structured format (usually JSON).
4.  **Availability**: The resulting object is appended to the pipeline's execution metadata, allowing subsequent activities to reference it using a path-based syntax (e.g., `activity('LookupName').output.value`).

## Common Patterns
*   **Dynamic Iteration**: Retrieving a list of table names or file paths from a configuration database to drive a "For Each" loop.
*   **Incremental Loading (Watermarking)**: Querying the maximum `LastModifiedDate` from a target table to filter the source query for the next load.
*   **Validation Gate**: Checking for the existence of a "Success" flag or a specific record before proceeding with a high-risk operation (e.g., dropping a table).
*   **Parameter Injection**: Fetching environment-specific configurations (like API endpoints or batch IDs) that are stored in a central management table.

## Anti-Patterns
*   **Large Data Extraction**: Using a Lookup Activity to retrieve thousands or millions of rows. Lookups are intended for metadata and small control-sets; large data movement should be handled by dedicated "Copy" or "Data Flow" activities.
*   **Complex Business Logic**: Embedding heavy transformation logic within the Lookup's SQL/Query statement. This obscures logic and makes debugging difficult.
*   **Polling Loops**: Using a Lookup Activity inside a tight loop to check for a condition (e.g., "Is the file there yet?"). This is inefficient and can lead to unnecessary resource consumption; "Wait" or "Webhook" activities are preferred.
*   **Ignoring Empty Results**: Failing to provide a conditional check for when a Lookup returns zero rows, which often leads to downstream "Null Reference" errors.

## Edge Cases
*   **Empty Result Sets**: When a query returns no data, the activity may succeed but return a null or empty array. Pipelines must be designed to handle this state without failing.
*   **Data Type Mismatch**: Source systems may return data types (e.g., GUIDs, Geospatial data) that the orchestration engine's JSON parser cannot natively represent, leading to truncation or string conversion.
*   **Timeout Limits**: Most orchestration engines impose a maximum execution time on Lookup Activities. If the source query is unoptimized, the lookup may fail even if the data exists.
*   **Result Set Size Limits**: There is typically a hard limit on the payload size (e.g., 4MB or 5,000 rows) that a Lookup Activity can pass to the pipeline context. Exceeding this causes a runtime error.

## Related Topics
*   **Iterative Activities (For-Each)**: Often the primary consumer of Lookup Collection Results.
*   **Conditional Logic (If-Condition)**: Used to evaluate the Singleton Results of a Lookup.
*   **Variables and Parameters**: Used to store or pass the values retrieved by a Lookup.
*   **Connectors/Linked Services**: The underlying infrastructure that enables the Lookup to reach the source system.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |