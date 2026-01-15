# What Is The Data Map In Purview For Fabric Items

Canonical documentation for What Is The Data Map In Purview For Fabric Items. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

The Data Map in Purview for Fabric Items exists to address the complex challenge of managing and governing large volumes of data across various fabric items in a unified and consistent manner. The class of problems it addresses includes data discovery, data lineage, and data quality, which are critical for making informed decisions and ensuring compliance with regulatory requirements. When misunderstood or inconsistently applied, these concepts can lead to data silos, poor data quality, and inefficient data management, resulting in significant risks and failures. These risks include non-compliance, data breaches, and inefficient use of resources, ultimately affecting an organization's reputation and bottom line.

## 2. Conceptual Overview

The conceptual model of the Data Map in Purview for Fabric Items consists of three major components: Data Sources, Data Lineage, and Data Governance. 
- **Data Sources** refer to the various fabric items from which data is collected and integrated into the Purview.
- **Data Lineage** involves tracking the origin, movement, and transformation of data across the fabric items, providing a clear understanding of how data is generated, processed, and consumed.
- **Data Governance** encompasses the policies, procedures, and standards that ensure data quality, security, and compliance across all fabric items.

These components relate to one another in that data sources provide the raw data, data lineage tracks the data's lifecycle, and data governance ensures that the data is managed and used in a compliant and secure manner. The outcome of this model is to produce a unified, trustworthy, and accessible data map that supports informed decision-making and efficient data management across all fabric items.

## 3. Scope and Non-Goals

The scope of this documentation includes:
* **Conceptual Framework**: The theoretical underpinnings of the Data Map in Purview for Fabric Items.
* **Terminology and Definitions**: Standardized vocabulary used in the context of the Data Map.

Out of scope are:
* **Tool-specific Implementations**: Detailed guides on how to implement the Data Map using specific tools or technologies.
* **Vendor-specific Behavior**: Documentation on how different vendors' products interact with the Data Map.
* **Operational or Procedural Guidance**: Step-by-step instructions for daily operations or procedures related to the Data Map.

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

| Term | Definition |
|------|------------|
| Data Map | A visual representation of an organization's data assets and their relationships. |
| Fabric Items | Components or entities within a data fabric that contribute to the overall data landscape. |
| Data Lineage | The process of tracking data from its origin to its consumption, including any transformations or processing it undergoes. |
| Data Governance | The overall management of the availability, usability, integrity, and security of an organization's data. |

> [!TIP]
> Definitions are crafted to be timeless and applicable across various contexts, avoiding language that may become outdated or contextually bound.

## 5. Core Concepts

### 5.1 Data Sources
Data sources are the foundation of the Data Map, representing the various fabric items from which data is derived. Understanding and cataloging these sources is crucial for data discovery and integration.

### 5.2 Data Lineage
Data lineage is essential for tracking data movement and transformations, providing insights into data quality, security, and compliance. It involves capturing metadata about data processes, including inputs, outputs, and any data transformations.

### 5.3 Concept Interactions and Constraints
The interaction between data sources and data lineage is fundamental, as data lineage depends on accurate and comprehensive data sources. Constraints include ensuring data privacy, security, and compliance during data collection and processing.

## 6. Standard Model

### 6.1 Model Description
The standard model for the Data Map in Purview for Fabric Items involves a centralized repository that catalogues all data sources and tracks data lineage across fabric items. This model supports data discovery, data quality assessment, and data governance.

### 6.2 Assumptions
Assumptions under which the model is valid include:
- All data sources are identifiable and accessible.
- Data lineage can be accurately tracked and recorded.
- Governance policies are in place and enforceable.

### 6.3 Invariants
Invariants of the model include:
- Data integrity: Data remains consistent and accurate across all fabric items.
- Data security: Access to data is controlled and auditable.
- Compliance: Data management practices adhere to regulatory requirements.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified to ensure data integrity and compliance.

## 7. Common Patterns

### Pattern A: Centralized Data Governance
- **Intent**: Establish a unified governance framework across all fabric items.
- **Context**: When multiple fabric items contribute to the data landscape, and consistency is crucial.
- **Tradeoffs**: Centralized control may introduce bureaucracy, but it ensures compliance and data quality.

## 8. Anti-Patterns

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Data Silos
- **Description**: Isolating data within individual fabric items without integration or governance.
- **Failure Mode**: Leads to data redundancy, inconsistencies, and poor data quality.
- **Common Causes**: Lack of centralized governance, inadequate resources, or insufficient awareness of data management best practices.

## 9. Edge Cases and Boundary Conditions

Edge cases include scenarios where data sources are transient, data lineage is complex due to multiple transformations, or governance policies are in conflict. These cases require careful consideration to ensure the Data Map remains accurate and effective.

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

## 10. Related Topics

Related topics include data architecture, data quality management, and information security, as these areas are closely intertwined with the concepts of the Data Map in Purview for Fabric Items.

## 11. References

1. **Data Governance Institute**  
   Data Governance Institute  
   https://www.datagovernance.com/  
   *Provides foundational knowledge on data governance principles and practices.*

2. **Data Lineage Patterns**  
   IBM  
   https://www.ibm.com/docs/en/db2-for-zos/12?topic=design-data-lineage-patterns  
   *Offers insights into designing and implementing data lineage patterns.*

3. **Purview Documentation**  
   Microsoft  
   https://docs.microsoft.com/en-us/azure/purview/  
   *Official documentation for Azure Purview, covering its features and capabilities.*

4. **Data Management Body of Knowledge (DMBOK)**  
   Data Management Association (DAMA)  
   https://www.dama.org/content/dmbok  
   *A comprehensive guide to data management, including data governance and data quality.*

5. **Data Fabric Architecture**  
   Gartner  
   https://www.gartner.com/en/documents/3987441  
   *Analyzes the concept of data fabric and its role in modern data management architectures.*

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial documentation |

---

This comprehensive documentation provides a foundational understanding of the Data Map in Purview for Fabric Items, covering its purpose, conceptual model, terminology, and standard practices. It serves as a stable reference for professionals seeking to implement and manage data maps effectively within their organizations.