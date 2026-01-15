# Gated Deployments

Canonical documentation for Gated Deployments. This document defines concepts, terminology, and standard usage.

## Purpose
Gated Deployments exist to manage risk and ensure quality within the software delivery lifecycle (SDLC). They provide a structured mechanism to halt or permit the progression of software artifacts between distinct environments or stages based on predefined criteria. The primary objective is to prevent the introduction of defects, security vulnerabilities, or performance regressions into sensitive environments (such as Production) by enforcing a "checkpoint" architecture.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Logical frameworks for deployment authorization.
* Criteria-based promotion strategies.
* Governance and compliance enforcement within delivery pipelines.
* Interaction between automated and manual verification.

**Out of scope:**
* Specific vendor implementations (e.g., GitHub Environments, Azure DevOps Gates, GitLab Protected Environments).
* Infrastructure-as-Code (IaC) syntax.
* Specific testing frameworks or languages.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Gate** | A logical checkpoint that evaluates specific criteria before allowing a deployment to proceed. |
| **Promotion** | The act of moving a validated artifact or configuration from a lower-order environment to a higher-order environment. |
| **Approval** | A manual or automated signal indicating that a gate's requirements have been satisfied. |
| **Pre-deployment Gate** | A check performed before an artifact is deployed to the target environment. |
| **Post-deployment Gate** | A check performed after deployment but before the release is considered "complete" or "successful." |
| **Environment** | A logical grouping of infrastructure where a specific version of an application is hosted (e.g., Staging, UAT, Production). |
| **Drift** | The divergence between the state of the environment and the intended state defined in the deployment manifest. |

## Core Concepts

### 1. Deterministic vs. Non-Deterministic Gates
*   **Deterministic Gates:** Rely on binary outcomes (e.g., "Did the unit tests pass?"). These are easily automated and provide consistent results.
*   **Non-Deterministic Gates:** Rely on qualitative assessment or external signals (e.g., "Does the UI feel responsive to the stakeholder?"). These often require manual intervention.

### 2. State Transition
A gated deployment represents a state transition in the lifecycle of an artifact. The artifact moves from a "Pending" or "Staged" state to an "Active" state only when the gate evaluates to `TRUE`.

### 3. Evidence-Based Promotion
The principle that no artifact should move forward without associated metadata (evidence) proving it has met the requirements of the current gate. This evidence forms the basis of an audit trail.

## Standard Model
The standard model for a Gated Deployment follows a linear or branching sequence of evaluation:

1.  **Trigger:** A deployment request is initiated (manually or via CI/CD).
2.  **Pre-Evaluation:** The system checks static requirements (e.g., "Is the deployment window open?").
3.  **External Signal Integration:** The gate queries external systems (e.g., Monitoring tools, Security scanners, Change Management Databases).
4.  **Decision Logic:**
    *   **Pass:** The deployment proceeds to the target environment.
    *   **Fail:** The deployment is aborted; notifications are sent.
    *   **Hold:** The deployment waits for a manual override or a timeout period.
5.  **Post-Evaluation:** After the bits are moved, the gate monitors health metrics to decide whether to keep the deployment or trigger an automated rollback.

## Common Patterns

### Manual Approval Gate
Requires a human actor with specific permissions to review the change and provide a digital signature or "Approve" action. This is common in highly regulated industries.

### Automated Quality Gate
A gate that automatically queries a test suite or security scanner. If the "Pass" rate is below a certain threshold (e.g., 100% of critical tests), the gate remains closed.

### Schedule-Based Gate (Deployment Windows)
A gate that only opens during specific timeframes to minimize business impact during peak hours or to ensure support staff are available.

### Health-Signal Gate (Canary/Blue-Green)
A post-deployment gate that monitors error rates or latency. If metrics exceed a defined threshold within the first $N$ minutes, the gate fails and triggers a rollback.

## Anti-Patterns

*   **The "Rubber Stamp":** Approvers granting permission without reviewing the evidence, rendering the gate performative rather than functional.
*   **Long-Lived Gates:** Leaving a deployment in a "Pending Approval" state for days, leading to configuration drift and "stale" artifacts.
*   **Over-Gating:** Implementing so many gates that the "Lead Time to Change" becomes prohibitive, encouraging teams to bypass the process.
*   **Silent Failures:** Gates that fail but do not provide clear diagnostic information as to *why* the criteria were not met.

## Edge Cases

*   **Emergency Overrides (Break-Glass):** Scenarios where a critical production fix must bypass standard gates. This requires a secondary audit process to justify the bypass post-facto.
*   **Flaky Signals:** When an automated gate relies on a non-deterministic test (e.g., an unstable integration test). This can lead to "Gate Fatigue" where users ignore legitimate failures.
*   **Dependency Deadlocks:** When Gate A requires Service B to be updated, but Service B's gate requires Service A to be updated first.
*   **Partial Success:** In multi-node deployments, a gate may face an ambiguous state where 50% of nodes succeeded and 50% failed.

## Related Topics
*   **Continuous Delivery (CD):** The broader practice of automating the release process.
*   **Observability:** The telemetry required to inform automated gates.
*   **Compliance as Code:** The practice of codifying gate requirements into the pipeline.
*   **Rollback Strategies:** The actions taken when a post-deployment gate fails.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial AI-generated canonical documentation |