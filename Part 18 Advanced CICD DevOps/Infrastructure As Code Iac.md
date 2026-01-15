# Infrastructure As Code Iac

Canonical documentation for Infrastructure As Code Iac. This document defines concepts, terminology, and standard usage.

## Purpose
Infrastructure as Code (IaC) is a methodology designed to manage and provision computing infrastructure through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools. 

The primary purpose of IaC is to eliminate the "environmental drift" associated with manual configuration, enabling infrastructure to be treated with the same rigor as application source code. It addresses the need for consistency, scalability, and speed in modern software delivery by providing a systematic way to version, test, and deploy infrastructure components.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   **Definition and Lifecycle:** The conceptual framework for managing infrastructure via code.
*   **Theoretical Boundaries:** The distinction between declarative and imperative approaches.
*   **State Management:** The logic behind tracking the relationship between code and physical assets.
*   **Core Principles:** Idempotency, immutability, and versioning.

**Out of scope:**
*   **Specific vendor implementations:** Detailed syntax for specific tools (e.g., Terraform, CloudFormation, Ansible, Pulumi).
*   **Hardware-level specifications:** Physical cabling or data center facility management.
*   **Application-level code:** The business logic residing on the infrastructure.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Idempotency** | The property where an operation can be applied multiple times without changing the result beyond the initial application. |
| **Declarative** | A functional approach where the desired end-state is defined, and the system determines the steps to achieve it. |
| **Imperative** | A procedural approach where specific commands and sequences are defined to reach a desired state. |
| **Drift** | The divergence between the actual state of the infrastructure and the state defined in the source code. |
| **Immutable Infrastructure** | A paradigm where infrastructure components are replaced rather than modified or updated in place. |
| **State** | A record of the current status and metadata of managed resources, used to map code definitions to real-world entities. |
| **Provisioning** | The process of setting up IT infrastructure; distinct from configuration management, though often overlapping. |

## Core Concepts

### 1. Infrastructure as Software
IaC applies software engineering practices to infrastructure. This includes version control (Git), continuous integration/continuous deployment (CI/CD), and automated testing. By treating infrastructure as software, teams can perform code reviews and maintain a history of changes.

### 2. Idempotency
A cornerstone of IaC is the guarantee that the deployment process can be executed repeatedly with the same input, resulting in the same environment state. This ensures that re-running a script does not create duplicate resources or cause errors if the resource already exists.

### 3. Single Source of Truth
The code repository serves as the definitive record of the infrastructure. Any change to the environment must originate in the code. If the environment is modified manually, it is considered "corrupted" until reconciled with the code.

### 4. Convergence
The process by which an IaC system identifies the difference between the "Current State" and the "Desired State" and executes the necessary actions to bring the environment into alignment.

## Standard Model

The standard model for IaC follows a four-stage lifecycle:

1.  **Definition:** The user writes code (usually in a Domain Specific Language or standard programming language) describing the desired resources (networks, servers, databases).
2.  **Plan/Preview:** The IaC engine compares the code against the existing state and generates an execution plan, showing what will be created, modified, or destroyed.
3.  **Execution/Apply:** The engine interacts with the target provider (Cloud API, Hypervisor, etc.) to provision or modify the resources.
4.  **State Persistence:** The engine updates a state file or database to record the current mapping of code to physical IDs, ensuring future runs are aware of existing assets.

## Common Patterns

### Modularization
Breaking infrastructure into small, reusable, and independent components (e.g., a "VPC module" or a "Database module"). This promotes reuse and limits the "blast radius" of changes.

### GitOps
A pattern where the Git repository is the only trigger for infrastructure changes. Automated agents monitor the repository and automatically apply changes to the environment when the code is merged.

### Policy as Code (PaC)
Integrating automated compliance and security checks into the IaC pipeline. Before infrastructure is provisioned, it is scanned against a set of rules (e.g., "No public S3 buckets").

### Blue/Green Infrastructure
Using IaC to spin up an entirely new environment (Green) alongside the old one (Blue). Traffic is cut over once the Green environment is validated, allowing for near-zero downtime updates.

## Anti-Patterns

### ClickOps (Manual Overrides)
Making manual changes to the infrastructure via a GUI or CLI after it has been provisioned by code. This leads to drift and makes the code unreliable.

### Monolithic Templates
Defining an entire global infrastructure in a single, massive file. This increases complexity, makes testing difficult, and increases the risk of accidental deletion of unrelated resources.

### Hardcoding
Embedding environment-specific values (IP addresses, secret keys, instance names) directly in the code rather than using variables or external secret managers.

### Ghost Resources
Resources that are created by an IaC process but are no longer tracked in the state file, often due to interrupted executions or manual deletions, leading to "zombie" costs and security risks.

## Edge Cases

### Circular Dependencies
Occurs when Resource A requires an attribute from Resource B, but Resource B also requires an attribute from Resource A. This requires multi-stage provisioning or "placeholder" resources.

### External State Modification
When an external system (like an auto-scaling group or a cloud provider's automated maintenance) modifies a resource. The IaC tool must be configured to either ignore these specific attributes or incorporate them into the state.

### Provider API Rate Limiting
In massive-scale infrastructures, the IaC tool may be throttled by the cloud provider's API. This requires implementing back-off strategies or splitting the infrastructure into smaller state files.

## Related Topics
*   **DevOps:** The cultural and professional movement that IaC supports.
*   **Configuration Management:** The practice of managing software and settings on the provisioned infrastructure.
*   **Site Reliability Engineering (SRE):** The discipline that utilizes IaC for maintaining system availability.
*   **Cloud Computing:** The primary delivery model for which IaC is utilized.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial AI-generated canonical documentation |