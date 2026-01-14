# What Is The Vacuum Command

Canonical documentation for What Is The Vacuum Command. This document defines concepts, terminology, and standard usage.

## Purpose
The `VACUUM` command exists to manage the physical storage lifecycle of data in systems that utilize Multi-Version Concurrency Control (MVCC) or append-only storage architectures. Its primary purpose is to reclaim storage space occupied by "dead" or obsolete data versions, optimize data structures for performance, and ensure the long-term integrity of the data system by preventing resource exhaustion (such as transaction ID wraparound).

In modern data systems, updates and deletes often do not physically overwrite or remove data immediately to ensure transactional consistency and non-blocking reads. Without a vacuuming mechanism, these systems would suffer from "bloat," leading to degraded performance and eventual storage exhaustion.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative. While specific syntax varies across database engines (e.g., PostgreSQL, SQLite) and data lake formats (e.g., Delta Lake, Apache Iceberg), the underlying principles remain constant.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   The mechanics of space reclamation in versioned storage.
*   The relationship between MVCC and data obsolescence.
*   Maintenance of auxiliary structures (indexes, statistics).
*   Prevention of system-level exhaustion (e.g., ID wraparound).

**Out of scope:**
*   Specific SQL syntax for individual vendors.
*   Operating system-level disk defragmentation.
*   Application-level data archiving or purging logic.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Bloat** | The accumulation of disk space that is allocated to a table or index but contains no usable data (dead tuples). |
| **Dead Tuple** | A record version that is no longer visible to any active transaction and is eligible for removal. |
| **MVCC** | Multi-Version Concurrency Control; a method used to provide concurrent access to data without locking by maintaining multiple versions of records. |
| **Compaction** | The process of reorganizing data files to merge small files or remove deleted entries to improve read efficiency. |
| **Transaction ID (XID)** | A unique identifier assigned to a transaction; in many systems, these are finite and must be recycled. |
| **Free Space Map** | A data structure used by the system to track where reusable space exists within data files. |
| **Tombstone** | A marker placed on a record to indicate it has been deleted without immediately removing the data from the physical medium. |

## Core Concepts

### 1. Space Reclamation
In systems that do not perform "in-place" updates, an `UPDATE` is effectively a `DELETE` of the old version and an `INSERT` of the new version. The `VACUUM` command identifies these old versions (dead tuples) that are no longer needed by any running transaction and marks their space as available for future writes.

### 2. Index Maintenance
Indexes must be kept in sync with the underlying data. When data versions are removed, the corresponding entries in the index must also be cleaned up. Vacuuming ensures that indexes do not point to non-existent data and do not grow indefinitely.

### 3. Statistics Collection
Most vacuum implementations include a secondary function to analyze the distribution of data within the table. This metadata is provided to the query optimizer to ensure efficient execution plans.

### 4. Visibility Mapping
Vacuuming often updates a "visibility map," which tracks which pages of data contain only tuples visible to all transactions. This allows the system to skip these pages during subsequent vacuum cycles or certain types of queries (e.g., Index-Only Scans).

## Standard Model
The standard model for a vacuum operation follows a three-phase lifecycle:

1.  **Scanning:** The system scans the target data structures to identify records that are no longer visible to any active transaction.
2.  **Pruning/Cleanup:** The system removes references to dead records from indexes and marks the space in the data files as "free."
3.  **Free Space Tracking:** The reclaimed space is recorded in a Free Space Map (FSM), allowing the storage engine to prioritize these gaps for new data insertions before requesting more space from the operating system.

## Common Patterns

### Manual Vacuum
An administrator explicitly triggers the command. This is typically done after massive data migrations, bulk deletes, or during scheduled maintenance windows where resource contention is low.

### Automated/Background Vacuum
The system monitors "drift" or "bloat" thresholds and automatically spawns background processes to perform incremental vacuuming. This minimizes the impact on active workloads by spreading the maintenance overhead over time.

### Full/Compact Vacuum
A more intensive operation that not only marks space as reusable but physically reorganizes the data to return space to the operating system. This usually requires an exclusive lock on the data structure, preventing concurrent reads/writes.

## Anti-Patterns

*   **Neglecting Vacuuming:** Allowing bloat to grow unchecked, which leads to exponential performance degradation and eventual disk exhaustion.
*   **Over-Vacuuming:** Running intensive vacuum operations too frequently on systems with low churn, leading to unnecessary I/O overhead and CPU consumption.
*   **Long-Running Transactions:** Holding a transaction open for an extended period (hours or days). This prevents the vacuum command from reclaiming any data modified after the transaction started, as those versions must remain "visible" to the old transaction.
*   **Vacuuming During Peak Load:** Triggering a full, resource-intensive vacuum during periods of high application activity without proper throttling.

## Edge Cases

*   **Transaction ID Wraparound:** In systems using 32-bit transaction IDs, the system may reach a point where IDs "wrap around" to zero. If a vacuum has not "frozen" old records (marking them as visible to all future transactions), the system may enter a read-only state or suffer data corruption to prevent ID collisions.
*   **Empty Tables:** Vacuuming a table that is completely empty but has a high "high-water mark" (historical peak size) may not return space to the OS unless a "Full" or "Compact" variant is used.
*   **Interrupted Vacuum:** If a vacuum process is killed or the system crashes, most modern implementations are designed to be idempotent, meaning they can resume or restart without corrupting the underlying data.

## Related Topics
*   **Multi-Version Concurrency Control (MVCC):** The architectural reason why vacuuming is necessary.
*   **Write-Ahead Logging (WAL):** How vacuum operations are recorded to ensure durability.
*   **Query Optimization:** How the statistics gathered during vacuuming influence execution paths.
*   **Storage Compaction:** The equivalent concept in Log-Structured Merge (LSM) tree-based systems.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |