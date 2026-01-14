# Can You Split One Fabric Capacity Across Multiple Azure Subscriptions

Canonical documentation for Can You Split One Fabric Capacity Across Multiple Azure Subscriptions. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

The topic of splitting one fabric capacity across multiple Azure subscriptions exists to address the need for efficient resource allocation and management within large-scale cloud deployments. It aims to provide a solution for organizations that require distributing their fabric capacity across multiple subscriptions, either due to organizational structure, billing requirements, or scalability needs. Misunderstanding or inconsistent application of this concept can lead to inefficient resource utilization, increased costs, and complexity in managing Azure resources.

## 2. Conceptual Overview

The conceptual model for splitting fabric capacity across multiple Azure subscriptions involves several key components:
- **Fabric Capacity**: The total amount of resources (e.g., compute, storage) available for allocation.
- **Azure Subscriptions**: The logical containers for Azure resources, each with its own billing, access control, and resource quotas.
- **Resource Allocation**: The process of distributing fabric capacity across one or more Azure subscriptions.

These components interact to produce outcomes such as optimized resource utilization, simplified billing, and enhanced scalability. The model is designed to facilitate flexible and efficient management of Azure resources across multiple subscriptions.

## 3. Scope and Non-Goals

Clarify the explicit boundaries of this documentation.

**In scope:**
* Conceptual models for fabric capacity allocation
* Terminology related to Azure subscriptions and resource management
* Standard patterns for splitting fabric capacity

**Out of scope:**
* Tool-specific implementations for managing Azure resources
* Vendor-specific behavior beyond standard Azure functionality
* Operational or procedural guidance for day-to-day management

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

Provide precise, stable definitions for all key terms used throughout this document.

| Term | Definition |
|------|------------|
| Fabric Capacity | The total amount of resources (compute, storage, etc.) available for allocation within an Azure environment. |
| Azure Subscription | A logical container for Azure resources, defining billing, access control, and resource quotas. |
| Resource Allocation | The process of assigning available resources to specific workloads or applications within Azure. |

> [!TIP]
> Definitions should avoid contextual or time-bound language and remain valid as the ecosystem evolves.

## 5. Core Concepts

Explain the fundamental ideas that form the basis of this topic.

### 5.1 Fabric Capacity
Fabric capacity refers to the total resources available for allocation within an Azure environment. It includes compute resources (like virtual machines), storage resources, and network resources.

### 5.2 Azure Subscriptions
Azure subscriptions are the foundational elements for organizing Azure resources. Each subscription can have its own set of resources, access controls, and billing arrangements.

### 5.3 Concept Interactions and Constraints
Fabric capacity can be split across multiple Azure subscriptions, allowing for flexible resource allocation and management. However, this splitting is subject to constraints such as subscription limits, resource quotas, and access control policies. Understanding these interactions and constraints is crucial for effective resource management.

## 6. Standard Model

Describe the generally accepted or recommended model for this topic.

### 6.1 Model Description
The standard model involves allocating fabric capacity to specific Azure subscriptions based on organizational needs, resource requirements, and billing preferences. This allocation is typically managed through Azure Resource Manager (ARM) templates or the Azure portal.

### 6.2 Assumptions
The model assumes that the organization has a clear understanding of its resource needs, subscription limits, and access control requirements. It also assumes that the necessary permissions and access rights are in place for managing resources across subscriptions.

### 6.3 Invariants
The model must always ensure that:
- Resource allocations do not exceed subscription limits.
- Access control policies are consistently applied across subscriptions.
- Billing and cost tracking are accurately managed for each subscription.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified.

## 7. Common Patterns

Document recurring, accepted patterns associated with this topic.

### Pattern A: Centralized Resource Management
- **Intent:** To simplify resource allocation and management by centralizing control.
- **Context:** Typically applied in large, complex Azure deployments.
- **Tradeoffs:** Simplified management vs. potential for over-centralization and reduced autonomy for individual teams.

## 8. Anti-Patterns

Describe common but discouraged practices.

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Overlapping Subscriptions
- **Description:** Creating multiple subscriptions with overlapping resource allocations, leading to confusion and inefficiency.
- **Failure Mode:** Difficulty in managing resources, tracking costs, and applying access controls.
- **Common Causes:** Lack of clear organizational structure or insufficient planning in resource allocation.

## 9. Edge Cases and Boundary Conditions

Explain unusual or ambiguous scenarios that may challenge the standard model.

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

- **Scenario:** An organization has a single fabric capacity that needs to be split across subscriptions in different Azure regions due to data sovereignty requirements.
- **Challenge:** Ensuring consistent resource allocation, access control, and billing management across regions.

## 10. Related Topics

List adjacent, dependent, or prerequisite topics.
- Azure Resource Manager (ARM)
- Azure Subscriptions and Billing
- Access Control and Identity Management in Azure

## 11. References

Provide exactly **five** authoritative external references that substantiate or inform this topic.

1. **Azure Documentation: Azure Subscriptions**  
   Microsoft Corporation  
   https://docs.microsoft.com/en-us/azure/cost-management-billing/manage/mca-overview  
   *Justification:* Official Microsoft documentation on Azure subscriptions and billing.
2. **Azure Resource Manager Documentation**  
   Microsoft Corporation  
   https://docs.microsoft.com/en-us/azure/azure-resource-manager/  
   *Justification:* Official documentation on Azure Resource Manager, crucial for understanding resource allocation and management.
3. **Microsoft Azure Well-Architected Framework**  
   Microsoft Corporation  
   https://docs.microsoft.com/en-us/azure/architecture/framework/  
   *Justification:* Provides best practices and guidelines for designing and operating reliable, secure, and high-performing workloads in Azure.
4. **Azure Cost Estimation and Optimization**  
   Microsoft Corporation  
   https://docs.microsoft.com/en-us/azure/cost-management-billing/costs/quick-acm-costs-estimation  
   *Justification:* Official guidance on estimating and optimizing Azure costs, relevant to managing fabric capacity across subscriptions.
5. **Azure Security and Compliance**  
   Microsoft Corporation  
   https://docs.microsoft.com/en-us/azure/security/fundamentals/  
   *Justification:* Essential for understanding security and compliance aspects of managing Azure resources and subscriptions.

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

> [!CAUTION]
> If fewer than five authoritative references exist, explicitly state this.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial documentation |

---

This documentation provides a comprehensive overview of splitting one fabric capacity across multiple Azure subscriptions, covering conceptual models, terminology, core concepts, and standard practices. It serves as a foundational resource for understanding and managing Azure resources efficiently.