# When Should You Choose A Lakehouse Over A Warehouse

Canonical documentation for When Should You Choose A Lakehouse Over A Warehouse. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

The topic of choosing between a lakehouse and a warehouse exists to address the challenges of data management and analytics in modern data-driven organizations. The class of problems it addresses includes data siloing, scalability issues, and the need for real-time insights. The risks or failures that arise when this topic is misunderstood or inconsistently applied include inefficient data processing, poor data quality, and inadequate decision-making. This section is descriptive, not instructional, and aims to provide a comprehensive understanding of the problem space.

## 2. Conceptual Overview

The conceptual model of choosing between a lakehouse and a warehouse consists of three major components: data ingestion, data processing, and data analytics. These components relate to one another in a pipeline fashion, where data is ingested from various sources, processed and transformed, and then analyzed to produce insights. The outcomes of this model are designed to produce fast, scalable, and cost-effective data analytics capabilities.

## 3. Scope and Non-Goals

The scope of this documentation includes:

**In scope:**
* Data lakehouse architecture
* Data warehouse architecture
* Comparison of lakehouse and warehouse architectures

**Out of scope:**
* Tool-specific implementations (e.g., Apache Spark, Amazon Redshift)
* Vendor-specific behavior (e.g., AWS, GCP, Azure)
* Operational or procedural guidance (e.g., data governance, data quality)

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

The following terms are defined for the purpose of this documentation:

| Term | Definition |
|------|------------|
| Data Lakehouse | A centralized repository that stores raw, unprocessed data in its native format, allowing for flexible and scalable data processing and analytics. |
| Data Warehouse | A centralized repository that stores processed and transformed data in a structured format, optimized for querying and analysis. |
| Data Ingestion | The process of collecting and transporting data from various sources into a lakehouse or warehouse. |
| Data Processing | The process of transforming and preparing data for analysis, including data cleaning, data transformation, and data aggregation. |
| Data Analytics | The process of analyzing and interpreting data to produce insights and inform decision-making. |

> [!TIP]
> Definitions should avoid contextual or time-bound language and remain valid as the ecosystem evolves.

## 5. Core Concepts

The fundamental ideas that form the basis of this topic are:

### 5.1 Data Lakehouse
A data lakehouse is a centralized repository that stores raw, unprocessed data in its native format. It is designed to provide flexible and scalable data processing and analytics capabilities.

### 5.2 Data Warehouse
A data warehouse is a centralized repository that stores processed and transformed data in a structured format. It is optimized for querying and analysis, providing fast and efficient access to data.

### 5.3 Concept Interactions and Constraints
The data lakehouse and data warehouse concepts interact in a complementary fashion, where the lakehouse provides a flexible and scalable foundation for data processing and analytics, and the warehouse provides a structured and optimized repository for querying and analysis. The constraints of this interaction include data consistency, data quality, and data governance.

## 6. Standard Model

The standard model for choosing between a lakehouse and a warehouse is based on the following assumptions:

### 6.1 Model Description
The model consists of a data lakehouse that stores raw, unprocessed data, and a data warehouse that stores processed and transformed data. The data lakehouse provides a flexible and scalable foundation for data processing and analytics, while the data warehouse provides a structured and optimized repository for querying and analysis.

### 6.2 Assumptions
The assumptions under which this model is valid include:

* The organization has a large and diverse set of data sources.
* The organization requires flexible and scalable data processing and analytics capabilities.
* The organization requires fast and efficient access to data for querying and analysis.

### 6.3 Invariants
The properties that must always hold true within this model include:

* Data consistency: Data must be consistent across the lakehouse and warehouse.
* Data quality: Data must be accurate, complete, and reliable.
* Data governance: Data must be governed and managed according to organizational policies and procedures.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified.

## 7. Common Patterns

The following patterns are commonly associated with choosing between a lakehouse and a warehouse:

### Pattern A: Lakehouse-First Approach
- **Intent:** To provide a flexible and scalable foundation for data processing and analytics.
- **Context:** When the organization has a large and diverse set of data sources, and requires flexible and scalable data processing and analytics capabilities.
- **Tradeoffs:** The lakehouse-first approach provides flexibility and scalability, but may require additional processing and transformation steps to prepare data for analysis.

## 8. Anti-Patterns

The following anti-patterns are commonly associated with choosing between a lakehouse and a warehouse:

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Warehouse-Only Approach
- **Description:** Using a data warehouse as the sole repository for data, without a lakehouse or other flexible and scalable data processing and analytics capabilities.
- **Failure Mode:** The warehouse-only approach can lead to data siloing, scalability issues, and inadequate decision-making.
- **Common Causes:** The warehouse-only approach is often caused by a lack of understanding of the benefits of a lakehouse, or a lack of resources to implement a lakehouse.

## 9. Edge Cases and Boundary Conditions

The following edge cases and boundary conditions may challenge the standard model:

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

* Handling real-time data streams
* Integrating with external data sources
* Managing data quality and governance

## 10. Related Topics

The following topics are related to choosing between a lakehouse and a warehouse:

* Data governance
* Data quality
* Data processing and analytics
* Cloud computing and storage

## 11. References

The following authoritative external references substantiate or inform this topic:

1. **Big Data: The Missing Manual**  
   Tim O'Reilly  
   https://www.oreilly.com/library/view/big-data-the/9781449327175/  
   *Justification:* This book provides a comprehensive introduction to big data, including data lakehouses and warehouses.
2. **Data Warehouse Design: The Definitive Guide**  
   Ralph Kimball  
   https://www.kimballgroup.com/data-warehouse-design/  
   *Justification:* This guide provides a detailed overview of data warehouse design, including best practices and common pitfalls.
3. **Data Lake Architecture: Designing Hadoop and Object Stores for Data Lakes**  
   Nitin Gupta  
   https://www.packtpub.com/product/data-lake-architecture/9781787287391  
   *Justification:* This book provides a comprehensive guide to designing data lake architectures, including best practices and common pitfalls.
4. **Cloud Data Warehousing for Dummies**  
   Amazon Web Services  
   https://www.amazon.com/Cloud-Data-Warehousing-Dummies-Amazon/s?k=cloud+data+warehousing+for+dummies  
   *Justification:* This book provides a comprehensive introduction to cloud data warehousing, including best practices and common pitfalls.
5. **Data Management: A Guide to Data Management Best Practices**  
   Data Management Association  
   https://www.dma.org/data-management-a-guide-to-data-management-best-practices/  
   *Justification:* This guide provides a comprehensive overview of data management best practices, including data governance, data quality, and data processing and analytics.

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial documentation |

---