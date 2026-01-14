# What Is The Default Spark Version In Fabric

Canonical documentation for What Is The Default Spark Version In Fabric. This document defines concepts, terminology, and standard usage regarding the baseline Apache Spark environment within the Microsoft Fabric ecosystem.

## Purpose
The concept of a "Default Spark Version" exists to provide a predictable, stable, and optimized execution environment for distributed computing tasks. In a Software-as-a-Service (SaaS) analytics platform, the default version ensures that users can execute notebooks, jobs, and pipelines immediately without manual infrastructure configuration. It addresses the need for a balanced lifecycle between cutting-edge features and enterprise-grade stability.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the architectural logic of Spark versioning within Fabric.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The definition of "Runtime" as a container for Spark versions.
* The logic governing how a default version is selected and updated.
* The relationship between Spark versions and associated libraries (Delta Lake, Python, R).
* Governance of version lifecycles (GA vs. Preview).

**Out of scope:**
* Step-by-step UI tutorials for changing versions.
* Specific performance benchmarks of one version versus another.
* Third-party library installation guides.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| Runtime | A curated stack of components including Apache Spark, Java, Scala, Python, R, and specialized libraries (e.g., Delta Lake) that function as a single unit. |
| Default Version | The specific Runtime version automatically assigned to a Workspace or Environment if no explicit override is defined by the user. |
| GA (General Availability) | A stable, production-ready version of a Runtime that is officially supported for mission-critical workloads. |
| Preview | A version of a Runtime made available for testing new features; it is not yet considered the stable default for production. |
| Workspace Setting | A configuration layer that allows administrators to deviate from the capacity-level default Spark version. |

## Core Concepts

### The Runtime Bundle
In Fabric, Spark is not managed as an isolated component. Instead, it is part of a **Fabric Runtime**. The default Spark version is inextricably linked to specific versions of:
* **Operating System/Base Image:** The underlying container environment.
* **Apache Spark:** The core engine (e.g., 3.4, 3.5).
* **Delta Lake:** The storage layer protocol.
* **Language Providers:** Specific versions of Python (PySpark), R (SparkR), and Scala.

### Version Inheritance
The default version follows a hierarchical inheritance model:
1. **Capacity Level:** The broadest default set by the tenant or capacity administrator.
2. **Workspace Level:** Overrides the capacity default for all items within a specific workspace.
3. **Environment/Item Level:** Overrides both workspace and capacity defaults for specific notebooks or Spark job definitions.

### Lifecycle Management
Default versions are not static. They transition through a lifecycle:
* **Introduction:** New versions are introduced as "Preview."
* **Promotion:** Once stabilized, a version is promoted to "GA."
* **Defaulting:** A specific GA version is designated as the "Default" for new workspaces.
* **Deprecation:** Older versions are marked for retirement and eventually removed.

## Standard Model
The standard model for Spark versions in Fabric relies on the **Current GA Runtime**. 

As of the current architectural standard, Fabric typically maintains one "Default" version that represents the most stable integration of Spark 3.x. When a new major Spark version (e.g., moving from Spark 3.4 to 3.5) is integrated into a Fabric Runtime, it undergoes a soak period in Preview before becoming the new Default.

The system is designed to ensure that:
* **New Workspaces** receive the latest stable Default.
* **Existing Workspaces** retain their selected version to prevent breaking changes, unless the version reaches End-of-Life (EOL).

## Common Patterns

### The "Latest Stable" Pattern
Most organizations utilize the system-provided default to minimize administrative overhead. This ensures that the underlying infrastructure is automatically patched and optimized by the platform provider.

### The "Pinned Version" Pattern
For production workloads requiring strict idempotency, users create an **Environment** to "pin" a specific Spark version. This prevents the workload from automatically shifting when the platform updates the global default.

### The "Preview Testing" Pattern
Developers often create a parallel environment using a Preview Spark version to test compatibility and performance improvements before the version is promoted to Default status.

## Anti-Patterns

### Hardcoding Version-Specific Logic
Writing code that relies on internal Spark APIs or specific sub-version behaviors that may change during a minor runtime update. This leads to "brittle" pipelines.

### Ignoring Deprecation Notices
Failing to migrate workloads from a deprecated Spark version to the current default before the EOL date, which can lead to forced upgrades and potential runtime failures.

### Over-customization of Environments
Creating unique Spark environments for every individual notebook. This increases cold-start times and complicates governance compared to using the workspace default.

## Edge Cases

### Library Conflicts
A default Spark version may include a pre-installed library (e.g., Pandas or NumPy) that conflicts with a user-required version. In these cases, the "Default" environment must be overridden by a custom Environment definition.

### Cross-Workspace Dependencies
When a notebook in Workspace A (using Default Spark X) calls a job in Workspace B (using Default Spark Y), data serialization issues may occur if the Spark versions or Delta Lake protocols are significantly different.

### Regional Rollouts
Because Fabric is a global SaaS, the "Default" version may vary slightly across different geographical regions during a rollout window (the "Staged Deployment" effect).

## Related Topics
* **Fabric Environments:** The mechanism for customizing Spark settings.
* **Delta Lake Protocol:** The storage format versioning that often moves in lockstep with Spark versions.
* **V-Order:** A Fabric-specific optimization for Parquet files used within the Spark runtimes.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2025-05-22 | Initial canonical documentation defining the default Spark versioning logic in Fabric. |