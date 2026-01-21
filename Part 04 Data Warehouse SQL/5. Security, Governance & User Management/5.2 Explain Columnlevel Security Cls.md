# Explain Column-level Security (CLS)

Canonical documentation for Column-level Security (CLS). This document defines concepts, terminology, and standard usage.

## Purpose
Column-level Security (CLS) exists to provide granular access control over sensitive data within a structured dataset. In modern data environments, a single table often contains a mix of non-sensitive operational data and highly sensitive information (e.g., PII, financial records, or trade secrets). 

CLS addresses the limitation of table-level security, which operates on an "all-or-nothing" access model. By decoupling access rights from the table entity and applying them to individual attributes (columns), organizations can enforce the Principle of Least Privilege (PoLP) without duplicating data or creating an unmanageable number of database views.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Logical frameworks for restricting access to specific data attributes.
* Interaction between security policies and query execution.
* Impact on data governance and compliance.
* Theoretical models of attribute-based and role-based column filtering.

**Out of scope:**
* Specific syntax for SQL Server, Snowflake, BigQuery, or other vendors.
* Row-level security (RLS), except where it intersects with CLS.
* Physical data encryption at rest (TDE).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Principal** | The entity (user, service account, or group) requesting access to the data. |
| **Attribute** | The specific column or field within a dataset that is subject to security constraints. |
| **Policy** | A set of rules that determines whether a Principal can view the data within a specific Attribute. |
| **Masking** | A technique often used alongside CLS to obscure data (e.g., showing only the last four digits of a SSN) rather than blocking access entirely. |
| **Metadata** | Information about the data that the security engine uses to apply CLS policies. |
| **Schema Binding** | The relationship between the security policy and the underlying data structure. |

## Core Concepts
The fundamental ideas of CLS revolve around the separation of concerns between data storage and data visibility.

### 1. Granularity of Control
CLS shifts the security boundary from the object level (the table) to the element level (the column). This allows for a "Single Source of Truth" where one table serves multiple audiences with different visibility requirements.

### 2. Policy-Driven Access
Access is not determined by the physical location of the data, but by a policy engine that evaluates the context of the request. This context typically includes the Principal’s identity, their assigned roles, and sometimes environmental factors (e.g., IP address or time of day).

### 3. Transparent Enforcement
In an ideal CLS implementation, the enforcement is transparent to the user. If a user lacks permission for a column, the system should either return a null value, a masked value, or an error, depending on the defined security posture, without requiring the user to modify their query logic.

## Standard Model
The standard model for CLS follows a request-response interception pattern:

1.  **Request:** A Principal submits a query (e.g., `SELECT * FROM Employees`).
2.  **Interception:** The query engine identifies the columns requested.
3.  **Policy Evaluation:** The engine checks the security metadata associated with the requested columns against the Principal’s permissions.
4.  **Transformation:**
    *   **Authorized:** The column data is retrieved normally.
    *   **Unauthorized:** The engine applies a transformation (Redaction, Nullification, or Exception).
5.  **Result Set:** The filtered or transformed data is returned to the Principal.

## Common Patterns
*   **Role-Based CLS (RBAC):** Access is granted based on the user's functional role (e.g., "HR_Manager" can see the `Salary` column, but "Staff_Analyst" cannot).
*   **Attribute-Based CLS (ABAC):** Access is granted based on attributes of the user and the data (e.g., "Users in the UK region can see columns tagged as 'GDPR_Public'").
*   **Dynamic Data Masking (DDM):** A subset of CLS where the column is visible, but the data is partially obscured based on the user's clearance level.
*   **Tag-Based Security:** Security policies are applied to "tags" (e.g., `PII`, `PCI`) rather than specific column names, allowing for scalable policy management across thousands of tables.

## Anti-Patterns
*   **Security by View:** Creating a separate database view for every possible combination of column permissions. This leads to "view explosion" and significant maintenance overhead.
*   **Application-Level Filtering:** Fetching all data to the application layer and filtering columns in the code. This violates security principles by exposing sensitive data to the application's memory and network transit.
*   **Hardcoding Policies:** Embedding security logic directly into stored procedures or queries rather than using a centralized policy engine.
*   **Over-reliance on Nulls:** Using `NULL` to represent unauthorized access without distinguishing it from actual `NULL` data, leading to data integrity confusion in reporting.

## Edge Cases
*   **Joins on Restricted Columns:** If a user is barred from seeing a `Social_Security_Number` column, should they be allowed to perform a `JOIN` on that column? Most authoritative models block the join or return an empty set to prevent "inference attacks."
*   **Aggregations (SUM/AVG):** Can a user calculate the `AVG(Salary)` if they are not permitted to see individual `Salary` values? Standard CLS usually blocks the aggregation to prevent data leakage through statistical inference.
*   **SELECT * Operations:** In many systems, `SELECT *` will fail if the user lacks access to even one column, or it will silently omit the restricted columns. The behavior must be explicitly defined to avoid breaking legacy applications.
*   **Schema Changes:** If a column is renamed or its data type changes, the CLS policy must be robust enough to persist or fail-closed.

## Related Topics
*   **Row-level Security (RLS):** Restricting which rows a user can see.
*   **Data Sovereignty:** Legal requirements regarding where data is stored and who can access it.
*   **Principle of Least Privilege (PoLP):** The foundational security philosophy for CLS.
*   **Data Masking:** The technical process of obscuring data.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |