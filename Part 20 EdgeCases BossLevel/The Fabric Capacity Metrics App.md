# [The Fabric Capacity Metrics App](Part 20 EdgeCases BossLevel/The Fabric Capacity Metrics App.md)

Canonical documentation for [The Fabric Capacity Metrics App](Part 20 EdgeCases BossLevel/The Fabric Capacity Metrics App.md). This document defines concepts, terminology, and standard usage.

## Purpose
[The Fabric Capacity Metrics App](Part 20 EdgeCases BossLevel/The Fabric Capacity Metrics App.md) exists to provide transparency and governance over abstracted compute resources in a distributed data ecosystem. In modern data platforms, hardware is often abstracted into "Capacity," a fungible pool of processing power. Without a dedicated observability layer, organizations cannot effectively correlate workload execution with resource consumption, leading to unpredictable costs or performance degradation.

This application serves as the primary interface for monitoring the health, utilization, and efficiency of these resources. It addresses the problem of "black box" resource allocation by decomposing complex telemetry into actionable insights regarding workload attribution, peak demand management, and throughput limits.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the architectural and functional requirements of a capacity monitoring system.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Resource Attribution:** The methodology for assigning compute costs to specific workspaces, items, or users.
* **Consumption Logic:** The mathematical frameworks used to calculate resource usage (e.g., smoothing, carryover, and burstable logic).
* **Governance and Throttling:** The mechanisms by which a system signals or enforces capacity limits.
* **Performance Telemetry:** The categorization of operations into interactive or background processes.

**Out of scope:**
* **Specific Vendor Pricing:** Financial cost-per-unit values or regional pricing variations.
* **Hardware Specifications:** The underlying physical server architecture (CPU, RAM, IOPS) supporting the capacity.
* **External Monitoring Integration:** Third-party logging sinks (e.g., Log Analytics, Datadog) unless used as a direct data source for the app.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Capacity** | A dedicated pool of compute resources allocated to an organization to run various data workloads. |
| **Capacity Unit (CU)** | An abstract, standardized measure of compute power used to quantify throughput across different types of operations. |
| **Smoothing** | A computational technique that averages high-intensity, short-duration spikes over a longer window to prevent immediate throttling. |
| **Throttling** | The intentional slowing or rejection of new requests when a capacity has exhausted its allocated resources and carryover limits. |
| **Interactive Workload** | Operations triggered by a user interface that require near-instantaneous response (e.g., report rendering, SQL queries). |
| **Background Workload** | Long-running or scheduled operations that are less time-sensitive (e.g., data ingestion, scheduled refreshes, notebook execution). |
| **Carryover** | The accumulation of consumed resources that exceed the current time window's limit, to be "paid back" in subsequent windows. |
| **Burn Rate** | The velocity at which a capacity consumes its allocated units relative to its total limit. |

## Core Concepts

### Resource Abstraction
The app operates on the principle that diverse workloads (SQL, Spark, AI, BI) should be measured using a unified metric. By abstracting physical hardware into Capacity Units (CUs), the app allows for a "single pane of glass" view where a data refresh can be compared directly against a dashboard query in terms of its impact on the total resource pool.

### Temporal Distribution (Smoothing)
Unlike traditional monitoring which focuses on instantaneous peaks, capacity metrics utilize temporal distribution. This recognizes that data workloads are inherently "bursty." 
* **Interactive Smoothing:** Typically averaged over a short window (e.g., 5-10 minutes).
* **Background Smoothing:** Typically averaged over a long window (e.g., 24 hours).
This distinction ensures that a massive data load doesn't immediately crash the system, but rather consumes a "slice" of the capacity over a full day.

### The Utilization Lifecycle
The app tracks the lifecycle of a resource request:
1. **Request:** The initiation of a task.
2. **Consumption:** The actual compute effort required.
3. **Attribution:** Mapping the consumption to a specific tenant or item.
4. **Smoothing/Carryover:** Calculating how that consumption fits into the rolling window.
5. **Enforcement:** Determining if the capacity is in a healthy, over-utilized, or throttled state.

## Standard Model
The standard model for a Capacity Metrics App follows a hierarchical data aggregation strategy:

1. **Capacity Level:** The highest level of aggregation, showing total health and remaining "headroom."
2. **Workspace/Project Level:** Aggregating consumption by logical groupings to facilitate internal chargebacks.
3. **Item/Operation Level:** The granular view showing specific reports, notebooks, or pipelines that are the primary drivers of consumption.
4. **Time-Series View:** A visual representation of consumption vs. limit over time, highlighting periods of over-utilization (Carryover) and potential future throttling.

## Common Patterns

### Proactive Governance (The "Early Warning" Pattern)
Administrators monitor the "Burn Rate" and "Carryover" metrics to identify trends before they result in throttling. If the carryover is consistently increasing, it indicates that the current capacity size is insufficient for the steady-state workload.

### Chargeback/Showback
Using the attribution metrics to export usage data to financial systems. This allows organizations to distribute the cost of a single large capacity across multiple business units based on their actual CU consumption.

### Workload Optimization
Identifying "Top Consumers"—specific items that use a disproportionate amount of resources. This allows developers to target specific queries or processes for refactoring to improve overall system efficiency.

## Anti-Patterns

### Peak-Only Provisioning
Scaling capacity based on the highest instantaneous peak without accounting for smoothing. This leads to significant over-spending, as the smoothing logic is designed to handle those peaks within a smaller capacity footprint.

### Ignoring Background Carryover
Focusing only on interactive performance while ignoring a massive background carryover. This can lead to "sudden" throttling where the system stops accepting new requests because it is still "paying back" the compute debt from a background task that ran hours ago.

### Granularity Overload
Attempting to manage a capacity by looking at every individual millisecond of execution. Capacity management is a macro-level discipline; micro-optimization should be reserved for specific performance tuning of high-impact items.

## Edge Cases

### System-Level Overhead
Small amounts of capacity consumption that are not attributed to any specific user item. These are typically "heartbeat" operations or metadata management required to keep the capacity operational.

### Simultaneous Multi-Tenant Spikes
When multiple workspaces on the same capacity hit their peak demand simultaneously. While smoothing helps, the cumulative carryover can trigger "Rejection Throttling," where even small interactive requests are denied to protect system stability.

### Long-Running Operations spanning Smoothing Windows
An operation that takes longer than the smoothing window itself (e.g., a 48-hour data migration). The app must correctly distribute this consumption across all windows it touches to avoid "double-counting" or "missing" consumption.

## Related Topics
* **FinOps (Financial Operations):** The broader practice of bringing financial accountability to the variable spend model of the cloud.
* **Throttling Policies:** The specific rulesets that determine when and how a system restricts access based on metrics.
* **Workload Management (WLM):** The technical configuration of priority and resource allocation within a compute engine.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |