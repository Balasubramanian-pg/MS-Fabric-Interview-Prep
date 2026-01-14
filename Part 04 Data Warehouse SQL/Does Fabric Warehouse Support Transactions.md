# Does Fabric Warehouse Support Transactions

Canonical documentation for Does Fabric Warehouse Support Transactions. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of this topic is to define the transactional capabilities and limitations of the Fabric Warehouse. In modern data architecture, ensuring data integrity during concurrent operations and complex ETL (Extract, Transform, Load) processes is critical. This documentation explains how the system maintains state consistency through transactional boundaries, preventing data corruption and ensuring reliable recovery.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the architectural behavior of the Fabric Warehouse engine.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* ACID (Atomicity, Consistency, Isolation, Durability) compliance within the warehouse engine.
* Transactional boundaries for Data Manipulation Language (DML) and Data Definition Language (DDL).
* Concurrency models and isolation levels.
* Multi-statement transaction support.

**Out of scope:**
* Transactional consistency across external Spark engines or Lakehouse files (outside the Warehouse boundary).
* Specific performance tuning for individual queries.
* Third-party orchestration tool transaction management.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **ACID** | A set of properties (Atomicity, Consistency, Isolation, Durability) that guarantee database transactions are processed reliably. |
| **Atomicity** | The "all or nothing" property; if any part of a transaction fails, the entire transaction is rolled back. |
| **Snapshot Isolation** | A concurrency control method where a transaction sees a consistent snapshot of the data as it existed at the start of the transaction. |
| **DML** | Data Manipulation Language; operations such as `INSERT`, `UPDATE`, and `DELETE`. |
| **DDL** | Data Definition Language; operations such as `CREATE`, `ALTER`, and `DROP`. |
| **Implicit Transaction** | A single SQL statement that is automatically treated as a standalone transaction. |
| **Explicit Transaction** | A group of statements wrapped in `BEGIN TRANSACTION` and `COMMIT/ROLLBACK` commands. |

## Core Concepts
The Fabric Warehouse is built on a distributed relational engine that prioritizes transactional integrity. Unlike traditional "Big Data" systems that may offer only "eventual consistency," the Warehouse provides full ACID support.

### 1. Transactional Scope
Transactions are scoped to the specific Warehouse database. While the underlying data is stored in Delta Lake format (Parquet), the Warehouse engine manages the metadata and transaction logs to ensure that SQL operations behave like a traditional relational database.

### 2. Multi-Statement Support
The engine supports grouping multiple DML statements into a single logical unit of work. This ensures that if a process involves updating a fact table and a dimension table simultaneously, both succeed or both fail.

### 3. DDL and DML Interoperability
Unlike many cloud data warehouses, the Fabric Warehouse allows DDL operations (like creating a table) to be included within a transaction alongside DML operations.

## Standard Model
The standard model for transactions in the Fabric Warehouse follows the **Snapshot Isolation** pattern.

*   **Read Consistency:** Readers do not block writers, and writers do not block readers. When a transaction begins, it views a version of the database consistent with the last committed state.
*   **Write Concurrency:** The engine uses optimistic concurrency control. If two transactions attempt to modify the same data simultaneously, the first to commit succeeds, while the second may encounter a conflict error.
*   **Durability:** Once a transaction is committed, the changes are persisted to the underlying scalable storage (OneLake) and are guaranteed to survive system failures.

## Common Patterns
*   **The Try-Catch Pattern:** Using `BEGIN TRY...END TRY` blocks in T-SQL to wrap `BEGIN TRANSACTION` and `COMMIT`, with a `ROLLBACK` in the `CATCH` block to ensure atomicity during errors.
*   **Batch Loading:** Wrapping large `INSERT INTO...SELECT` operations in a transaction to ensure that a partial load does not leave the warehouse in an inconsistent state.
*   **Schema Evolution:** Performing a `DROP TABLE` and `CREATE TABLE` (or `ALTER`) within a transaction to ensure the application layer never sees the table in a "missing" or "incomplete" state.

## Anti-Patterns
*   **Long-Running Transactions:** Keeping a transaction open for extended periods (e.g., hours) during manual data entry or slow network transfers. This can lead to excessive metadata growth and potential conflicts.
*   **Over-segmentation:** Committing every single row insertion individually in a loop. This creates significant overhead and negates the performance benefits of the distributed engine.
*   **Ignoring Conflict Errors:** Failing to implement retry logic for transactions that might fail due to concurrent write conflicts in a high-volume environment.

## Edge Cases
*   **Cross-Database Transactions:** While transactions are robust within a single Warehouse, transactions spanning multiple Warehouses or between a Warehouse and a Lakehouse may not support atomic "Two-Phase Commit" (2PC) protocols.
*   **Metadata-Only Operations:** Certain system-level configurations or administrative tasks may bypass the standard transactional log and should be treated as non-atomic.
*   **Session Disconnection:** If a session is terminated (e.g., network failure) before a `COMMIT` is issued, the engine automatically triggers a `ROLLBACK` to protect data integrity.

## Related Topics
*   **Concurrency Limits:** Understanding how many simultaneous transactions the engine can handle.
*   **OneLake Integration:** How the Warehouse writes to the underlying Delta Lake storage.
*   **T-SQL Surface Area:** The specific SQL commands supported for transaction management.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |