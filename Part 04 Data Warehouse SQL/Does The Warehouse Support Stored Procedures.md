# Does The Warehouse Support Stored Procedures

Canonical documentation for Does The Warehouse Support Stored Procedures. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of this topic is to define the architectural role, capability, and governance of stored procedures within a data warehouse environment. It addresses the fundamental requirement for executing complex, multi-step logic directly on the data platform to minimize data movement, enforce security boundaries, and automate administrative or transformational workflows.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The definition and functional requirements of stored procedures in a warehouse context.
* The distinction between procedural logic and declarative queries.
* Architectural patterns for server-side execution.
* Security and lifecycle management of procedural code.

**Out of scope:**
* Specific syntax for vendor-specific languages (e.g., T-SQL, PL/SQL, Scripting in BigQuery, Snowflake Scripting).
* Performance tuning for specific hardware configurations.
* Comparison of specific cloud provider pricing models for compute.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| Stored Procedure | A collection of procedural and declarative statements stored within the warehouse that can be executed as a single unit. |
| Procedural Logic | Code that defines *how* to perform a task using control flow (loops, conditionals, variables), as opposed to declarative logic which defines *what* data to retrieve. |
| Side Effect | A change in the state of the database (e.g., INSERT, UPDATE, DELETE, or DDL) resulting from the execution of a procedure. |
| Execution Context | The security and session environment under which a procedure runs, determining permissions and resource access. |
| Idempotency | The property of a procedure where multiple executions under the same conditions produce the same result without unintended side effects. |
| UDF (User Defined Function) | A routine that returns a value and is typically restricted from performing side effects, often confused with but distinct from stored procedures. |

## Core Concepts

### 1. Server-Side Execution
Stored procedures reside and execute within the warehouse's compute layer. This eliminates the "network round-trip" penalty associated with client-side applications sending individual SQL statements sequentially.

### 2. Encapsulation and Abstraction
Procedures allow complex business logic or data engineering workflows to be abstracted behind a single call interface. This enables modularity, where the underlying logic can be updated without changing the interface used by calling applications.

### 3. Procedural vs. Set-Based Processing
While warehouses are optimized for set-based operations (SQL), stored procedures provide the "glue" logic (if-then-else, while loops) necessary to orchestrate these set-based operations in a specific sequence.

### 4. Security and Access Control
Stored procedures act as a security layer. Users can be granted permission to execute a procedure without having direct access to the underlying tables, ensuring data is only modified or accessed through approved logic.

## Standard Model
The standard model for stored procedures in a modern warehouse involves three primary layers:

1.  **The Interface:** The signature (name and parameters) used by the orchestrator or user to invoke the logic.
2.  **The Logic Engine:** The procedural code (often SQL-based or a high-level language like JavaScript or Python) that handles control flow and variable assignment.
3.  **The Data Operation:** The declarative SQL statements executed by the procedure to perform data manipulation (DML) or definition (DDL).

In this model, the warehouse acts as both the storage engine and the application server for data-centric logic.

## Common Patterns

### Administrative Automation
Using procedures to automate maintenance tasks such as partition management, index rebuilding (in legacy systems), or metadata logging.

### Dynamic SQL Generation
Constructing and executing SQL statements at runtime based on input parameters. This is frequently used for generic data loading patterns where table names or column lists are passed as variables.

### Transactional Orchestration
Wrapping multiple DML statements in a single transaction block within a procedure to ensure atomicity (all-or-nothing execution).

### ELT (Extract, Load, Transform) Logic
Performing complex transformations within the warehouse after data has been loaded into staging tables, often involving multi-step dependencies that cannot be expressed in a single `INSERT INTO ... SELECT` statement.

## Anti-Patterns

### "Black Box" Logic
Embedding critical business logic in stored procedures without external version control or documentation, making it difficult for upstream or downstream systems to understand data lineage.

### Row-by-Row Processing (RBAR)
Using procedural loops to process individual rows rather than utilizing the warehouse's native set-based processing capabilities. This leads to significant performance degradation.

### Over-Orchestration
Using stored procedures to handle complex workflow orchestration (retries, parallelization, external triggers) that would be better managed by a dedicated orchestration tool (e.g., Airflow, Dagster).

### Tight Coupling
Creating procedures that are overly dependent on specific schema states, leading to fragile pipelines that break frequently during upstream schema changes.

## Edge Cases

### Long-Running Timeouts
Warehouses often impose hard limits on the execution duration of a single procedure. Logic that exceeds these limits must be decomposed into smaller, asynchronous units.

### Cross-Database/Cross-Instance Calls
Procedures that attempt to access data across different warehouse instances or disparate cloud regions may encounter security, latency, or unsupported capability issues.

### Language Runtimes
Modern warehouses may support multiple languages (e.g., Python, Java) within procedures. An edge case exists when the procedural logic requires libraries or binaries not supported by the warehouse's restricted runtime environment.

## Related Topics
* **Data Orchestration:** The external management of task dependencies.
* **User Defined Functions (UDFs):** For scalar or table-valued logic without side effects.
* **Version Control for Databases:** Managing the lifecycle of procedural code via Git.
* **Idempotent Pipeline Design:** Ensuring procedures can be safely re-run.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |