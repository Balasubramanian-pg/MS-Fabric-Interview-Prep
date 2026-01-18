# [The Migration Dilemma](Part 21 Scenarios BossLevel/The Migration Dilemma.md)

Canonical documentation for [The Migration Dilemma](Part 21 Scenarios BossLevel/The Migration Dilemma.md). This document defines concepts, terminology, and standard usage.

## Purpose
[The Migration Dilemma](Part 21 Scenarios BossLevel/The Migration Dilemma.md) describes the systemic friction and decision-making paradoxes encountered when transitioning a system, dataset, or architecture from a legacy state to a target state. It addresses the fundamental tension between the necessity of evolution (to reduce technical debt or increase capability) and the inherent risks of transition (operational instability, data loss, and resource exhaustion).

This topic exists to provide a framework for understanding why migrations are non-trivial and why the "optimal" technical path often conflicts with business continuity and risk tolerance.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Theoretical frameworks for evaluating migration risk.
* Structural patterns for state transition.
* The relationship between data gravity and architectural mobility.
* Socio-technical impacts of system replacement.

**Out of scope:**
* Specific vendor implementations or cloud provider migration tools.
* Step-by-step tutorials for specific software upgrades.
* Programming language-specific refactoring guides.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Legacy State** | The existing environment, system, or data structure currently providing value but slated for replacement or modification. |
| **Target State** | The desired future environment or architecture intended to resolve the limitations of the Legacy State. |
| **Parity** | The requirement that the Target State must match the functional and non-functional outputs of the Legacy State before decommissioning. |
| **Data Gravity** | The concept that as data sets grow, they become increasingly difficult to move, attracting applications and services toward them. |
| **Cutover** | The discrete point in time when the primary authority for a workload shifts from the Legacy State to the Target State. |
| **Dual-Write** | A transitional state where data is simultaneously written to both Legacy and Target systems to ensure synchronization. |
| **Migration Debt** | The accumulated technical and operational cost incurred by maintaining two systems during a prolonged transition. |

## Core Concepts

### The Cost of Inaction vs. The Cost of Transition
[The Migration Dilemma](Part 21 Scenarios BossLevel/The Migration Dilemma.md) is rooted in the comparison of two risks:
1. **Stagnation Risk:** The cost of remaining in the Legacy State (increasing maintenance costs, security vulnerabilities, inability to scale).
2. **Transition Risk:** The cost of moving to the Target State (potential downtime, data corruption, unforeseen architectural incompatibilities).

### The J-Curve of Migration
Most migrations experience a temporary decline in performance, velocity, or stability immediately following the start of the transition. This "J-Curve" represents the period where resources are split between two environments, and the team has not yet realized the efficiencies of the Target State.

### The Parity Paradox
The requirement for 100% functional parity often prevents migration success. Because the Legacy State often contains undocumented "features" or bug-dependent behaviors, attempting to replicate them perfectly in the Target State can lead to over-engineering or the replication of flawed logic.

## Standard Model
The standard model for resolving the Migration Dilemma follows a four-phase lifecycle:

1.  **Assessment & Discovery:** Quantifying the dependencies and data gravity of the Legacy State.
2.  **Bridge Construction:** Establishing the mechanisms for data synchronization or traffic routing between states (e.g., APIs, replication streams).
3.  **Incremental Transition:** Moving workloads or data segments in controlled batches to validate the Target State under production pressure.
4.  **Decommissioning:** The formal removal of the Legacy State once the Target State has achieved "Stable Authority."

## Common Patterns

### The Strangler Fig Pattern
The gradual replacement of system components by placing a new system "around" the edges of the old one. Over time, the new system grows to encompass all functionality, and the old system is "strangled" and removed.

### Parallel Run
Operating both the Legacy and Target states simultaneously for a set period. Outputs are compared for consistency, but the Legacy State remains the system of record until confidence is established.

### Blue-Green Migration
A method of cutover where two identical environments exist. Traffic is routed to the "Blue" (Legacy) environment while the "Green" (Target) is prepared. A single switch redirects all traffic to Green.

## Anti-Patterns

### The "Big Bang" Migration
Attempting to migrate the entire system and all data in a single, irreversible event. This maximizes risk and provides no fallback mechanism if the Target State fails.

### The Perpetual Bridge
Creating synchronization layers between the Legacy and Target states but never actually performing the cutover. This results in "Migration Debt," where the complexity of maintaining the bridge exceeds the value of the migration itself.

### Feature Creep during Migration
Attempting to add significant new functionality to the Target State while simultaneously trying to achieve parity with the Legacy State. This conflates "Migration" with "Product Development," leading to moving targets and timeline inflation.

## Edge Cases

### Irreversible State Changes
Scenarios where the act of migrating data alters it in a way that it cannot be moved back to the Legacy State (e.g., destructive schema changes). This removes the "Rollback" option, turning the migration into a "Point of No Return" event.

### Migrating During Active Outage
Performing a migration as a reactive measure to a failure in the Legacy State. This bypasses standard validation phases and significantly increases the probability of data loss or architectural misalignment.

### Ghost Dependencies
Dependencies that are not visible during the Assessment phase (e.g., a third-party service scraping a legacy UI) which only reveal themselves after the Legacy State is decommissioned.

## Related Topics
* **Technical Debt Management:** The broader context of why migrations become necessary.
* **Data Integrity and Consistency:** The theoretical requirements for moving stateful information.
* **Change Management:** The human and organizational aspect of transitioning between systems.
* **Disaster Recovery:** Patterns used in migrations often overlap with failover and recovery strategies.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |