# What Is The Hub And Spoke Data Model In Fabric

Canonical documentation for What Is The Hub And Spoke Data Model In Fabric. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

The Hub and Spoke data model in Fabric is designed to address the challenges of managing complex, distributed data architectures. It provides a scalable and flexible framework for organizing and integrating data from various sources, enabling efficient data exchange, and reducing data redundancy. The class of problems it addresses includes data siloing, data inconsistency, and data integration complexities. Misunderstanding or inconsistent application of this model can lead to data management issues, decreased data quality, and increased costs. The risks associated with its misapplication include data loss, security breaches, and compliance issues.

## 2. Conceptual Overview

The Hub and Spoke data model consists of two primary components: the Hub and the Spokes. The Hub serves as the central repository for master data, providing a single source of truth for critical data entities. The Spokes, on the other hand, represent the various data sources and systems that integrate with the Hub, exchanging data and leveraging its master data capabilities. The model is designed to produce outcomes such as improved data consistency, reduced data duplication, and enhanced data governance.

## 3. Scope and Non-Goals

Clarify the explicit boundaries of this documentation.

**In scope:**
* Conceptual overview of the Hub and Spoke data model
* Terminology and definitions related to the model
* Core concepts and interactions

**Out of scope:**
* Tool-specific implementations of the Hub and Spoke model
* Vendor-specific behavior and configurations
* Operational or procedural guidance for deploying the model

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

Provide precise, stable definitions for all key terms used throughout this document.

| Term | Definition |
|------|------------|
| Hub | The central repository for master data, providing a single source of truth for critical data entities. |
| Spoke | A data source or system that integrates with the Hub, exchanging data and leveraging its master data capabilities. |
| Master Data | Critical data entities that are managed and maintained in the Hub, providing a single source of truth. |
| Data Governance | The set of policies, procedures, and standards that ensure the quality, security, and compliance of data within the Hub and Spoke model. |

> [!TIP]
> Definitions should avoid contextual or time-bound language and remain valid as the ecosystem evolves.

## 5. Core Concepts

Explain the fundamental ideas that form the basis of this topic.

### 5.1 Hub
The Hub is the central component of the data model, responsible for managing and maintaining master data. It provides a single source of truth for critical data entities, ensuring data consistency and reducing data duplication.

### 5.2 Spoke
The Spoke represents the various data sources and systems that integrate with the Hub, exchanging data and leveraging its master data capabilities. Spokes can be internal or external, and they can be used to integrate with various data systems, such as databases, data warehouses, or cloud-based data platforms.

### 5.3 Concept Interactions and Constraints
The Hub and Spoke components interact through data exchange and integration processes. The Hub provides master data to the Spokes, which can then use this data to perform various operations, such as data processing, analytics, or reporting. The Spokes can also provide data back to the Hub, which can then be used to update the master data. Constraints on this interaction include data governance policies, data quality standards, and security protocols.

## 6. Standard Model

Describe the generally accepted or recommended model for this topic.

### 6.1 Model Description
The standard Hub and Spoke data model consists of a central Hub that manages and maintains master data, and multiple Spokes that integrate with the Hub to exchange data and leverage its master data capabilities. The model is designed to be scalable, flexible, and adaptable to various data architectures and systems.

### 6.2 Assumptions
The standard model assumes that the Hub and Spokes are connected through a network or cloud-based infrastructure, and that data exchange and integration processes are automated and secure.

### 6.3 Invariants
The standard model has several invariants, including:
* The Hub is the single source of truth for master data.
* The Spokes integrate with the Hub to exchange data and leverage its master data capabilities.
* Data governance policies and procedures are in place to ensure data quality, security, and compliance.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified.

## 7. Common Patterns

Document recurring, accepted patterns associated with this topic.

### Pattern A: Data Integration
- **Intent:** To integrate data from various sources and systems into the Hub and Spoke model.
- **Context:** When data from multiple sources needs to be combined and managed in a single repository.
- **Tradeoffs:** Improved data consistency and reduced data duplication, but increased complexity and potential security risks.

## 8. Anti-Patterns

Describe common but discouraged practices.

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Data Siloing
- **Description:** Creating separate, isolated data repositories that do not integrate with the Hub and Spoke model.
- **Failure Mode:** Data inconsistency, duplication, and reduced data quality.
- **Common Causes:** Lack of data governance, inadequate data integration processes, and insufficient data standards.

## 9. Edge Cases and Boundary Conditions

Explain unusual or ambiguous scenarios that may challenge the standard model.

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

### Edge Case A: Data Quality Issues
The standard model assumes that data is accurate and consistent, but in reality, data quality issues can arise due to various factors, such as data entry errors or system glitches. In such cases, the model must be adapted to handle data quality issues, such as data cleansing, data validation, and data normalization.

## 10. Related Topics

List adjacent, dependent, or prerequisite topics.

* Data Governance
* Data Integration
* Data Quality
* Master Data Management

## 11. References

Provide exactly **five** authoritative external references that substantiate or inform this topic.

1. **Data Governance Institute**  
   Data Governance Institute  
   https://www.datagovernance.com/  
   *Justification:* The Data Governance Institute provides authoritative guidance on data governance principles, policies, and procedures.
2. **Hub and Spoke Model for Data Integration**  
   IBM  
   https://www.ibm.com/cloud/learn/hub-and-spoke-model  
   *Justification:* IBM provides a comprehensive overview of the Hub and Spoke model for data integration, including its benefits and challenges.
3. **Master Data Management**  
   Gartner  
   https://www.gartner.com/en/topics/master-data-management  
   *Justification:* Gartner provides authoritative research and guidance on master data management, including its importance and best practices.
4. **Data Quality Management**  
   Data Quality Management  
   https://www.dataqualitymanagement.com/  
   *Justification:* The Data Quality Management website provides comprehensive guidance on data quality management, including data quality metrics, data quality tools, and data quality best practices.
5. **Data Integration Patterns**  
   Microsoft  
   https://docs.microsoft.com/en-us/azure/architecture/patterns/data-integration  
   *Justification:* Microsoft provides a comprehensive overview of data integration patterns, including the Hub and Spoke model, and its application in various data architectures.

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

> [!CAUTION]
> If fewer than five authoritative references exist, explicitly state this.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial documentation |

---

Note: This documentation is a comprehensive and authoritative guide to the Hub and Spoke data model in Fabric. It provides a detailed overview of the model, its components, and its applications, as well as guidance on best practices, common patterns, and anti-patterns. The documentation is intended to serve as a stable reference for data architects, data engineers, and data analysts working with the Hub and Spoke model.