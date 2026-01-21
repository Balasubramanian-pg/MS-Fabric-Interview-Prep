# What Is A Cross-database Query In Fabric

Canonical documentation for What Is A Cross-database Query In Fabric. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of a cross-database query in a unified data platform like Fabric is to enable seamless data integration and analysis across disparate logical data containers without the need for physical data movement or complex Extract, Transform, Load (ETL) processes. 

In modern data architectures, data is often partitioned into different databases or warehouses based on business domains, security requirements, or organizational boundaries. Cross-database querying addresses the need for a "single pane of glass" view, allowing users to join, union, and aggregate data residing in different SQL Analytics endpoints, Warehouses, or Lakehouses as if they were part of a single, unified system.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the architectural behavior of cross-database operations within the Fabric ecosystem.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The logical mechanism of querying across multiple data stores (Warehouses and Lakehouses).
* The role of the unified storage layer (OneLake) in facilitating these queries.
* Security and permission boundaries in multi-database environments.
* The conceptual framework of the three-part naming convention.

**Out of scope:**
* Specific T-SQL syntax tutorials or Spark code snippets.
* Performance tuning for specific hardware configurations.
* Third-party virtualization tools outside the native Fabric environment.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **OneLake** | The hierarchical, unified data lake that serves as the single source of truth for all data items in the environment. |
| **SQL Analytics Endpoint** | A read-only interface provided for Lakehouses that allows T-SQL querying of Delta tables. |
| **Warehouse** | A traditional data warehousing item that supports full DDL/DML operations and stores data in proprietary or open formats (Delta). |
| **Three-Part Naming** | The addressing convention (`DatabaseName.SchemaName.TableName`) used to reference objects across different databases. |
| **Cross-database Query** | A single execution command (SQL or Spark) that references objects residing in more than one database or lakehouse. |
| **Workspace** | A container for items (Warehouses, Lakehouses, Reports) that defines a primary security and administrative boundary. |

## Core Concepts

### Logical Abstraction
Cross-database querying relies on a logical abstraction layer. While data may be physically stored in different folders or regions within OneLake, the query engine treats these locations as distinct databases within a unified namespace. This allows for the decoupling of physical storage from logical data modeling.

### Unified Security Context
A fundamental concept is the propagation of the user's identity. When a cross-database query is executed, the system evaluates the user's permissions across all referenced items. If a user has access to "Database A" but not "Database B," a query attempting to join tables from both will fail, ensuring that data boundaries are respected despite the ease of connectivity.

### Metadata Discovery
For cross-database queries to function, the environment maintains a shared metadata catalog. This catalog allows the query optimizer to resolve object names, retrieve schemas, and estimate statistics for tables residing outside the current database context.

## Standard Model
The standard model for cross-database querying in Fabric follows a **Hub-and-Spoke** or **Data Mesh** architecture:

1.  **The Storage Layer:** All data resides in OneLake in Delta Lake format.
2.  **The Compute Layer:** Distributed query engines (SQL or Spark) access the data.
3.  **The Reference Model:** Users utilize the three-part naming convention (`[Database].[Schema].[Table]`) to bridge gaps between different business domains.
4.  **The Execution:** The query engine performs a "distributed join," pulling data from the respective locations into a shared memory space for processing, then returning a unified result set.

## Common Patterns

*   **Reference Data Joins:** Keeping "Gold" master data (e.g., Customers, Geography) in a central Warehouse while joining it against transactional data in localized Lakehouses.
*   **Staging to Production:** Querying across a "Staging" database and a "Production" database to perform data validation or incremental updates.
*   **Cross-Domain Analytics:** Combining Sales data from one workspace with Supply Chain data from another to gain end-to-end visibility.
*   **Virtual Data Marts:** Creating views in a central database that reference tables in multiple disparate databases, effectively creating a "virtual" mart without duplicating data.

## Anti-Patterns

*   **Circular Dependencies:** Creating views in Database A that reference Database B, while Database B contains views referencing Database A. This leads to maintenance complexity and potential resolution errors.
*   **Excessive Cross-Workspace Joins:** While supported, frequently joining massive datasets across different workspaces (especially if they reside in different physical regions) can introduce latency and egress costs.
*   **Over-reliance on Cross-database Views:** Building deep stacks of nested views across multiple databases can obscure the data lineage and make performance troubleshooting difficult.
*   **Ignoring Schema Drift:** Relying on cross-database queries without a schema management strategy; changes in the source database schema can break downstream queries in other databases.

## Edge Cases

*   **Collation Mismatches:** Querying across databases with different collation settings (e.g., Case-Sensitive vs. Case-Insensitive) can lead to unexpected join failures or incorrect results.
*   **Object Name Collisions:** In environments with many databases, identical schema and table names are common. Failure to use the full three-part name can lead to referencing the wrong object.
*   **Cross-Region Latency:** If databases are located in different geographical regions within the same tenant, cross-database queries may experience significant performance degradation due to data transfer times.
*   **Permission Inheritance:** Scenarios where a user has "Read" access to a view in Database A, but lacks underlying "Read" access to the tables in Database B that the view references.

## Related Topics
*   **OneLake Shortcuts:** A mechanism to reference data without moving it, often used in conjunction with cross-database queries.
*   **Delta Lake Format:** The underlying storage standard that enables interoperability between different engines.
*   **Workspace Permissions:** The foundational security layer governing access to databases.
*   **T-SQL Surface Area:** The specific subset of SQL commands supported for cross-database operations.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |