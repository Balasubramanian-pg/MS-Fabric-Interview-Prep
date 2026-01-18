# [Spark Checkpoint Cleanup](Part 20 EdgeCases BossLevel/Spark Checkpoint Cleanup.md)

Canonical documentation for [Spark Checkpoint Cleanup](Part 20 EdgeCases BossLevel/Spark Checkpoint Cleanup.md). This document defines concepts, terminology, and standard usage.

## Purpose
[Spark Checkpoint Cleanup](Part 20 EdgeCases BossLevel/Spark Checkpoint Cleanup.md) addresses the lifecycle management of persistent data artifacts generated during distributed processing. Checkpointing is a fault-tolerance mechanism that truncates the lineage of a Resilient Distributed Dataset (RDD) or records the state of a streaming query by saving data to reliable storage. Without a systematic cleanup process, these artifacts accumulate indefinitely, leading to storage exhaustion, increased operational costs, and potential performance degradation due to metadata bloat in the underlying file system.

The primary objective of checkpoint cleanup is to ensure that only the data necessary for recovery or lineage truncation is retained, while obsolete files are purged according to defined retention policies.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
**In scope:**
*   **Lifecycle Management:** The transition of checkpoint data from active to obsolete.
*   **Storage Reclamation:** The mechanisms for identifying and removing unneeded files.
*   **State Management:** The relationship between application state, watermarking, and physical file retention.
*   **Fault Tolerance Constraints:** Ensuring cleanup does not compromise the ability to recover from failures.

**Out of scope:**
*   Specific vendor-specific CLI commands (e.g., AWS CLI, Azure PowerShell).
*   Configuration of specific storage backends (e.g., HDFS block size tuning, S3 bucket versioning).
*   Performance tuning of the underlying network layer.

## Definitions
| Term | Definition |
|------|------------|
| **Checkpoint** | A snapshot of a dataset or application state persisted to reliable storage to truncate lineage or enable recovery. |
| **Lineage** | The graph of transformations that produced a specific dataset; checkpointing allows this graph to be discarded. |
| **Metadata File** | A file containing structural information about the checkpoint, such as offsets, schema, or versioning. |
| **Orphaned Checkpoint** | Checkpoint data remaining on storage after the associated Spark application has terminated or the state is no longer reachable. |
| **State Store** | The physical location where Structured Streaming stores intermediate state across micro-batches. |
| **Watermark** | A threshold used in streaming to determine how long to wait for late data before discarding state. |
| **Retention Policy** | A set of rules defining the duration or version count for which checkpoint data must be preserved. |

## Core Concepts

### Lineage Truncation vs. State Persistence
In batch processing (RDDs), checkpointing is used to break the dependency chain. Once a checkpoint is written and the lineage is truncated, the previous RDDs in the chain are no longer needed. In Structured Streaming, checkpointing is continuous and incremental, recording offsets and state updates to ensure "exactly-once" semantics.

### Automatic vs. Manual Cleanup
*   **Automatic Cleanup:** Spark manages the deletion of older checkpoint files during the execution of a job (e.g., cleaning up old shuffle files or older streaming state versions).
*   **Manual/External Cleanup:** The process of removing files that Spark's internal mechanisms fail to delete, often due to ungraceful application shutdowns or long-term storage policies.

### The Metadata Dependency
Checkpointing is not merely data storage; it includes metadata that points to specific data files. Cleanup must be "metadata-aware." Deleting data files without updating or removing corresponding metadata can lead to "File Not Found" exceptions during recovery.

## Standard Model

The standard model for [Spark Checkpoint Cleanup](Part 20 EdgeCases BossLevel/Spark Checkpoint Cleanup.md) follows a three-tier approach:

1.  **In-Application Management:** The Spark engine manages active state. For Structured Streaming, the `spark.sql.streaming.minBatchesToRetain` configuration dictates how many previous versions of the state are kept.
2.  **Graceful Shutdown:** Applications should be shut down using standard signals to allow the engine to perform final cleanup tasks.
3.  **External Lifecycle Policy:** A secondary process (such as a storage-level TTL or a scheduled maintenance script) identifies and removes directories associated with application IDs that are no longer active in the cluster manager.

## Common Patterns

### Time-to-Live (TTL) Policies
Implementing storage-level TTLs on checkpoint directories. This is effective for temporary batch jobs where the checkpoint is only relevant for the duration of the execution.

### Application-Linked Directories
Organizing checkpoint paths by Application ID or a unique Execution ID. This allows external cleanup scripts to safely delete entire directory trees once the cluster manager (YARN, Kubernetes, Mesos) marks the application as "Finished" or "Killed."

### Periodic Compaction
In streaming scenarios, state stores may perform periodic compaction or "vacuuming" to merge small delta files into larger files and delete the obsolete deltas.

## Anti-Patterns

### Manual Deletion of Active Checkpoints
Deleting files from a checkpoint directory while the Spark application is running. This leads to immediate job failure or data corruption, as Spark assumes the immutability of written checkpoint files.

### Infinite Retention
Failing to define a cleanup strategy, assuming that storage is "infinite" or "cheap." This eventually leads to "Small File Problems" on HDFS or excessive metadata API costs on object stores.

### Shared Checkpoint Directories
Using the same checkpoint directory for multiple independent Spark applications. This causes metadata collisions and makes it impossible to determine which application owns which file, preventing safe cleanup.

### Ignoring the "Commits" Directory
In Structured Streaming, the `commits` and `offsets` directories are as critical as the `state` directory. Cleaning up data files while leaving metadata files can lead to a state where the engine believes data exists when it does not.

## Edge Cases

### Ungraceful Shutdowns (Zombies)
If a Spark driver is killed (e.g., `kill -9`), the internal cleanup hooks are never executed. This results in orphaned files that must be handled by an external process.

### Long-Running Streams with No Watermarks
In Structured Streaming, if no watermark is defined, the state may grow indefinitely. Cleanup mechanisms will only remove old *versions* of the state, but the *size* of the current state will continue to increase, eventually leading to Out-of-Memory (OOM) errors or storage exhaustion.

### Cross-Version Upgrades
When upgrading Spark versions, the checkpoint format may change. Cleanup processes must be careful not to delete checkpoints that are required for a one-time migration or rollback strategy.

## Related Topics
*   **Spark Structured Streaming State Management:** The internal logic of how state is versioned.
*   **Fault Tolerance and Recovery:** How Spark uses checkpoints to resume processing.
*   **Object Store Consistency Models:** How eventual consistency (in older object stores) affects the visibility and deletion of checkpoint files.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |