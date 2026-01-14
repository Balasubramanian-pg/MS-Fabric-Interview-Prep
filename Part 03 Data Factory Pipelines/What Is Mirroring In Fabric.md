# What Is Mirroring In Fabric

Canonical documentation for What Is Mirroring In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
Mirroring in a data fabric addresses the fundamental challenge of data fragmentation and the latency inherent in traditional Extract, Transform, Load (ETL) processes. Its primary purpose is to provide a continuous, low-latency, and low-friction method of surfacing data from disparate external sources into a unified fabric environment. 

By creating a synchronized reflection of source data, mirroring enables organizations to perform analytics, AI modeling, and reporting on live operational data without impacting the performance of the source systems or requiring complex data pipeline maintenance. It bridges the gap between operational databases and analytical platforms by automating the ingestion and conversion of data into a standardized, fabric-native format.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The architectural pattern of continuous data synchronization within a fabric.
* The theoretical mechanism of Change Data Capture (CDC) as applied to mirroring.
* The relationship between source systems and the fabric-native storage layer.
* Consistency models and synchronization boundaries.

**Out of scope:**
* Specific vendor-specific UI instructions or configuration steps.
* Hardware-level disk mirroring (RAID).
* Traditional batch-based ETL methodologies.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Mirroring** | The process of creating a near real-time, read-only replica of an external data source within the fabric's native storage. |
| **Source System** | The external operational database or application where the primary data resides and is modified. |
| **Fabric Storage** | The unified, often distributed, storage layer where the mirrored data is persisted (e.g., OneLake, Data Lakehouse). |
| **Change Data Capture (CDC)** | The technology used to identify and capture changes (inserts, updates, deletes) made to the source data so they can be applied to the mirror. |
| **Schema Evolution** | The ability of the mirroring process to automatically adapt to changes in the source data structure (e.g., adding a column). |
| **Initial Snapshot** | The first full synchronization of data from the source to the fabric before incremental updates begin. |

## Core Concepts

### Continuous Synchronization
Unlike batch processing, mirroring is designed for continuity. Once the initial snapshot is completed, the fabric maintains a persistent connection to the source system's transaction logs. Changes are streamed and applied to the fabric storage with minimal latency, ensuring the "mirror" reflects the current state of the "source."

### Zero-ETL Philosophy
Mirroring adheres to a "Zero-ETL" or "Low-Code" philosophy. The fabric handles the underlying complexities of data type mapping, connection management, and state tracking. This removes the need for data engineers to manually construct and maintain pipelines for every table or entity.

### Read-Only Decoupling
The mirrored data in the fabric is strictly read-only. This decoupling ensures that analytical workloads—which can be resource-intensive—do not compete for compute resources with the operational source system.

### Native Format Conversion
As data is mirrored, it is typically converted from the source's proprietary format into an open, performant standard (such as Parquet or Delta Lake) within the fabric. This allows the data to be immediately accessible by various compute engines (SQL, Spark, AI) without further transformation.

## Standard Model
The standard model for Mirroring in Fabric follows a three-stage pipeline:

1.  **Capture:** The fabric monitors the source system (usually via transaction logs or a specialized agent) to detect DML (Data Manipulation Language) and DDL (Data Definition Language) changes.
2.  **Ingest & Transform:** The captured changes are securely transmitted to the fabric. The fabric performs "on-the-fly" conversion of the data into the fabric's standardized storage format.
3.  **Persist & Catalog:** The data is written to the fabric's storage layer and automatically registered in the fabric's global metadata catalog, making it discoverable and queryable.

## Common Patterns

### Operational Reporting
Using the mirror as the primary source for real-time dashboards, ensuring that business intelligence reflects the most recent transactions without slowing down the production database.

### Cross-Source Joinery
Mirroring multiple disparate databases (e.g., a SQL database and a NoSQL database) into the same fabric to perform federated queries and joins that were previously impossible or highly latent.

### AI/ML Feature Store
Using mirrored data as a fresh stream of features for machine learning models, ensuring that model inference is based on the latest available state of the world.

## Anti-Patterns

### Mirroring as a Backup
Mirroring is a synchronization tool, not a point-in-time recovery solution. If data is accidentally deleted in the source, the deletion is mirrored to the fabric. It should not replace a robust backup and disaster recovery strategy.

### Bi-directional Sync
Attempting to write back to the source system through the mirror. Mirroring is architecturally defined as a one-way flow (Source → Fabric). Writing back introduces circular dependencies and data integrity risks.

### Heavy Transformation during Mirroring
Attempting to perform complex business logic or aggregations during the mirroring process. Mirroring should be a "faithful reflection." Complex transformations should occur in downstream layers of the fabric (e.g., via Medallion Architecture).

## Edge Cases

### Schema Drift
When a source system undergoes a significant structural change (e.g., dropping a column or changing a data type), the mirroring engine must decide whether to break, ignore, or adapt. Canonical mirroring should support non-breaking schema evolution.

### Network Partitions
In the event of a network failure between the source and the fabric, the mirroring process must maintain a "checkpoint." Once connectivity is restored, the engine must be able to resume from the exact point of failure without data loss or duplication.

### Large Binary Objects (LOBs)
Mirroring systems often struggle with extremely large blobs or clobs. Standard practice involves either capping the size of mirrored objects or storing references rather than the full binary content.

## Related Topics
* **Data Fabric Architecture:** The overarching framework in which mirroring resides.
* **Change Data Capture (CDC):** The underlying mechanism for identifying source changes.
* **Data Lakehouse:** The typical storage destination for mirrored data.
* **Virtualization vs. Mirroring:** The distinction between querying data in place (virtualization) versus replicating it for performance (mirroring).

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |