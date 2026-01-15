# How Do You Move From Azure Data Factory To Fabric Data Factory

Canonical documentation for How Do You Move From Azure Data Factory To Fabric Data Factory. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

The migration from Azure Data Factory (ADF) to Fabric Data Factory (FDF) is a complex process that involves transferring data pipelines, workflows, and dependencies between two distinct data integration platforms. This topic exists to address the class of problems related to data migration, integration, and workflow orchestration. The risks or failures that arise when this process is misunderstood or inconsistently applied include data loss, workflow disruptions, and increased costs. The purpose of this documentation is to provide a comprehensive guide for migrating from ADF to FDF, ensuring a smooth transition and minimizing downtime.

## 2. Conceptual Overview

The conceptual model for migrating from ADF to FDF consists of three major components: 
1. **Data Pipeline Migration**: This involves transferring data pipelines, including datasets, linked services, and activities, from ADF to FDF.
2. **Workflow Orchestration**: This component focuses on migrating workflows, including schedules, triggers, and dependencies, to ensure seamless execution in FDF.
3. **Dependency Management**: This involves identifying, mapping, and migrating dependencies, such as libraries, scripts, and connectors, to ensure compatibility and functionality in FDF.

These components interact to produce a migrated data factory that maintains the original functionality and performance, while leveraging the features and benefits of FDF.

## 3. Scope and Non-Goals

Clarify the explicit boundaries of this documentation.

**In scope:**
* Data pipeline migration strategies
* Workflow orchestration techniques
* Dependency management best practices

**Out of scope:**
* Tool-specific implementations (e.g., Azure portal, PowerShell, or SDKs)
* Vendor-specific behavior (e.g., Microsoft or Fabric-specific features)
* Operational or procedural guidance (e.g., monitoring, troubleshooting, or security)

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

Provide precise, stable definitions for all key terms used throughout this document.

| Term | Definition |
|------|------------|
| Azure Data Factory (ADF) | A cloud-based data integration service for creating, scheduling, and managing data pipelines |
| Fabric Data Factory (FDF) | A cloud-based data integration platform for creating, scheduling, and managing data pipelines |
| Data Pipeline | A series of activities that extract, transform, and load data from source systems to target systems |
| Workflow | A sequence of activities that are executed in a specific order to achieve a business outcome |
| Dependency | A component or library that is required for a data pipeline or workflow to function correctly |

> [!TIP]
> Definitions should avoid contextual or time-bound language and remain valid as the ecosystem evolves.

## 5. Core Concepts

Explain the fundamental ideas that form the basis of this topic.

### 5.1 Data Pipeline Migration
Data pipeline migration involves transferring datasets, linked services, and activities from ADF to FDF. This process requires careful planning, mapping, and testing to ensure that the migrated pipelines maintain their original functionality and performance.

### 5.2 Workflow Orchestration
Workflow orchestration involves migrating schedules, triggers, and dependencies to ensure that workflows are executed correctly in FDF. This component requires a deep understanding of the workflow logic, dependencies, and execution sequences.

### 5.3 Concept Interactions and Constraints
The core concepts interact through a complex web of dependencies and constraints. For example, data pipeline migration depends on the availability of compatible linked services and datasets in FDF. Similarly, workflow orchestration requires a thorough understanding of the workflow logic and dependencies to ensure correct execution.

## 6. Standard Model

Describe the generally accepted or recommended model for this topic.

### 6.1 Model Description
The standard model for migrating from ADF to FDF involves a phased approach:
1. **Assessment**: Evaluate the existing ADF environment, including data pipelines, workflows, and dependencies.
2. **Planning**: Develop a migration plan, including mapping, testing, and validation strategies.
3. **Migration**: Execute the migration plan, transferring data pipelines, workflows, and dependencies to FDF.
4. **Testing**: Verify that the migrated environment functions correctly, including data pipelines, workflows, and dependencies.
5. **Deployment**: Deploy the migrated environment to production, ensuring minimal downtime and disruption.

### 6.2 Assumptions
The standard model assumes that:
* The ADF environment is well-documented and understood.
* The FDF environment is properly configured and provisioned.
* The migration team has the necessary skills and expertise.

### 6.3 Invariants
The standard model defines the following invariants:
* Data pipelines must maintain their original functionality and performance.
* Workflows must be executed correctly, with minimal disruption or downtime.
* Dependencies must be properly mapped and migrated to ensure compatibility and functionality.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified.

## 7. Common Patterns

Document recurring, accepted patterns associated with this topic.

### Pattern A: Lift-and-Shift Migration
- **Intent**: Migrate data pipelines and workflows from ADF to FDF with minimal changes.
- **Context**: Suitable for simple, well-documented environments with minimal dependencies.
- **Tradeoffs**: Fast migration, minimal risk, but may not leverage FDF features and benefits.

## 8. Anti-Patterns

Describe common but discouraged practices.

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Big-Bang Migration
- **Description**: Migrate the entire ADF environment to FDF in a single, large-scale effort.
- **Failure Mode**: High risk of errors, downtime, and disruption due to complexity and scope.
- **Common Causes**: Insufficient planning, inadequate testing, and poor change management.

## 9. Edge Cases and Boundary Conditions

Explain unusual or ambiguous scenarios that may challenge the standard model.

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

* **Incomplete Documentation**: ADF environment is poorly documented, making it difficult to assess and migrate.
* **Custom or Proprietary Components**: ADF environment includes custom or proprietary components that are not compatible with FDF.

## 10. Related Topics

List adjacent, dependent, or prerequisite topics.

* Data Integration
* Workflow Orchestration
* Cloud Migration
* Data Pipeline Management

## 11. References

Provide exactly **five** authoritative external references that substantiate or inform this topic.

1. **Azure Data Factory Documentation**  
   Microsoft  
   https://docs.microsoft.com/en-us/azure/data-factory/  
   *Justification*: Official documentation for Azure Data Factory, providing detailed information on data pipeline migration and workflow orchestration.
2. **Fabric Data Factory Documentation**  
   Fabric  
   https://docs.fabric.io/data-factory/  
   *Justification*: Official documentation for Fabric Data Factory, providing detailed information on data pipeline migration and workflow orchestration.
3. **Cloud Data Integration Patterns**  
   Gartner  
   https://www.gartner.com/en/documents/3989617/cloud-data-integration-patterns  
   *Justification*: Research report providing insights into cloud data integration patterns, including migration strategies and best practices.
4. **Data Pipeline Migration Best Practices**  
   AWS  
   https://aws.amazon.com/blogs/big-data/data-pipeline-migration-best-practices/  
   *Justification*: Blog post providing best practices for data pipeline migration, including assessment, planning, and execution strategies.
5. **Workflow Orchestration in Cloud Environments**  
   IBM  
   https://www.ibm.com/cloud/blog/workflow-orchestration-in-cloud-environments  
   *Justification*: Blog post providing insights into workflow orchestration in cloud environments, including migration strategies and best practices.

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial documentation |

---

This documentation provides a comprehensive guide for migrating from Azure Data Factory to Fabric Data Factory, covering conceptual models, terminology, core concepts, standard models, common patterns, anti-patterns, edge cases, and related topics. By following this documentation, organizations can ensure a smooth transition and minimize downtime, while leveraging the features and benefits of Fabric Data Factory.