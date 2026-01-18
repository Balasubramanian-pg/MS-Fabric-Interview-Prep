# [Custom Fabric Item Types](Part 20 EdgeCases BossLevel/Custom Fabric Item Types.md)

Canonical documentation for [Custom Fabric Item Types](Part 20 EdgeCases BossLevel/Custom Fabric Item Types.md). This document defines concepts, terminology, and standard usage.

## Purpose
The concept of [Custom Fabric Item Types](Part 20 EdgeCases BossLevel/Custom Fabric Item Types.md) exists to provide an extensibility framework for unified data platforms (Fabrics). As data ecosystems evolve, native item types (such as warehouses, lakes, or pipelines) may not satisfy every specialized domain or third-party integration requirement. 

Custom Item Types allow developers to define new, first-class entities that inherit the platform's core capabilities—such as security, governance, lineage, and workspace management—while providing specialized functionality, unique metadata schemas, and custom user interfaces. This ensures a consistent user experience across disparate tools and services within a single ecosystem.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The architectural contract between the platform host and the custom item.
* Metadata structures and lifecycle management of custom entities.
* Theoretical frameworks for item registration and discovery.
* Security and governance inheritance models.

**Out of scope:**
* Specific vendor SDKs or API syntax (e.g., Microsoft Fabric Workload Development Kit).
* Frontend framework-specific implementation details (e.g., React vs. Angular components).
* Pricing or licensing models for third-party extensions.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Fabric Host** | The primary platform or ecosystem that orchestrates resources, security, and the user interface. |
| **Item Type Definition** | The blueprint or manifest that defines the properties, capabilities, and icons of a custom item. |
| **Item Instance** | A specific, named occurrence of a Custom Item Type created within a workspace or environment. |
| **Payload** | The internal data or configuration specific to the custom item that the Fabric Host stores but does not necessarily interpret. |
| **Lifecycle Event** | Standardized triggers (Create, Read, Update, Delete) that the Host communicates to the Custom Item provider. |
| **Manifest** | A declarative file describing the Custom Item Type’s identity, permissions, and entry points. |

## Core Concepts

### 1. The Extensibility Contract
[Custom Fabric Item Types](Part 20 EdgeCases BossLevel/Custom Fabric Item Types.md) operate on a contract-based model. The Fabric Host provides the infrastructure (storage, compute, identity), and the Custom Item provider fulfills a set of required interfaces. This allows the custom item to appear "native" to the end-user.

### 2. Metadata vs. Payload
*   **Metadata:** Information the Fabric Host understands (Name, ID, Owner, Created Date, Lineage).
*   **Payload:** Information only the Custom Item provider understands (Specific logic, proprietary configurations, or internal state).

### 3. Identity and Security Inheritance
Custom items must participate in the Host's Role-Based Access Control (RBAC). A custom item does not typically manage its own identity provider; instead, it maps Host-provided tokens to internal permissions.

### 4. Manifest-Driven Registration
The definition of a Custom Item Type is usually governed by a manifest. This manifest acts as the "source of truth" for the Host to know how to render the item in menus, what APIs to call for lifecycle events, and what permissions are required to interact with it.

## Standard Model

The standard model for a Custom Fabric Item Type follows a decoupled architecture:

1.  **Registration Layer:** The developer registers the Item Type with the Host via a Manifest.
2.  **Control Plane:** The Host manages the item's existence (CRUD operations) and visibility in the workspace.
3.  **Data/Logic Plane:** The Custom Item provider hosts the actual logic or data processing, often residing in an external service or a sandboxed environment within the Fabric.
4.  **UI Integration:** The Host provides a "canvas" (often an iframe or web component) where the Custom Item provider renders its specific configuration or monitoring interface.

## Common Patterns

### The Proxy Pattern
The Custom Item acts as a pointer to a resource hosted in an external cloud or on-premises environment. The Fabric Item manages the metadata and access, while the actual work happens elsewhere.

### The Wrapper Pattern
The Custom Item encapsulates an existing open-source tool (e.g., a specialized visualization library) to make it compliant with the Fabric’s governance and sharing rules.

### The Virtualized Item
An item that does not have a persistent state but generates its content dynamically based on the context of the workspace it resides in.

## Anti-Patterns

*   **Bypassing Host Security:** Implementing a secondary authentication layer within the Custom Item that ignores the Fabric Host's identity context.
*   **Opaque Payloads:** Storing critical business logic in a format that cannot be version-controlled or audited by the Host's governance tools.
*   **Monolithic Design:** Building a Custom Item that attempts to replicate native Fabric features (like its own internal file system) rather than leveraging the Host's native capabilities.
*   **Hard-Coding Environment Logic:** Creating Item Types that rely on specific URL structures or hard-coded IDs, preventing portability across different Fabric tenants.

## Edge Cases

*   **Orphaned Instances:** What happens to an Item Instance if the Item Type Definition is deleted from the Host? Standard practice requires a "soft-delete" or a read-only state for existing instances.
*   **Version Mismatch:** When a Custom Item Type is updated to a new version, existing instances may require a migration path or a compatibility shim to prevent breaking the UI/API.
*   **Cross-Tenant Sharing:** Handling permissions when a Custom Item is shared with a user outside the home tenant, especially if the Custom Item's backend logic resides in a specific geographic region.
*   **Circular Dependencies:** A Custom Item that requires a native item (e.g., a Lakehouse) to function, but the native item is deleted or moved.

## Related Topics
*   **Fabric Workspace Management:** The container logic where items reside.
*   **Data Lineage and Provenance:** How custom items report their data inputs and outputs.
*   **Managed Identities:** How the custom item service authenticates back to the Fabric Host.
*   **Tenant Settings:** Global configurations that may enable or disable custom item extensibility.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |