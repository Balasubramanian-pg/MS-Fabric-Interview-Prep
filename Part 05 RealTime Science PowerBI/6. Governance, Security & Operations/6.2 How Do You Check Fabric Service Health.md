# [How Do You Check Fabric Service Health](Part 05 RealTime Science PowerBI/How Do You Check Fabric Service Health.md)

Canonical documentation for [How Do You Check Fabric Service Health](Part 05 RealTime Science PowerBI/How Do You Check Fabric Service Health.md). This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of checking fabric service health is to maintain the operational integrity, availability, and reliability of a distributed system. In a fabric architecture—where numerous interconnected services, nodes, and partitions function as a single entity—health monitoring provides the necessary visibility to detect failures, trigger automated self-healing mechanisms, and inform orchestration decisions. This process addresses the problem of "silent failures" in complex systems by providing a standardized way to communicate the functional status of individual components to a centralized management layer.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Health Modeling:** The logical structure of health states and reporting.
* **Aggregation Logic:** How individual component statuses contribute to the global health of the fabric.
* **Reporting Mechanisms:** The theoretical methods by which health data is transmitted and stored.
* **Evaluation Policies:** The rules governing how health data is interpreted.

**Out of scope:**
* **Specific vendor implementations:** (e.g., specific CLI commands for Azure Service Fabric, Kubernetes, or VMware Tanzu).
* **Hardware-level diagnostics:** (e.g., specific BIOS or firmware error codes).
* **Network protocol specifics:** (e.g., the exact packet structure of a heartbeat).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Fabric** | A distributed computing topology consisting of interconnected nodes and services managed as a single pool of resources. |
| **Health State** | A discrete value representing the operational condition of an entity (typically OK, Warning, or Error). |
| **Entity** | Any identifiable component within the fabric that can report health (e.g., a node, application, service, or partition). |
| **Health Store** | A centralized or distributed repository that collects and aggregates health reports from across the fabric. |
| **Watchdog** | An external process or service that monitors other entities and submits health reports on their behalf. |
| **Liveness** | A measure of whether a process is running and responsive. |
| **Readiness** | A measure of whether a service is prepared to accept and process traffic. |
| **TTL (Time to Live)** | The duration for which a health report is considered valid before it expires. |

## Core Concepts

### Hierarchical Health Modeling
Fabric health is inherently hierarchical. A fabric is composed of applications, which are composed of services, which are composed of partitions and replicas, all running on physical or virtual nodes. The health of a parent entity is a function of the health of its children, governed by specific aggregation policies.

### State-Based Reporting
Unlike traditional logging, which records events, health checking is state-based. A health report represents a "snapshot" of a component's condition at a specific point in time. These reports are persisted in a Health Store to provide a continuous view of the system's status.

### Asynchronous Evaluation
Health checking in a fabric is typically asynchronous. Components report their status independently, and the fabric's management layer evaluates these reports periodically or upon request to determine the "effective health" of the system.

## Standard Model

The standard model for checking fabric service health follows a **Report-Store-Evaluate** workflow:

1.  **Reporting:** Entities (or Watchdogs) generate health reports. A report must contain:
    *   **Source ID:** Who is reporting.
    *   **Entity ID:** What is being reported on.
    *   **Property:** The specific aspect being monitored (e.g., "DiskSpace", "CPU", "Connectivity").
    *   **Health State:** The status (OK, Warning, Error).
    *   **TTL:** How long the report remains valid.
2.  **Storage:** The Health Store receives reports. It overwrites old reports for the same Entity/Property combination with new data. If a report's TTL expires, the store may transition the entity to an "Unknown" or "Error" state depending on policy.
3.  **Aggregation:** The fabric controller applies aggregation rules. For example, if 20% of service replicas are in an "Error" state, the parent service may be marked as "Warning." If 51% are in "Error," the service may be marked as "Error."
4.  **Querying:** Administrators or automated systems query the Health Store to retrieve the current state of an entity or the entire fabric.

## Common Patterns

### The Watchdog Pattern
An external agent (the Watchdog) monitors a service from the outside (e.g., by performing HTTP probes). This is used when the service itself cannot report health or to verify that the service is reachable over the network.

### Sidecar Reporting
A secondary process (sidecar) runs alongside the main service. The sidecar handles the logic of gathering metrics and reporting them to the Health Store, offloading this responsibility from the primary application logic.

### Differential Reporting
To reduce network traffic, entities only send health reports when their state changes or at a low-frequency "heartbeat" interval to maintain TTL.

## Anti-Patterns

### Binary Health Logic
Treating health as only "Up" or "Down." This ignores the "Warning" state, which is critical for proactive maintenance and identifying "grey failures" (where a system is running but performing poorly).

### Circular Dependencies
A health checking mechanism that depends on the very service it is monitoring to report its status. If the service fails, the health check fails to report the failure.

### Excessive Reporting Frequency
Sending health reports too frequently (e.g., every millisecond) can overwhelm the Health Store and consume significant fabric bandwidth, leading to a "self-inflicted" Denial of Service (DoS).

### Ignoring TTL
Failing to set or respect Time to Live values. Without TTL, a "Healthy" report from a crashed component might persist indefinitely in the Health Store, providing a false sense of security.

## Edge Cases

### Network Partitions (Split-Brain)
In a network partition, the Health Store may receive conflicting reports or no reports at all from a segment of the fabric. The system must decide whether to assume the missing entities are dead (Error) or simply unreachable (Unknown).

### Flapping States
An entity that rapidly oscillates between "OK" and "Error." Standard models often implement "hysteresis" or "dampening," where a state change is only accepted after it remains stable for a defined period.

### Zombie Replicas
A replica that has been decommissioned by the fabric controller but continues to send "Healthy" reports. The Health Store must be able to correlate health reports with the current intended state of the fabric.

## Related Topics
* **Distributed Consensus Algorithms:** Used to ensure the Health Store remains consistent across nodes.
* **Orchestration and Scheduling:** How the fabric uses health data to move or restart services.
* **Observability and Telemetry:** The broader field of monitoring that includes logs, metrics, and traces.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |