# What Is A Lakehouse In Fabric

Canonical documentation for What Is A Lakehouse In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
The Lakehouse in Fabric addresses the historical dichotomy between data lakes and data warehouses. Traditionally, organizations were forced to maintain two separate environments: a data lake for low-cost storage of raw, unstructured data and a data warehouse for high-performance, structured relational queries. This separation resulted in data silos, redundant ETL (Extract, Transform, Load) processes, and synchronization challenges.

The Lakehouse architecture exists to provide a unified data platform that combines the flexibility and scale of a data lake with the performance, ACID (Atomicity, Consistency, Isolation, Durability) compliance, and schema management of a data warehouse. It serves as a single source of truth where data engineers, data scientists, and data analysts can collaborate on the same physical data without moving or copying it.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the architectural patterns within the Fabric ecosystem.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The architectural definition of a Lakehouse within a unified SaaS data environment.
* The relationship between storage (OneLake) and table formats (Delta Lake).
* The interaction between different compute engines (Spark, SQL) on a shared data layer.
* Governance and security boundaries at the Lakehouse level.

**Out of scope:**
* Step-by-step UI tutorials or "How-to" guides.
* Detailed pricing and licensing models.
* Comparison with third-party, non-Fabric lakehouse implementations.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Lakehouse** | A data management architecture that provides a relational database layer (tables) on top of a file-based data lake. |
| **OneLake** | The unified, multi-tenant logical data lake that serves as the single storage backend for all Fabric items. |
| **Delta Lake** | An open-source storage layer that brings ACID transactions to Apache Spark and big data workloads. |
| **SQL Analytics Endpoint** | A read-only gateway that allows T-SQL queries to be executed against the Delta tables in a Lakehouse. |
| **V-Order** | A write-time optimization technique that applies sorting, distribution, and encoding to Parquet files to enhance read performance. |
| **Shortcut** | A virtualization capability that allows data to be referenced from external locations (S3, ADLS Gen2) without moving the underlying bits. |

## Core Concepts

### Unified Storage (OneLake)
The Lakehouse is built upon a "One Copy" philosophy. All data is stored in OneLake in an open format (Parquet/Delta). This eliminates the need to export data from a lake to a warehouse for reporting; the same data used by a Spark job for machine learning is immediately accessible via SQL for business intelligence.

### Decoupled Compute and Storage
The Lakehouse separates the storage of data from the engines used to process it. This allows multiple engines (Spark for engineering, SQL for analysis, Power BI for visualization) to operate on the same data simultaneously without resource contention at the storage layer.

### ACID Compliance on Files
By utilizing the Delta Lake format, the Lakehouse ensures that data operations are atomic and consistent. This prevents data corruption during concurrent writes and allows for "Time Travel" (querying previous versions of data).

### Schema Enforcement and Evolution
The Lakehouse supports schema enforcement to ensure data quality and schema evolution to allow the data structure to change over time without breaking downstream dependencies.

## Standard Model

The standard model for a Lakehouse in Fabric follows the **Medallion Architecture**, which organizes data into logical layers based on quality and structure:

1.  **Bronze (Raw):** The landing zone for source data in its original format. Data is typically stored as-is, preserving history and providing a recovery point.
2.  **Silver (Validated/Enriched):** Data is cleaned, filtered, and standardized. Joins and lookups are performed to create a consolidated view. This layer is typically stored in Delta format.
3.  **Gold (Curated/Refined):** Data is aggregated and modeled for specific business use cases (e.g., Star Schema). This layer is optimized for consumption by BI tools and executive reporting.

## Common Patterns

### The "Shortcut" Integration
Instead of traditional ETL, organizations use Shortcuts to link external cloud storage (like AWS S3 or Azure Data Lake Gen2) directly into the Lakehouse. This allows the Lakehouse to act as a centralized management layer for decentralized data.

### Automatic Table Discovery
When files are written to the "Tables" section of the Lakehouse in Delta format, the system automatically discovers them and exposes them as managed tables in the SQL Analytics Endpoint without manual DDL (Data Definition Language) intervention.

### Cross-Lakehouse Querying
Users often implement a "Hub and Spoke" pattern where a central Lakehouse contains master data, and departmental Lakehouses query that data via shortcuts or direct references to maintain a single version of truth.

## Anti-Patterns

*   **Data Siloing by Format:** Storing data in proprietary or non-Delta formats within the Lakehouse, which prevents the SQL Analytics Endpoint and Power BI from accessing it efficiently.
*   **Over-Partitioning:** Creating too many small partitions (e.g., partitioning by a high-cardinality ID), which leads to the "Small File Problem" and degrades query performance.
*   **Treating Lakehouse as a Legacy RDBMS:** Attempting to perform frequent, single-row updates or deletes (point lookups) rather than batch-oriented operations, which is inefficient in a file-based architecture.
*   **Manual Metadata Duplication:** Manually creating schemas in a separate Data Warehouse that point to Lakehouse files, rather than utilizing the automatic integration of the SQL Analytics Endpoint.

## Edge Cases

*   **Schema Drift in Auto-Discovery:** When source systems change schemas frequently, the automatic table discovery may fail or create conflicts if the Delta log becomes inconsistent with the expected schema.
*   **Large Metadata Overhead:** In Lakehouses with hundreds of thousands of small files, the metadata overhead for Delta Lake can impact "Time Travel" performance and log vacuuming.
*   **Concurrent Write Conflicts:** While Delta Lake supports ACID, high-concurrency writes to the same partition from different Spark clusters can lead to "ConcurrentAppendException" or "FileAlreadyExistsException" if not managed via optimistic concurrency control.

## Related Topics
*   **OneLake Architecture:** The underlying storage substrate for all Lakehouses.
*   **Data Warehouse in Fabric:** A complementary item type optimized for T-SQL intensive workloads and DML operations.
*   **Delta Lake Protocol:** The open-source standard that enables the Lakehouse functionality.
*   **Direct Lake Mode:** The Power BI connectivity technology that reads Lakehouse data directly without import or DirectQuery.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |