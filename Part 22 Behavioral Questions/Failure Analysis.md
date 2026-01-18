# [Failure Analysis](Part 22 Behavioral Questions/Failure Analysis.md)

Canonical documentation for [Failure Analysis](Part 22 Behavioral Questions/Failure Analysis.md). This document defines concepts, terminology, and standard usage.

## Purpose
[Failure Analysis](Part 22 Behavioral Questions/Failure Analysis.md) (FA) is the systematic investigation of a system, product, or process that has ceased to perform its intended function. The primary objective of [Failure Analysis](Part 22 Behavioral Questions/Failure Analysis.md) is to determine the root cause of a failure to prevent recurrence, improve reliability, and mitigate risk. It shifts the focus from reactive "fixing" to proactive systemic improvement by identifying the physical, human, or latent conditions that led to the non-conformance.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   **Methodologies:** Logical frameworks for identifying causality.
*   **Causal Taxonomy:** Classification of failure types and origins.
*   **Lifecycle of Analysis:** The stages from detection to verification of resolution.
*   **Systemic Theory:** The interaction between components, environments, and human actors.

**Out of scope:**
*   **Specific vendor implementations:** Proprietary software tools or specific laboratory hardware.
*   **Industry-specific regulations:** While FA is used in aerospace (AS9100) or medical (ISO 13485), this document focuses on the universal principles.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Failure** | The termination of the ability of an item to perform a required function. |
| **Fault** | An abnormal condition or defect at the component level that may lead to a failure. |
| **Root Cause** | The fundamental, underlying reason for a failure which, if addressed, prevents recurrence. |
| **Proximate Cause** | The immediate event or condition that directly resulted in the failure. |
| **Latent Condition** | Dormant vulnerabilities within a system (e.g., poor design, inadequate training) that combine with active triggers to cause failure. |
| **Failure Mode** | The specific manner or way by which a failure is observed (the "how"). |
| **Failure Mechanism** | The physical, chemical, or logical process that leads to a failure (the "why" at a technical level). |
| **Containment** | Temporary actions taken to prevent the effects of a failure from spreading or affecting customers while analysis is ongoing. |

## Core Concepts

### The Chain of Causality
Failure is rarely the result of a single isolated event. It is typically a sequence of events where a trigger interacts with existing vulnerabilities. Analysis must distinguish between the **Trigger** (the spark), the **Mechanism** (the fire), and the **Root Cause** (the presence of flammable material in an unsafe area).

### The Swiss Cheese Model
Proposed by James Reason, this concept suggests that systems have multiple layers of defense (slices of cheese). Failures occur when the "holes" (weaknesses) in every layer align, allowing a hazard to pass through all defenses. FA seeks to identify where these holes exist and why they aligned.

### Determinism vs. Probabilistic Failure
*   **Deterministic:** Failures that occur every time a specific set of conditions is met (e.g., a software bug in a specific logic path).
*   **Probabilistic:** Failures that occur based on statistical likelihood, often influenced by environmental stress or wear-out phases (e.g., hardware component degradation).

### Human Factors
FA recognizes that "human error" is rarely a root cause but rather a symptom of deeper systemic issues, such as poor interface design, fatigue, or inadequate procedural documentation.

## Standard Model
The generally accepted workflow for [Failure Analysis](Part 22 Behavioral Questions/Failure Analysis.md) follows a non-linear but structured lifecycle:

1.  **Detection and Characterization:** Identifying that a failure has occurred and documenting the state of the system at the time of failure.
2.  **Containment:** Implementing "stop-gap" measures to limit the blast radius or impact.
3.  **Evidence Preservation:** Collecting logs, physical debris, telemetry, or state dumps before they are altered by recovery attempts.
4.  **Hypothesis Generation:** Using deductive or inductive reasoning to list potential causes.
5.  **Testing and Simulation:** Attempting to replicate the failure mode under controlled conditions to validate hypotheses.
6.  **Root Cause Identification:** Narrowing down the variables to the fundamental systemic flaw.
7.  **Remediation and Prevention:** Designing and implementing a permanent fix.
8.  **Verification:** Monitoring the system to ensure the failure mode does not recur and that the fix has not introduced new faults.

## Common Patterns

### 5 Whys
An iterative interrogative technique used to explore the cause-and-effect relationships underlying a particular problem. By repeating the question "Why?", each answer forms the basis of the next question until the root cause is reached.

### Fishbone (Ishikawa) Diagram
A visualization tool used to categorize the potential causes of a failure into standard categories such as Methods, Machines, People, Materials, Measurement, and Environment.

### Fault Tree Analysis (FTA)
A top-down, deductive failure analysis in which an undesired state of a system is analyzed using Boolean logic to combine a series of lower-level events.

### Failure Mode and Effects Analysis (FMEA)
A proactive, bottom-up approach used to identify all possible points of failure in a design or process and evaluate the relative impact of those failures.

## Anti-Patterns

### The Single Cause Fallacy
The mistaken belief that a complex system failure has only one cause. This leads to "stop-gap" fixes that ignore contributing latent conditions.

### Blame Culture
Focusing on *who* failed rather than *what* failed. This discourages honest reporting and hides the systemic issues that allowed the individual to make a mistake.

### Confirmation Bias
The tendency to search for, interpret, and favor information that confirms one's pre-existing hypothesis about the failure, while ignoring contradictory evidence.

### Stopping at the Proximate Cause
Concluding an investigation once the immediate trigger is found (e.g., "The server ran out of memory") without asking why the system was not resilient to that condition.

## Edge Cases

### Intermittent (Transient) Failures
Failures that appear and disappear without an obvious pattern (often called "Heisenbugs" in software). These are difficult to analyze because they are hard to replicate and may be sensitive to environmental variables like temperature or race conditions.

### Cascading Failures
A failure in one component that triggers failures in successive components. In these cases, the "Root Cause" may be located in a seemingly unrelated subsystem that was the first domino to fall.

### Normal Accidents (Systemic Complexity)
In highly complex and tightly coupled systems, some failures are inevitable and cannot be attributed to a specific component or human error, but rather to the unpredictable interaction of system variables.

## Related Topics
*   **Reliability Engineering:** The broader discipline of ensuring systems function correctly over time.
*   **Observability and Telemetry:** The infrastructure required to provide the data necessary for FA.
*   **Quality Assurance (QA):** The process of preventing failures before they reach production.
*   **Incident Management:** The operational framework for responding to failures in real-time.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |