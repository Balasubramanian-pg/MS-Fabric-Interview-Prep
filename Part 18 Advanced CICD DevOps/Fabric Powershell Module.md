# Fabric Powershell Module

Canonical documentation for Fabric Powershell Module. This document defines concepts, terminology, and standard usage.

## Purpose
The Fabric PowerShell Module exists to provide a programmatic, command-line interface for the administration, orchestration, and automation of a unified data fabric environment. It addresses the need for scalable management of distributed data architectures, enabling administrators and engineers to perform complex operations—such as resource provisioning, security configuration, and metadata management—without relying on graphical user interfaces. 

By abstracting the underlying REST APIs into standardized cmdlets, the module facilitates the integration of data fabric management into DevOps pipelines, disaster recovery workflows, and enterprise-scale governance frameworks.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the architectural role of PowerShell modules in data fabric ecosystems.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Lifecycle Management:** Creation, update, and deletion of fabric artifacts and containers.
* **Access Control:** Programmatic management of identities, roles, and permissions.
* **Capacity Management:** Monitoring and scaling of compute and storage resources.
* **Automation Patterns:** Standards for scripting, error handling, and pipeline integration.
* **Metadata Operations:** Retrieval and modification of object properties and lineage.

**Out of scope:**
* **Data Plane Operations:** The actual processing of data within a notebook or SQL engine (e.g., writing Spark code).
* **Vendor-Specific Extensions:** Proprietary add-ons that deviate from the core fabric management model.
* **PowerShell Language Fundamentals:** General scripting concepts not unique to the Fabric context.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Artifact (Item)** | A discrete unit of data or logic within the fabric, such as a lakehouse, warehouse, or report. |
| **Capacity** | A dedicated pool of resources (compute/storage) that powers the fabric operations. |
| **Cmdlet** | A lightweight command used in the PowerShell environment to perform a specific action. |
| **Idempotency** | The property of a script or command where multiple executions result in the same state without unintended side effects. |
| **Service Principal** | An application-based identity used for automated authentication without human intervention. |
| **Tenant** | The top-level organizational boundary representing a single instance of the data fabric. |
| **Workspace** | A logical container used to group related artifacts and manage collaborative access. |

## Core Concepts

### 1. Verb-Noun Syntax
Following the standard shell convention, the module utilizes a `Verb-Noun` structure (e.g., `Get-FabricWorkspace`). This ensures predictability and discoverability for users familiar with command-line environments.

### 2. Authentication and Context
The module operates within a security context. It supports multiple authentication flows, including interactive login for administrators and non-interactive (token-based or secret-based) login for automated systems. The "Context" maintains the session state, including the target environment and active credentials.

### 3. Resource Hierarchy
The module respects the hierarchical nature of the data fabric:
* **Tenant Level:** Global settings and capacity allocation.
* **Workspace Level:** Security boundaries and group-based management.
* **Item Level:** Individual artifact configuration and metadata.

### 4. Asynchronous Operations
Many fabric operations (like provisioning a large warehouse) are long-running. The module provides mechanisms to initiate these tasks, track their status via "Operation IDs," and optionally wait for completion before proceeding.

## Standard Model
The recommended model for utilizing the Fabric PowerShell Module is based on the **State-Driven Automation** approach:

1.  **Connect:** Establish a secure session using the most restrictive identity necessary for the task.
2.  **Discover:** Query the current state of the environment (e.g., `Get` commands) to determine if changes are required.
3.  **Execute:** Apply changes (e.g., `New`, `Set`, or `Remove` commands) to reach the desired state.
4.  **Verify:** Confirm the operation was successful through post-execution metadata checks.
5.  **Disconnect:** Explicitly terminate the session to ensure security and resource cleanup.

## Common Patterns

### Bulk Provisioning
Using loops and configuration files (JSON/CSV) to create hundreds of workspaces or items simultaneously, ensuring consistency across departments.

### Permission Auditing
Iterating through all workspaces and artifacts to export a comprehensive matrix of user permissions for compliance reporting.

### CI/CD Integration
Using the module within a build agent to deploy artifacts from a development environment to a production environment, often involving the updating of connection strings or parameters.

### Capacity Monitoring
Scheduled scripts that check the utilization of compute resources and trigger alerts or scale-up actions if thresholds are exceeded.

## Anti-Patterns

*   **Hardcoding Credentials:** Storing usernames, passwords, or secrets directly within scripts instead of using secure secret management systems.
*   **Ignoring Pipeline Input:** Failing to design scripts that accept piped objects, which limits the ability to chain commands efficiently.
*   **Lack of Error Handling:** Assuming every command will succeed and failing to implement `Try-Catch` blocks, leading to "zombie" resources or partial deployments.
*   **Over-Polling:** Repeatedly querying the API for status updates at high frequency, which can lead to rate-limiting or throttling by the fabric provider.

## Edge Cases

*   **Orphaned Artifacts:** Scenarios where a workspace is deleted but underlying capacity or metadata references persist due to interrupted execution.
*   **Throttling Limits:** When executing bulk operations, the module may encounter API rate limits. Scripts must be designed to handle "429 Too Many Requests" errors gracefully using exponential backoff.
*   **Cross-Region Operations:** Managing artifacts in a different geographic region than the one where the PowerShell session was initiated may introduce latency or specific data residency constraints.
*   **Large Metadata Payloads:** Commands that return thousands of objects may require pagination or server-side filtering to prevent memory exhaustion on the client machine.

## Related Topics
*   **Fabric REST APIs:** The underlying interface that the PowerShell module wraps.
*   **Service Principal Authentication:** The standard for non-human access to the fabric.
*   **Infrastructure as Code (IaC):** The broader discipline of managing resources via configuration files.
*   **Data Governance:** The policy framework that the PowerShell module helps enforce.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial AI-generated canonical documentation |