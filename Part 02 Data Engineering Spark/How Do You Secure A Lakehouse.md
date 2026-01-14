# How Do You Secure A Lakehouse

Canonical documentation for How Do You Secure A Lakehouse. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of securing a lakehouse is to protect data assets within an architecture that merges the low-cost, flexible storage of a data lake with the performance, structure, and ACID (Atomicity, Consistency, Isolation, Durability) guarantees of a data warehouse. 

Because a lakehouse decouples storage from compute and supports diverse workloads (AI, ML, BI, and streaming), it introduces a unique security surface area. Securing a lakehouse addresses the problem of maintaining a "single source of truth" for security policies across heterogeneous compute engines while ensuring data privacy, regulatory compliance, and protection against unauthorized access or exfiltration.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Architectural Layers:** Security at the storage, metadata (catalog), and compute layers.
* **Access Control Models:** Frameworks for managing permissions across structured and unstructured data.
* **Data Lifecycle Security:** Protection of data at rest, in transit, and during processing.
* **Governance Integration:** The relationship between security policies and data discovery/lineage.

**Out of scope:**
* **Specific vendor implementations:** (e.g., specific configurations for Databricks, Snowflake, AWS Lake Formation, or Microsoft Fabric).
* **General Network Security:** Standard firewalls or VPC setups, except where they uniquely intersect with lakehouse architecture.
* **Physical Security:** Security of the underlying data center hardware.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Lakehouse** | An architectural pattern that implements data warehouse-like structures and data management features on top of low-cost cloud storage. |
| **Decoupled Security** | A security model where permissions are defined independently of the compute engine used to access the data. |
| **Metadata Catalog** | A centralized repository that stores schemas and table definitions, often serving as the enforcement point for access control. |
| **Fine-Grained Access Control (FGAC)** | The ability to restrict access at the row, column, or cell level, rather than just at the file or table level. |
| **ACID Transactions** | Properties that ensure data integrity during concurrent writes, which is a prerequisite for reliable security auditing. |
| **Data Masking** | The process of obscuring specific data elements (e.g., PII) within a column while allowing the rest of the record to be accessed. |
| **Credential Passthrough** | A mechanism where the identity of the end-user is passed directly to the storage layer to validate access. |

## Core Concepts
The security of a lakehouse rests on four fundamental pillars:

1.  **Unified Governance:** Security policies must be defined once in a central catalog and enforced regardless of whether the data is accessed via SQL, Python, R, or Scala.
2.  **Defense in Depth:** Security is applied at multiple layers: the storage layer (IAM/Service Principals), the metadata layer (Table/View permissions), and the compute layer (Session isolation).
3.  **Identity-Centric Access:** Permissions are tied to a verified identity (User or Service) rather than network location or shared secrets.
4.  **Immutability and Versioning:** Leveraging the underlying table formats (e.g., Parquet with transaction logs) to provide an immutable audit trail of data changes.

## Standard Model
The generally accepted model for lakehouse security is the **Three-Layer Enforcement Model**:

### 1. The Storage Layer (Physical Security)
Data is stored as files (e.g., Parquet, Avro) in an object store. Security here involves:
*   **Encryption at Rest:** Using provider-managed or customer-managed keys.
*   **Storage-Level ACLs:** Restricting direct access to the underlying buckets to prevent users from bypassing the metadata layer.

### 2. The Metadata/Catalog Layer (Logical Security)
This is the "Brain" of the lakehouse. It maps physical files to logical tables.
*   **Object-Level Permissions:** Granting `SELECT`, `UPDATE`, or `DELETE` on tables and views.
*   **Schema Evolution Control:** Ensuring only authorized users can alter the structure of the data.

### 3. The Compute Layer (Session Security)
The engine (Spark, Presto, SQL Engine) that processes the data.
*   **Isolation:** Ensuring that one user's code cannot access another user's memory space or temporary files.
*   **Secure Connectivity:** Using TLS for data in transit between the storage and the compute clusters.

## Common Patterns
*   **Role-Based Access Control (RBAC):** Assigning permissions to roles, which are then assigned to users. This is the standard for organizational data management.
*   **Attribute-Based Access Control (ABAC):** Using tags (e.g., `Sensitivity=Public` or `Department=Finance`) to dynamically determine access at runtime.
*   **Dynamic Data Masking:** Redacting sensitive information based on the user's role without altering the underlying physical data.
*   **Discovery-Driven Security:** Automatically applying security tags based on automated PII (Personally Identifiable Information) scanning during the ingestion phase.

## Anti-Patterns
*   **The "God Mode" Service Account:** Using a single high-privilege identity for all compute tasks, making it impossible to audit individual user actions.
*   **Storage-Only Security:** Relying solely on folder-level permissions in an object store. This fails to provide row/column level security and breaks when data is moved.
*   **Manual Policy Synchronization:** Manually trying to keep permissions in sync across multiple different tools (e.g., a SQL engine and a Data Science notebook) instead of using a unified catalog.
*   **Hardcoded Credentials:** Embedding API keys or storage secrets within notebooks or application code.

## Edge Cases
*   **Cross-Platform Sharing:** When data must be shared with an external organization that does not use the same identity provider or lakehouse platform. This requires "Open Sharing" protocols that maintain security without credential exchange.
*   **Time-Travel Queries:** Accessing historical versions of data. Security must ensure that a user who has access to current data also has (or is explicitly denied) access to the data as it existed in the past.
*   **UDF (User Defined Function) Injection:** Malicious code embedded in a custom function that attempts to exfiltrate data or bypass logical access controls at the compute level.
*   **Unstructured Data:** Securing images, PDFs, or binary files within the lakehouse that do not fit into a standard table/schema format.

## Related Topics
*   **Data Privacy Frameworks (GDPR/CCPA):** The legal requirements that drive lakehouse security architecture.
*   **Data Lineage:** The ability to track the flow of data, which is essential for auditing and verifying security perimeters.
*   **Identity and Access Management (IAM):** The foundational system for managing user identities.
*   **Table Formats (Delta Lake, Iceberg, Hudi):** The technical specifications that enable ACID and metadata-level security on a lake.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |