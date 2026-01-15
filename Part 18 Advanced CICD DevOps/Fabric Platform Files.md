# Fabric Platform Files

Canonical documentation for Fabric Platform Files. This document defines concepts, terminology, and standard usage.

## Purpose
Fabric Platform Files represent the foundational storage abstraction within a unified data fabric architecture. The purpose of this layer is to provide a single, virtualized, and globally accessible namespace for all data assets, regardless of their physical location or underlying storage technology. 

By decoupling the logical file representation from physical storage hardware, the platform addresses the problem of data fragmentation, silos, and the overhead of data movement. It enables multiple compute engines (e.g., SQL, Spark, AI/ML) to operate on a single source of truth without requiring proprietary data formats or redundant copies.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Logical Storage Abstraction:** The virtualization of files and folders within a unified namespace.
* **Metadata Persistence:** How file-level metadata is maintained to support discovery and governance.
* **Multi-Protocol Access:** The theoretical ability to access the same file via different protocols (e.g., DFS, APIs, or legacy file system interfaces).
* **Data Interoperability:** The use of open-standard formats within the fabric.

**Out of scope:**
* **Specific Vendor Implementations:** Details regarding Microsoft Fabric OneLake, Amazon S3, or Google Cloud Storage specific APIs.
* **Hardware Specifications:** Physical disk types, IOPS, or networking hardware.
* **Compute Engine Logic:** The internal workings of the engines that process the files (e.g., Spark shuffle logic).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Fabric File** | A discrete unit of data stored within the fabric's unified namespace, typically immutable once written. |
| **Unified Namespace** | A single logical hierarchy that organizes files across different physical storage locations into a coherent structure. |
| **Shortcut / Virtual Link** | A metadata object that points to data stored in an external location, making it appear as if it is local to the fabric. |
| **Metadata Layer** | The system of record that tracks file locations, schemas, permissions, and lineage. |
| **Atomic Unit of Storage** | The smallest increment of data that can be managed or moved by the fabric (usually a file or a block). |
| **Schema-on-Read** | A data handling strategy where the structure of the file is applied during processing rather than enforced during ingestion. |

## Core Concepts

### 1. Unified Storage Substrate
The fabric treats all underlying storage as a single pool. This substrate abstracts the complexities of cloud-specific storage accounts, regions, and zones. Users interact with a logical path (e.g., `/Workspace/Folder/File.parquet`) rather than a physical URI.

### 2. Decoupling of Storage and Compute
Fabric Platform Files exist independently of the compute engines that process them. This allows storage to scale infinitely and persist even when compute resources are paused or terminated.

### 3. Open Data Standards
To ensure longevity and prevent vendor lock-in, the platform prioritizes open-source file formats (such as Parquet, Avro, or Delta) that support ACID properties and high-performance analytical queries.

### 4. Virtualization (Shortcutting)
A core concept where data is not moved or copied into the fabric. Instead, the fabric creates a "pointer" to external data. This allows the fabric to govern and analyze data that resides in legacy systems or disparate cloud providers as if it were native.

## Standard Model

The standard model for Fabric Platform Files follows a hierarchical organization designed for multi-tenancy and governance:

1.  **Tenant/Organization Level:** The root of the namespace.
2.  **Workspace/Domain Level:** A logical container for related projects, providing a boundary for security and administration.
3.  **Item/Artifact Level:** A collection of files representing a specific entity (e.g., a Lakehouse, a Warehouse, or a Dataset).
4.  **File/Folder Level:** The actual data stored in a structured or unstructured format.

In this model, **Metadata** is stored alongside the data (often in a `_metadata` or `_delta_log` folder) to ensure that the state of the files is self-describing and resilient.

## Common Patterns

### The Medallion Architecture
Files are organized into layers based on quality and readiness:
*   **Bronze (Raw):** Original, unmodified files.
*   **Silver (Validated):** Cleaned, filtered, and augmented files.
*   **Gold (Enriched):** Aggregated files ready for consumption by business users.

### Partitioning
Files are organized into sub-folders based on high-cardinality columns (e.g., `/Year=2024/Month=01/`) to optimize query performance by enabling "partition pruning."

### Compaction (Bin-Packing)
A pattern where many small files are periodically merged into larger, more efficient files to reduce metadata overhead and improve I/O performance.

## Anti-Patterns

*   **The Small File Problem:** Storing millions of tiny files (kilobytes in size), which degrades performance due to excessive metadata lookups and I/O overhead.
*   **Hard-Coding Physical Paths:** Referencing specific cloud storage URIs instead of using the fabric's logical namespace, which breaks portability.
*   **Lack of Schema Evolution Control:** Changing file structures without updating the associated metadata, leading to "broken" downstream consumers.
*   **Data Duplication for Security:** Creating physical copies of files to manage access control instead of using the fabric's native identity and access management (IAM) layer.

## Edge Cases

*   **Concurrent Writes:** When two different engines attempt to update the same file or folder simultaneously. The fabric must implement optimistic concurrency control (OCC) or distributed locking.
*   **Schema Drift:** When a source system changes the format of files unexpectedly. The fabric must decide whether to fail the ingestion or attempt to merge the new schema.
*   **Dangling Shortcuts:** When a virtual link points to a physical location that has been deleted or moved, resulting in a "404 Not Found" at the logical layer.
*   **Cross-Region Latency:** Accessing files via a shortcut located in a different geographical region, which may introduce significant performance penalties despite appearing "local."

## Related Topics

*   **Data Governance:** The framework for managing the security and quality of fabric files.
*   **ACID Transactions:** The mechanism (like Delta Lake) that ensures file integrity during multi-step operations.
*   **Compute Abstraction:** The layer that allows different engines to query the same files.
*   **Data Lineage:** The tracking of a file's history from origin to consumption.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial AI-generated canonical documentation |