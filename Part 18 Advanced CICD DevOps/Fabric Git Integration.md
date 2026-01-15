# Fabric Git Integration

Canonical documentation for Fabric Git Integration. This document defines concepts, terminology, and standard usage.

## Purpose
Fabric Git Integration exists to bridge the gap between interactive, low-code/no-code data orchestration environments and formal software engineering lifecycles. It addresses the problem of "state volatility" in data platforms by providing a mechanism to persist, version, and audit the configuration and logic of data artifacts. 

By integrating a Version Control System (VCS) directly into the data workspace, organizations can transition from manual, ad-hoc changes to a disciplined DataOps methodology, enabling collaborative development, automated deployment pipelines, and disaster recovery.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The synchronization mechanism between live data workspaces and remote repositories.
* The serialization of data artifacts into machine-readable formats.
* Lifecycle management of data items (creation, modification, deletion) via Git.
* Conflict resolution strategies within a data-centric context.

**Out of scope:**
* Specific Git provider features (e.g., GitHub Actions, Azure DevOps Pipelines).
* Detailed internal file schemas for specific data items (e.g., .PBIP or .NB format specifics).
* Physical data storage (Data Lake/Warehouse) versioning (e.g., Delta Lake time travel).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Workspace** | A logical container in the data platform where items (reports, notebooks, pipelines) reside and execute. |
| **Remote Repository** | The hosted Git-based storage that serves as the source of truth for the workspace's metadata. |
| **Serialization** | The process of converting a live workspace item into a structured file format (usually JSON or XML) suitable for version control. |
| **Synchronization (Sync)** | The bidirectional process of aligning the state of the Workspace with the state of the Remote Repository. |
| **Item** | A discrete functional unit within the fabric (e.g., a Semantic Model, a Data Factory Pipeline, or a Notebook). |
| **Head State** | The most recent commit in the connected branch of the Remote Repository. |
| **Uncommitted Change** | A modification made in the Workspace that has not yet been serialized and pushed to the Remote Repository. |

## Core Concepts

### 1. Bidirectional Synchronization
Fabric Git Integration operates on a bidirectional model. Changes can originate in the Workspace (via the UI) or in the Remote Repository (via code edits or merges). The integration layer monitors both environments for "drift" and provides mechanisms to reconcile differences.

### 2. Metadata Representation
Unlike traditional software where the code *is* the application, data platform items often consist of complex configurations. Integration requires these items to be decomposed into a directory structure containing metadata files. This allows Git to track granular changes rather than treating items as opaque binary blobs.

### 3. Connection Mapping
A fundamental concept is the 1:1 mapping between a Workspace and a specific Branch within a Remote Repository. This mapping ensures a deterministic relationship between the live environment and the versioned code.

## Standard Model

The standard model for Fabric Git Integration follows a "Workspace-as-Client" architecture:

1.  **Connection:** A Workspace is linked to a specific Git URL and Branch.
2.  **Detection:** The system identifies discrepancies between the Workspace's current state and the Branch's Head State.
3.  **Action:**
    *   **Source Control (Push):** The user serializes Workspace changes into a commit and pushes them to the Remote.
    *   **Update (Pull):** The user applies changes from the Remote to the Workspace, overwriting or updating live items.
4.  **Validation:** Before a sync is finalized, the system validates that the incoming metadata is compatible with the platform's runtime requirements.

## Common Patterns

### Feature Branching per Developer
Each developer works in a personal Workspace connected to a unique feature branch. Once the work is complete, a Pull Request (PR) is opened in the Remote Repository to merge the feature branch into a shared development or main branch.

### Environment Promotion (DTAP)
Separate Workspaces are created for Development, Test, and Production. Each Workspace is connected to a corresponding branch (e.g., `dev`, `test`, `main`). Promotion of logic occurs through Git merges rather than manual exports/imports.

### Programmatic Modification
Since items are stored as serialized files, developers can use external scripts or IDEs (like VS Code) to perform bulk updates to metadata (e.g., renaming variables across 50 reports) and push those changes directly to the repository to be synced back to the Workspace.

## Anti-Patterns

*   **Direct Production Editing:** Making manual changes in a Production Workspace without committing them to Git. This creates "Configuration Drift" and makes the environment non-reproducible.
*   **Monolithic Branches:** Connecting multiple Workspaces to the same branch for active development, leading to frequent race conditions and sync conflicts.
*   **Git as Backup Only:** Using the integration merely to "save" work at the end of the week, rather than using it as a functional part of the development lifecycle.
*   **Ignoring System Files:** Manually deleting hidden metadata or configuration files within the repository that the platform requires for serialization.

## Edge Cases

*   **Unsupported Item Types:** Not all items in a data platform may support Git integration. These "orphaned" items must be managed manually, creating a hybrid deployment model.
*   **Circular Dependencies:** When Item A depends on Item B, and Item B depends on Item A, the synchronization engine may struggle to determine the order of creation during an initial "Pull" into an empty workspace.
*   **Large Metadata Files:** Items with exceptionally large metadata (e.g., a Semantic Model with thousands of measures) may hit Git provider file size limits or cause timeouts during serialization.
*   **Conflict Resolution in UI:** When a conflict occurs (e.g., the same item was changed in the Workspace and the Remote), the platform may require the user to choose one version entirely, as line-by-line merging of complex JSON metadata is often non-trivial for end-users.

## Related Topics

*   **Continuous Integration/Continuous Deployment (CI/CD):** The automation layer that sits on top of Git Integration.
*   **Application Lifecycle Management (ALM):** The broader strategy of managing the life of an application from requirements to retirement.
*   **Role-Based Access Control (RBAC):** The security layer that governs who can initiate Sync, Push, or Pull operations.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial AI-generated canonical documentation |