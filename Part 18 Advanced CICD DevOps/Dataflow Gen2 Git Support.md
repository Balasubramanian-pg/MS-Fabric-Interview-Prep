# Dataflow Gen2 Git Support

Canonical documentation for Dataflow Gen2 Git Support. This document defines concepts, terminology, and standard usage.

## Purpose
Dataflow Gen2 Git Support exists to bridge the gap between low-code/no-code data transformation environments and professional software engineering lifecycles. It addresses the need for version control, collaborative development, and automated deployment pipelines (CI/CD) within data engineering workflows. By providing a mechanism to synchronize visual dataflow definitions with a remote repository, it enables auditability, rollback capabilities, and multi-developer coordination.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   **Serialization Mechanisms:** The conversion of visual dataflow metadata into machine-readable formats for storage.
*   **Synchronization Logic:** The bidirectional flow of data between the authoring environment and the source control system.
*   **Lifecycle Management:** The theoretical framework for branching, merging, and deploying dataflows across environments.
*   **Conflict Resolution:** The principles of handling divergent versions of dataflow definitions.

**Out of scope:**
*   Specific vendor-specific UI navigation (e.g., specific buttons in Microsoft Fabric or Power BI).
*   Third-party Git provider hosting details (e.g., GitHub vs. GitLab internal infrastructure).
*   Data plane execution (how the data is processed after the flow is defined).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Dataflow Gen2** | A cloud-native data preparation engine that supports high-scale compute and output destinations. |
| **Serialization** | The process of translating a visual dataflow graph and its associated logic into a text-based format (typically JSON or TMSL) suitable for Git. |
| **Workspace Sync** | The state of alignment between the live authoring environment and the connected Git branch. |
| **Commit/Push** | The act of persisting local workspace changes to the remote repository. |
| **Update/Pull** | The act of applying changes from the remote repository to the local authoring workspace. |
| **Metadata Definition** | The underlying code representation of the dataflow, including connection references, transformation steps, and schema mappings. |

## Core Concepts

### 1. Metadata Representation
Unlike traditional code, Dataflow Gen2 is often authored visually. Git support requires these visual representations to be serialized into a declarative format. This format must capture:
*   **Query Logic:** The functional transformations (e.g., M-code or SQL).
*   **Orchestration Metadata:** Refresh schedules and execution parameters.
*   **Lineage:** Connections between sources and destinations.

### 2. Bidirectional Synchronization
Git support implies a two-way relationship. Changes made in the visual editor must be exportable to Git, and changes made to the code in Git (via pull requests or direct edits) must be importable back into the visual editor.

### 3. Environment Isolation
Git support enables the separation of concerns by allowing different branches or repositories to represent different stages of the data lifecycle (Development, Testing, Production).

## Standard Model
The standard model for Dataflow Gen2 Git Support follows the **Workspace-as-a-Branch** paradigm:

1.  **Connection:** A specific workspace is linked to a specific branch in a Git repository.
2.  **Authoring:** Developers modify the dataflow within the workspace.
3.  **Persistence:** Changes are committed to the branch, creating a version history.
4.  **Validation:** The serialized metadata is validated against the dataflow engine's schema to ensure the code remains "loadable."
5.  **Deployment:** Merging a branch into a "Main" or "Production" branch triggers a synchronization to a separate production workspace.

## Common Patterns

### Feature Branching
Developers create individual branches for specific dataflow enhancements. They connect their private developer workspaces to these branches to iterate without affecting the shared codebase.

### Shared Integration Workspace
A centralized workspace connected to a "Develop" branch where multiple developers merge their changes to test interoperability before moving to production.

### Automated Documentation
Using the serialized JSON/code in Git to automatically generate data dictionaries or lineage diagrams using external documentation tools.

## Anti-Patterns

*   **Direct Production Editing:** Modifying a dataflow directly in a production workspace without going through the Git commit/merge process. This creates "configuration drift."
*   **Monolithic Dataflows:** Creating a single, massive dataflow that results in a massive serialized file, increasing the likelihood of merge conflicts.
*   **Storing Secrets in Git:** Including hardcoded credentials or connection strings in the dataflow definition. Standard practice dictates using environment-specific parameters or Key Vault references.
*   **Manual Serialization:** Manually exporting and importing files instead of using the native Git integration, which breaks the chain of custody and version history.

## Edge Cases

*   **Unsupported Connectors:** Some legacy or custom connectors may not serialize correctly, leading to "broken" definitions when pulled from Git.
*   **Circular Dependencies:** When two dataflows in the same repository reference each other in a way that prevents a clean "initial load" from Git.
*   **Schema Drift:** When the underlying data source changes its schema, the Git-stored definition may become invalid even if the code itself hasn't changed.
*   **Merge Conflicts in Visual Logic:** Resolving conflicts in a serialized JSON file can be difficult for humans. If two developers change the same transformation step, the resulting JSON conflict may require specialized knowledge of the serialization format to fix.

## Related Topics
*   **Data Lifecycle Management (DLM):** The broader strategy for managing data assets.
*   **Continuous Integration/Continuous Deployment (CI/CD):** The automation of testing and deployment.
*   **Service Principals:** Often used to authenticate the synchronization between the workspace and Git.
*   **Data Mesh:** An architectural framework where Git-supported dataflows act as versioned data products.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial AI-generated canonical documentation |