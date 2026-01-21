# [What Is A Fabric Item](Part 05 RealTime Science PowerBI/What Is A Fabric Item.md)

Canonical documentation for [What Is A Fabric Item](Part 05 RealTime Science PowerBI/What Is A Fabric Item.md). This document defines concepts, terminology, and standard usage.

## Purpose
The concept of a **Fabric Item** exists to provide a unified abstraction layer over heterogeneous data, compute, and configuration resources within a Data Fabric architecture. In modern distributed environments, data and logic are often fragmented across disparate storage systems, formats, and physical locations. 

The Fabric Item addresses the problem of architectural complexity by encapsulating raw resources into a standardized, manageable unit. This allows for consistent governance, discovery, and access control regardless of the underlying physical implementation or storage medium.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The conceptual definition of a Fabric Item as a unit of management.
* The relationship between metadata and physical data within an item.
* The lifecycle and behavioral expectations of items within a fabric.
* Standard properties (identity, security, and lineage).

**Out of scope:**
* Specific vendor implementations (e.g., Microsoft Fabric, SAP Data Fabric).
* Physical storage protocols (e.g., Parquet, Delta, HDFS) except as examples of underlying layers.
* Specific programming language SDKs.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Fabric Item** | The fundamental unit of logical organization within a data fabric, representing a specific resource (data, logic, or configuration). |
| **Abstraction Layer** | The conceptual boundary that separates the user's interaction with an item from the underlying physical storage or compute engine. |
| **Metadata Envelope** | The collection of attributes, schemas, and policies that describe and govern a Fabric Item. |
| **Pointer/Reference** | A mechanism within a Fabric Item that links the logical entity to its physical data location. |
| **Virtualization** | The process by which a Fabric Item represents data that remains in its source location without physical movement. |

## Core Concepts

### 1. Logical Encapsulation
A Fabric Item is not merely a file or a table; it is a logical container. It encapsulates the data itself (or a reference to it), the schema defining its structure, the security policies governing access, and the lineage tracking its provenance.

### 2. Unified Identity
Every Fabric Item must possess a unique, immutable identifier within the fabric's namespace. This identity allows the item to be referenced consistently by different services (e.g., analytics, machine learning, or reporting) without requiring knowledge of the item's physical path.

### 3. Metadata-First Architecture
In a fabric environment, the metadata is as significant as the data. A Fabric Item is "discovered" and "managed" via its metadata envelope. This allows for global searchability and automated governance before a single byte of data is actually read.

### 4. Location Transparency
The consumer of a Fabric Item should not need to know whether the data resides in an on-premises data center, a public cloud bucket, or a third-party SaaS application. The Fabric Item abstracts these details away.

## Standard Model
The standard model of a Fabric Item consists of four primary layers:

1.  **Identity Layer:** Contains the Unique ID (UUID), Name, Type (e.g., Table, Notebook, Dashboard), and Namespace/Workspace location.
2.  **Metadata Layer:** Contains the schema (fields, types), tags, descriptions, and custom properties.
3.  **Policy Layer:** Defines Access Control Lists (ACLs), data masking rules, and retention policies.
4.  **Data/Logic Layer:** The actual payload. This may be "Physical" (the item owns the data) or "Virtual" (the item points to external data).

## Common Patterns

### The Virtualized Item (Shortcut)
The item acts as a proxy for data stored in an external system. The fabric manages the metadata and security, but the data remains in the source system to avoid duplication.

### The Materialized Item
The fabric takes full ownership of the data. The data is ingested, transformed, and stored within the fabric's managed storage layer for optimized performance.

### The Composite Item
An item that is composed of other items. For example, a "Semantic Model" item may reference multiple "Table" items to create a unified view for business intelligence.

## Anti-Patterns

*   **The "Black Box" Item:** Creating items that contain data without defined schemas or metadata, making them undiscoverable and ungovernable.
*   **Hard-Coded Pathing:** Referencing the underlying physical storage (e.g., `s3://bucket/file.csv`) instead of the Fabric Item's logical identity.
*   **Monolithic Items:** Creating a single Fabric Item to represent an entire database or file system, which defeats the purpose of granular management and lineage.
*   **Bypassing the Fabric:** Accessing the underlying data of a Fabric Item directly through storage APIs, circumventing the security and governance layers defined at the fabric level.

## Edge Cases

*   **Orphaned Items:** A Fabric Item whose underlying physical data has been deleted or moved without updating the fabric's metadata.
*   **Circular Dependencies:** Composite items that reference each other in a loop, potentially causing failures in lineage tracking or data refresh processes.
*   **Transient Items:** Short-lived items created during a compute session (e.g., temporary tables) that may or may not require full registration in the fabric's global catalog.
*   **Cross-Region Latency:** When a Fabric Item's metadata is stored in one region but its physical data resides in another, leading to unexpected performance degradation.

## Related Topics
*   **Data Mesh:** A decentralized architectural pattern that often utilizes Fabric Items as "Data Products."
*   **Data Lineage:** The tracking of transformations and movements of Fabric Items over time.
*   **Unified Storage (OneLake/Data Lake):** The underlying storage philosophy often paired with Fabric Items.
*   **Metadata Harvesting:** The process of automatically creating Fabric Items from existing data sources.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |