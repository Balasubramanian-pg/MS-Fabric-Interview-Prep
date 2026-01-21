# Can You Trigger A Pipeline From Another Pipeline

Canonical documentation for Can You Trigger A Pipeline From Another Pipeline. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of cross-pipeline triggering is to enable modularity, separation of concerns, and complex workflow orchestration within automated systems. By allowing one pipeline to initiate the execution of another, organizations can decouple build processes from deployment processes, manage microservices independently, and create reusable automation components. This addresses the problem of "monolithic pipelines," which are difficult to maintain, debug, and scale.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core logic of upstream and downstream relationships.
* Data passing and state inheritance between pipelines.
* Execution flow models (Synchronous vs. Asynchronous).
* Dependency management and orchestration theory.

**Out of scope:**
* Specific vendor implementations (e.g., GitHub Actions, GitLab CI/CD, Jenkins, Azure DevOps).
* Specific scripting languages or syntax.
* Infrastructure-as-Code (IaC) provisioning, unless triggered via pipeline.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Upstream Pipeline** | The initiating pipeline that generates a trigger event. |
| **Downstream Pipeline** | The target pipeline that is executed in response to a trigger from an upstream source. |
| **Trigger** | The mechanism or event (API call, webhook, or native platform hook) that signals a pipeline to start. |
| **Payload** | The set of variables, metadata, or artifacts passed from the upstream pipeline to the downstream pipeline. |
| **Synchronous Execution** | A mode where the upstream pipeline pauses and waits for the downstream pipeline to complete before proceeding. |
| **Asynchronous Execution** | A "fire-and-forget" mode where the upstream pipeline continues its execution immediately after triggering the downstream pipeline. |
| **Fan-out** | A pattern where a single upstream pipeline triggers multiple downstream pipelines simultaneously. |
| **Fan-in** | A pattern where multiple upstream pipelines must complete or signal a single downstream pipeline to begin. |

## Core Concepts
The fundamental idea behind cross-pipeline triggering is the **Event-Driven Architecture (EDA)** applied to automation. Instead of a single linear sequence, automation is broken into discrete units of work.

1.  **Decoupling:** Pipelines should be functional units. For example, a "Build" pipeline should not necessarily need to know the internal logic of a "Production Deployment" pipeline.
2.  **Encapsulation:** Each pipeline manages its own environment, secrets, and logic. The trigger acts as a controlled interface between these environments.
3.  **Observability:** Cross-pipeline triggers require a centralized or linked logging mechanism to trace the execution path from the initial upstream event to the final downstream outcome.

## Standard Model
The standard model for cross-pipeline triggering involves a **Caller-Callee** relationship. 

1.  **Initiation:** The upstream pipeline reaches a specific stage or status (usually "Success").
2.  **Handshake:** The upstream pipeline sends a request to the orchestrator's API or internal bus. This request includes the target pipeline ID and any required parameters (Payload).
3.  **Validation:** The orchestrator validates permissions (does the upstream have the right to trigger the downstream?) and input parameters.
4.  **Execution:** The downstream pipeline is queued or executed.
5.  **Feedback Loop (Optional):** In synchronous models, the downstream pipeline reports its final status back to the upstream pipeline to determine the next steps in the upstream flow.

## Common Patterns
*   **The CI/CD Split:** A Continuous Integration (CI) pipeline builds an artifact and, upon success, triggers a Continuous Deployment (CD) pipeline to move that artifact through environments.
*   **The Orchestrator Pattern:** A "Parent" pipeline acts as a controller, triggering various "Child" pipelines in a specific order based on logic or environment availability.
*   **Microservice Propagation:** A change in a shared library pipeline triggers the rebuild/re-test pipelines of all dependent microservices.
*   **Scheduled Batching:** A scheduled pipeline runs and, based on the data found, triggers specific processing pipelines for different data subsets.

## Anti-Patterns
*   **Circular Dependencies:** Pipeline A triggers Pipeline B, which eventually triggers Pipeline A, creating an infinite loop.
*   **The "God" Pipeline Trigger:** An upstream pipeline passing an excessive amount of logic/code to a downstream pipeline, breaking encapsulation.
*   **Deep Nesting:** Triggering pipelines more than 3-4 levels deep (A -> B -> C -> D -> E), which makes debugging and failure recovery extremely difficult.
*   **Implicit Dependencies:** Triggering a downstream pipeline without passing the specific version or artifact ID, leading to the downstream pipeline using "latest" and causing non-deterministic results.

## Edge Cases
*   **Race Conditions:** Two upstream pipelines trigger the same downstream pipeline simultaneously, leading to resource contention or overwritten deployments.
*   **Downstream Failure Handling:** If a downstream pipeline fails in an asynchronous model, the upstream pipeline may report "Success" even though the overall process failed.
*   **Trigger Throttling:** High-frequency upstream events may exceed the orchestrator's capacity to queue or execute downstream pipelines.
*   **Security Context Switching:** The upstream pipeline may have a higher or lower privilege level than the downstream pipeline, requiring careful management of identity and access (IAM).

## Related Topics
*   **Pipeline Orchestration:** The broader management of multiple related pipelines.
*   **Artifact Repositories:** Often used as the "bridge" between pipelines (Upstream pushes, Downstream pulls).
*   **Webhooks:** The common technical protocol used for cross-system triggering.
*   **Event-Driven Architecture:** The architectural style that informs pipeline triggering.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |