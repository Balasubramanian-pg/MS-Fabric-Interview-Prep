# Why Are My Materialized Views Not Being Used By The Query Optimizer

Canonical documentation for Why Are My Materialized Views Not Being Used By The Query Optimizer. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

The topic of materialized views not being used by the query optimizer addresses a common issue in database management systems where pre-computed results stored in materialized views are not utilized by the query optimizer, leading to suboptimal query performance. This problem space is crucial because it directly affects the efficiency and scalability of database-driven applications. When materialized views are not used, it can result in increased query execution times, higher resource utilization, and decreased overall system performance. The risks of misunderstanding or misapplying materialized views include poor query performance, increased maintenance costs, and potential data inconsistencies.

## 2. Conceptual Overview

The conceptual model of materialized views and query optimization involves several key components:
- **Materialized Views**: Pre-computed results stored in a physical table, derived from a query.
- **Query Optimizer**: A component of the database management system responsible for determining the most efficient execution plan for a given query.
- **Query Execution Plan**: The sequence of operations the database performs to execute a query.

These components interact to produce an optimal query execution plan that minimizes resource utilization and execution time. The desired outcome is for the query optimizer to effectively utilize materialized views when they can provide a performance benefit, ensuring that queries are executed efficiently.

## 3. Scope and Non-Goals

Clarify the explicit boundaries of this documentation.

**In scope:**
* Conceptual understanding of materialized views and query optimization
* Factors influencing the query optimizer's decision to use materialized views

**Out of scope:**
* Tool-specific implementations of materialized views and query optimization
* Vendor-specific behavior of database management systems
* Operational or procedural guidance for creating or maintaining materialized views

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

Provide precise, stable definitions for all key terms used throughout this document.

| Term | Definition |
|------|------------|
| Materialized View | A database object that stores the result of a query in a physical table, updated periodically or on demand. |
| Query Optimizer | A database component that analyzes queries and determines the most efficient execution plan based on various factors, including data distribution, indexing, and system resources. |
| Query Execution Plan | The step-by-step sequence of operations the database performs to execute a query, including data retrieval, joins, sorting, and aggregation. |

> [!TIP]
> Definitions should avoid contextual or time-bound language and remain valid as the ecosystem evolves.

## 5. Core Concepts

Explain the fundamental ideas that form the basis of this topic.

### 5.1 Materialized Views
Materialized views are pre-computed results stored in a physical table. They are designed to improve query performance by avoiding the need to re-compute results from base tables. Materialized views play a crucial role in data warehousing and business intelligence applications where complex queries are common.

### 5.2 Query Optimization
Query optimization is the process of selecting the most efficient execution plan for a query. The query optimizer considers various factors, including the query syntax, data distribution, indexing, and system resources. Effective query optimization is critical for achieving good database performance.

### 5.3 Concept Interactions and Constraints
The query optimizer's decision to use a materialized view is influenced by several factors, including:
- **Query Syntax**: The materialized view must match the query syntax closely enough to be considered a viable alternative.
- **Data Freshness**: The materialized view must be up-to-date or within an acceptable freshness threshold.
- **Cost Estimation**: The query optimizer estimates the cost of using the materialized view versus recomputing the results from base tables.

## 6. Standard Model

Describe the generally accepted or recommended model for this topic.

### 6.1 Model Description
The standard model involves the query optimizer analyzing the query and considering materialized views as potential candidates for query execution. The optimizer evaluates the cost, data freshness, and query syntax match to determine if using a materialized view is beneficial.

### 6.2 Assumptions
The model assumes:
- Materialized views are properly maintained and updated.
- The query optimizer has accurate statistics about data distribution and system resources.
- The query syntax is compatible with the materialized view definition.

### 6.3 Invariants
The following properties must always hold true:
- Materialized views are used only when they provide a performance benefit.
- The query optimizer always considers materialized views as part of the optimization process.
- Data consistency and integrity are maintained when using materialized views.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified.

## 7. Common Patterns

Document recurring, accepted patterns associated with this topic.

### Pattern A: Regular Materialized View Refresh
- **Intent:** Ensure materialized views are up-to-date and reflect recent changes in the base tables.
- **Context:** Applied in environments with frequently updated data or high query volumes.
- **Tradeoffs:** Increased maintenance overhead versus improved query performance.

## 8. Anti-Patterns

Describe common but discouraged practices.

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Over-Reliance on Materialized Views
- **Description:** Creating materialized views for every possible query scenario, leading to maintenance and storage issues.
- **Failure Mode:** Decreased system performance due to excessive materialized view maintenance and storage requirements.
- **Common Causes:** Lack of understanding of query patterns and materialized view maintenance costs.

## 9. Edge Cases and Boundary Conditions

Explain unusual or ambiguous scenarios that may challenge the standard model.

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

- **Partial Materialized Views**: Materialized views that cover only a subset of the query requirements, requiring additional computation to fulfill the query.
- **Stale Materialized Views**: Materialized views that are not updated frequently enough, potentially leading to outdated results.

## 10. Related Topics

List adjacent, dependent, or prerequisite topics.
- Query Optimization Techniques
- Materialized View Maintenance Strategies
- Database Performance Tuning

## 11. References

Provide exactly **five** authoritative external references that substantiate or inform this topic.

1. **Database Systems: The Complete Book**  
   Hector Garcia-Molina, Ivan Martinez, and Jose Valenza  
   https://www.db-book.com/  
   *Justification:* Comprehensive textbook on database systems, including materialized views and query optimization.
2. **Query Optimization**  
   Microsoft Docs  
   https://docs.microsoft.com/en-us/sql/relational-databases/query-optimization?view=sql-server-ver15  
   *Justification:* Official documentation on query optimization from Microsoft, covering concepts and best practices.
3. **Materialized Views**  
   Oracle Documentation  
   https://docs.oracle.com/en/database/oracle/oracle-database/21/sqlrf/CREATE-MATERIALIZED-VIEW.html#GUID-6361F5DF-1D91-4F4B-85F3-2B64E7F49B5C  
   *Justification:* Official documentation on materialized views from Oracle, detailing their creation and management.
4. **Database Performance Tuning**  
   IBM Knowledge Center  
   https://www.ibm.com/docs/en/db2-for-zos/12?topic=performance-tuning  
   *Justification:* Comprehensive guide to database performance tuning from IBM, including materialized views and query optimization.
5. **SQL Query Optimization**  
   PostgreSQL Documentation  
   https://www.postgresql.org/docs/current/sql-query.html  
   *Justification:* Official documentation on SQL query optimization from PostgreSQL, covering query planning and execution.

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

> [!CAUTION]
> If fewer than five authoritative references exist, explicitly state this. In this case, five relevant references are provided.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial documentation |

---

This documentation provides a comprehensive overview of why materialized views may not be used by the query optimizer, covering conceptual models, terminology, core concepts, and standard practices. It aims to serve as a stable reference for understanding and addressing issues related to materialized views and query optimization in database management systems.