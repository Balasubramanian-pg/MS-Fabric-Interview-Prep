# [Onelake Ghost Files](Part 20 EdgeCases BossLevel/Onelake Ghost Files.md)

Canonical documentation for [Onelake Ghost Files](Part 20 EdgeCases BossLevel/Onelake Ghost Files.md). This document defines concepts, terminology, and standard usage.

## Purpose
The concept of Ghost Files addresses the discrepancy between the logical namespace (metadata) and the physical storage layer in a unified distributed data lake. In high-scale, distributed environments, the state of a file may become inconsistent due to asynchronous operations, distributed transaction failures, or caching latencies. This documentation establishes a framework for understanding, identifying, and managing these inconsistencies to ensure data integrity and system reliability.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the architectural phenomenon of ghosting within unified storage environments.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The lifecycle of file metadata vs. physical data blocks.
* Mechanisms leading to state divergence (Ghosting).
* Theoretical boundaries of eventual consistency in a global namespace.
* Validation logic for file existence.

**Out of scope:**
* Specific vendor-specific recovery tools or CLI commands.
* Troubleshooting specific cloud provider outages.
* Physical hardware failure analysis.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Ghost File** | A metadata entry that references a file or object which is either physically missing, inaccessible, or in an indeterminate state within the storage layer. |
| **Metadata Layer** | The logical catalog or namespace that tracks file names, paths, permissions, and pointers to physical data. |
| **Storage Layer** | The physical or virtualized block/object storage where the actual data bits reside. |
| **Orphaned Entry** | A specific type of ghost file where the metadata exists but the underlying data has been deleted or moved without a corresponding metadata update. |
| **Shadow Reference** | A condition where data exists in the storage layer but is no longer indexed or reachable via the metadata layer. |
| **Atomic Divergence** | The momentary window during a write or delete operation where the metadata and storage layers are out of sync. |

## Core Concepts
### The Decoupled Architecture
In a unified data lake, the metadata layer and the storage layer operate as distinct services. This decoupling allows for features like instant renaming and global namespaces but introduces the risk of "Ghosting." A Ghost File occurs when the "Source of Truth" for the namespace (the catalog) disagrees with the "Source of Truth" for the data (the storage).

### State Inconsistency
Ghosting typically manifests in two states:
1.  **Visible but Unreachable:** The file appears in directory listings (LS commands) but returns a "404 Not Found" or "Access Denied" when a Read operation is attempted.
2.  **Invisible but Occupying Space:** The file does not appear in listings, but storage quotas or underlying block analysis indicate the data still exists (Shadow Reference).

### Eventual Consistency vs. Strong Consistency
While many modern lakes strive for strong consistency, distributed systems often revert to eventual consistency during high-load scenarios or cross-regional replication. Ghost files are often a byproduct of the latency between an operation's completion in one layer and its propagation to the other.

## Standard Model
The standard model for managing file states in a unified lake follows the **Transactional Integrity Model**:

1.  **Pre-allocation:** The system reserves space or creates a "pending" metadata entry.
2.  **Commitment:** The storage layer confirms the write of the data blocks.
3.  **Finalization:** The metadata layer updates the status to "Available."
4.  **Cleanup:** Any temporary or previous versions are marked for asynchronous deletion.

A Ghost File represents a failure to move from step 2 to step 3, or a failure in the asynchronous cleanup process in step 4.

## Common Patterns
*   **The Deletion Lag:** A user deletes a large directory. The metadata layer marks it as deleted immediately, but the recursive deletion of physical objects takes time. During this window, the files may "ghost" back into view if the metadata cache is refreshed prematurely.
*   **The Failed Append:** A process attempts to append data to a file. The metadata updates the file size, but the write operation fails. The file now reports a size larger than the actual readable data.
*   **The Metadata Cache Hit:** A client queries a file that was recently deleted. The distributed cache returns the metadata (the ghost), but the storage request fails because the physical file is gone.

## Anti-Patterns
*   **Manual Metadata Manipulation:** Attempting to "fix" a ghost file by manually editing database entries or system catalogs without using the provided API, which often exacerbates the desynchronization.
*   **Polling for Existence:** Relying on repeated "File Exists" checks as a proxy for "File is Ready." This can trigger false positives if the metadata is written before the data is flushed.
*   **Ignoring Transaction Logs:** Failing to implement or monitor the system's internal transaction logs, which are the primary tool for reconciling ghosted states.

## Edge Cases
*   **Zero-Byte Ghosts:** A file that exists in both layers but contains no data due to a truncated write. While technically "present," it functions as a ghost for downstream analytical processes.
*   **Permission-Induced Ghosting:** A file that appears in a listing but is "missing" to a specific user because the metadata layer is visible, but the storage layer's security principal has not yet synchronized the ACLs.
*   **Concurrent Rename/Delete:** Two processes act on the same file simultaneously—one renames it, the other deletes it. The resulting state may leave a metadata entry for the new name pointing to a deleted physical location.

## Related Topics
*   **Distributed Systems Consistency (CAP Theorem)**
*   **Write-Ahead Logging (WAL)**
*   **Object Storage Versioning**
*   **Namespace Virtualization**

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |