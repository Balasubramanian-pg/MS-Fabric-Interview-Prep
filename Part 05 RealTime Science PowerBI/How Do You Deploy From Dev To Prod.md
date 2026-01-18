# [How Do You Deploy From Dev To Prod](Part 05 RealTime Science PowerBI/How Do You Deploy From Dev To Prod.md)

Canonical documentation for [How Do You Deploy From Dev To Prod](Part 05 RealTime Science PowerBI/How Do You Deploy From Dev To Prod.md). This document defines concepts, terminology, and standard usage.

## Purpose
The transition of software from a development state to a production state addresses the fundamental need for **risk mitigation** and **predictable delivery**. In software engineering, the "Dev to Prod" lifecycle exists to ensure that code changes are validated, integrated, and released without compromising the stability, security, or performance of the live environment. It provides a structured framework for moving intellectual property from a volatile, experimental state to a hardened, value-generating state.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative. It focuses on the logical progression of software rather than specific tooling or cloud providers.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The logical progression of code through distinct environments.
* Validation mechanisms and quality gates.
* Deployment strategies and release patterns.
* Principles of artifact management and environment parity.

**Out of scope:**
* Specific vendor implementations (e.g., AWS CodePipeline, GitHub Actions, Jenkins).
* Programming language-specific build instructions.
* Infrastructure provisioning (IaC) details, except where they intersect with deployment logic.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Artifact** | A discrete, versioned, and immutable package of software (e.g., a container image, binary, or ZIP) ready for deployment. |
| **Environment** | A logical collection of infrastructure and configuration where a specific version of an application resides. |
| **Promotion** | The act of advancing a validated artifact from a lower-level environment (e.g., Staging) to a higher-level environment (e.g., Production). |
| **Pipeline** | The automated sequence of steps (build, test, deploy) that facilitates the movement of code from source to production. |
| **Environment Parity** | The practice of keeping development, staging, and production environments as similar as possible to minimize "it works on my machine" discrepancies. |
| **Quality Gate** | A set of predefined criteria (automated or manual) that must be met before an artifact can be promoted. |
| **Rollback** | The process of reverting the production environment to a previous known-good state in the event of a failure. |

## Core Concepts

### 1. Immutability
Once an artifact is built and versioned, it must never be modified. Any change to the code requires the creation of a new artifact. This ensures that the exact code tested in a lower environment is what eventually runs in production.

### 2. Separation of Concerns
The deployment process distinguishes between the **Build** (creating the artifact), the **Release** (combining the artifact with environment-specific configuration), and the **Deployment** (placing the release into an environment).

### 3. Environment Hierarchy
Environments are organized by increasing levels of strictness and stability:
*   **Development (Dev):** Volatile, used for active feature creation.
*   **Testing/QA:** Used for functional and integration testing.
*   **Staging/Pre-Prod:** A mirror of production used for final validation.
*   **Production (Prod):** The live environment serving end-users.

### 4. Configuration Management
Code and configuration must be decoupled. The same artifact should be deployable to any environment, with environment-specific variables (database strings, API keys) injected at runtime.

## Standard Model

The standard model for deploying from Dev to Prod follows a linear progression through a delivery pipeline:

1.  **Commit/Push:** Code is merged into a shared repository.
2.  **Continuous Integration (CI):** The system triggers an automated build, runs unit tests, and generates a versioned artifact.
3.  **Deployment to Lower Environments:** The artifact is deployed to Dev or QA environments for automated integration and smoke testing.
4.  **Promotion to Staging:** Upon passing lower-level gates, the artifact is deployed to an environment that replicates Production. Here, User Acceptance Testing (UAT) and performance testing occur.
5.  **Production Release:** The artifact is promoted to the live environment using a controlled deployment strategy.
6.  **Post-Deployment Validation:** The system is monitored for regressions or performance degradation.

## Common Patterns

### Blue/Green Deployment
Two identical production environments exist. "Blue" is live, while "Green" receives the new deployment. Once Green is validated, traffic is switched from Blue to Green. This allows for near-zero downtime and instant rollbacks.

### Canary Releases
The new version is deployed to a small subset of infrastructure or users. If no errors are detected, the rollout expands incrementally until it covers 100% of the fleet.

### Rolling Updates
Instances in a cluster are updated sequentially. The system takes one instance offline, updates it, brings it back online, and moves to the next. This ensures capacity is maintained during the update.

### Feature Toggles (Dark Launching)
Code is deployed to production but remains dormant behind a configuration switch. This decouples the **technical deployment** from the **business release**.

## Anti-Patterns

*   **Manual Production Changes:** Modifying code or configuration directly on a production server ("Cowboy Coding"). This leads to configuration drift and non-reproducible states.
*   **Environment Divergence:** Allowing the Staging environment to differ significantly from Production (e.g., different OS versions or database engines), leading to "false positives" during testing.
*   **Building Multiple Times:** Re-compiling code for each environment. This introduces the risk that the code running in Prod is not identical to the code tested in QA.
*   **Lack of Automated Rollbacks:** Relying on manual intervention to fix a failed deployment, which increases Mean Time to Recovery (MTTR).

## Edge Cases

### Emergency Hotfixes
When a critical bug exists in Production, the standard pipeline may be too slow. An "Express Lane" or Hotfix process is required, where the fix is applied to the current production version and back-ported to Dev, while still undergoing minimal essential testing.

### Database Schema Migrations
Unlike stateless code, data has state. Deploying changes that involve database schema updates requires careful coordination (e.g., "Expand and Contract" pattern) to ensure the application remains compatible with the database during the transition.

### Large-Scale State Synchronicity
In distributed systems, deploying a change that requires all nodes to be updated simultaneously (Atomic Deployment) is an edge case that often requires a "maintenance window" or global lock, which contradicts modern high-availability goals.

## Related Topics
*   **Continuous Integration / Continuous Deployment (CI/CD):** The automation frameworks supporting this process.
*   **Observability:** Monitoring and logging required to validate a deployment.
*   **Infrastructure as Code (IaC):** Managing the environments themselves via versioned files.
*   **Site Reliability Engineering (SRE):** The discipline of maintaining production stability.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |