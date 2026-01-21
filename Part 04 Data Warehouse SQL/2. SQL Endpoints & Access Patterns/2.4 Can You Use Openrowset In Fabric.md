# Can You Use Openrowset In Fabric

Canonical documentation for Can You Use Openrowset In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of this topic is to define the availability and application of the `OPENROWSET` function within a unified SaaS data platform environment. It addresses the requirement for ad-hoc data virtualization, allowing users to query data stored in a distributed file system (Data Lake) using standard relational query language (T-SQL) without the need for prior data ingestion or formal schema definition.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the behavior of the SQL engines within the Fabric ecosystem.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The availability of `OPENROWSET` within the SQL Analytics Endpoint and Synapse Data Warehouse.
* Supported file formats and data sources accessible via the function.
* The relationship between the SQL engine and the underlying distributed storage (OneLake).
* Security and authentication mechanisms governing ad-hoc access.

**Out of scope:**
* Legacy OLE DB or ODBC providers used in on-premises SQL Server implementations.
* Non-SQL based data access methods (e.g., Spark DataFrames, KQL).
* Third-party external data connectors not natively integrated into the platform's storage layer.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **OPENROWSET** | A T-SQL table-valued function that enables access to data from an external data source or file-based storage. |
| **OneLake** | The unified, logical data lake that serves as the single storage layer for all items in the platform. |
| **SQL Analytics Endpoint** | A read-only T-SQL interface automatically generated for a Lakehouse, allowing for SQL-based querying of Delta tables. |
| **Synapse Data Warehouse** | A fully managed data warehouse that supports T-SQL DDL and DML operations. |
| **Schema-on-Read** | A data analysis strategy where the schema is applied to the data only when it is read, rather than when it is stored. |
| **Delta Lake** | An open-source storage layer that brings ACID transactions to Apache Spark and big data workloads. |

## Core Concepts
The use of `OPENROWSET` in this environment is built upon the principle of **Compute and Storage Decoupling**. 

1. **Ad-hoc Virtualization:** `OPENROWSET` acts as a bridge between the relational engine and the file system. It allows the engine to treat a file path (Parquet, CSV, or Delta) as a virtual table.
2. **T-SQL Surface Area:** The platform provides a modern T-SQL surface area that prioritizes cloud-native formats. Unlike traditional SQL Server, which used `OPENROWSET` for bulk inserts or linked servers, the modern implementation focuses on high-performance analytical file formats.
3. **Unified Security:** Access via `OPENROWSET` respects the overarching workspace and item-level permissions. The identity of the user executing the query is used to authorize access to the underlying files in storage.

## Standard Model
The standard model for using `OPENROWSET` involves three primary components:

1. **The Data Source:** A URI pointing to a location within the distributed storage (e.g., an ABFS or HTTPS path to a folder in OneLake).
2. **The Format Specification:** Defining the structure of the target data, such as `PARQUET`, `DELTA`, or `CSV`.
3. **The Schema Declaration:** Optionally defining the column names and data types (the `WITH` clause) to ensure the SQL engine interprets the file contents correctly.

In the SQL Analytics Endpoint, `OPENROWSET` is frequently used by the system itself to expose Delta tables, but it remains available for manual user queries against files in the "Files" section of a Lakehouse.

## Common Patterns
* **Data Exploration:** Analysts use `OPENROWSET` to "peek" into raw data files (CSV or Parquet) before deciding whether to formalize them into managed tables.
* **External Table Creation:** Using `OPENROWSET` as the foundation for views or external tables to provide a permanent relational interface over a folder of files.
* **Cross-Workspace Querying:** Accessing data residing in different workspaces by referencing the full URI of the target storage location, provided the user has the necessary permissions.
* **Bulk Ingestion:** Using `INSERT INTO...SELECT FROM OPENROWSET` to move data from raw files into managed, indexed warehouse tables.

## Anti-Patterns
* **Production Reporting on Raw CSVs:** Using `OPENROWSET` to query large volumes of uncompressed CSV files for production dashboards. This leads to poor performance compared to using Delta or Parquet formats.
* **Hardcoding Credentials:** Attempting to pass explicit credentials within the query string. The standard model relies on integrated identity-based access.
* **Over-complex Schema Mapping:** Defining excessively complex `WITH` clauses for files that are already self-describing (like Parquet or Delta), which increases maintenance overhead.

## Edge Cases
* **Nested Folders:** When querying a directory, `OPENROWSET` can aggregate data from multiple files. However, if the schema differs between files in the same folder, the query may fail or return inconsistent results.
* **Hidden Files:** Files prefixed with underscores (e.g., `_metadata`) are typically ignored by the engine during a wildcard search.
* **Schema Evolution:** If a Delta table has undergone schema evolution (e.g., column renaming), `OPENROWSET` must be used with the `DELTA` provider to correctly interpret the transaction log, rather than reading the Parquet files directly.
* **Large File Counts:** Querying a folder containing thousands of small files via `OPENROWSET` can lead to significant latency due to the overhead of file metadata discovery.

## Related Topics
* **T-SQL Surface Area in Fabric:** The broader set of SQL commands supported.
* **OneLake Shortcuts:** A method to reference data without moving it, which complements `OPENROWSET` usage.
* **Delta Lake Protocol:** The underlying storage format that `OPENROWSET` is optimized to read.
* **Lakehouse vs. Warehouse:** Understanding which engine to use for specific `OPENROWSET` scenarios.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |