# What Is Burstable Compute In Warehouse

Canonical documentation for What Is Burstable Compute In Warehouse. This document defines concepts, terminology, and standard usage.

## Purpose
The primary purpose of burstable compute in a data warehouse environment is to reconcile the discrepancy between static resource provisioning and the highly variable nature of analytical workloads. 

In traditional compute models, resources are provisioned for peak load, leading to significant idle-time waste, or for average load, leading to performance degradation during spikes. Burstable compute addresses this by providing a baseline level of performance with the ability to temporarily "burst" to a higher performance tier when demand increases. This model optimizes for cost-efficiency while maintaining high availability and responsiveness for unpredictable or periodic data processing tasks.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core functionality of credit-based and pool-based compute allocation.
* Theoretical boundaries of resource elasticity in a warehouse context.
* The relationship between baseline performance and peak performance.
* Economic and operational trade-offs of burstable architectures.

**Out of scope:**
* Specific vendor implementations (e.g., AWS EC2 T-series, Snowflake Virtual Warehouses, Azure Synapse scaling).
* Pricing models or specific currency-based cost analysis.
* Hardware-level CPU instruction sets or physical infrastructure details.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Baseline Capacity** | The guaranteed level of compute resources (CPU, RAM, I/O) available to a workload at all times. |
| **Burst Capacity** | The maximum allowable resource limit a workload can reach beyond its baseline for a limited duration. |
| **Compute Credits** | A conceptual or literal unit representing the accumulation of unused baseline capacity, later spent to enable bursting. |
| **Throttling** | The process of restricting a workload back to its baseline capacity once burst limits or credits are exhausted. |
| **Provisioned Capacity** | The total amount of resources explicitly reserved for a specific warehouse instance. |
| **Workload Skew** | A scenario where resource demand is non-uniformly distributed over time, necessitating burst capabilities. |

## Core Concepts

### Resource Elasticity
Burstable compute is a subset of resource elasticity. Unlike "auto-scaling," which may involve provisioning entirely new nodes or clusters, bursting typically refers to the immediate expansion of capabilities within an existing logical boundary.

### The Credit Mechanism
Most burstable systems operate on a "earn-and-spend" logic. When a warehouse is operating below its baseline capacity, it accumulates credits. When demand exceeds the baseline, these credits are consumed to maintain high performance. If the credit balance reaches zero, the system reverts to baseline performance.

### Temporal Allocation
Burstable compute shifts the focus from *how much* compute is available to *when* that compute is utilized. It assumes that the integral of compute demand over a 24-hour period is lower than the total baseline capacity provided, even if specific moments exceed that baseline.

## Standard Model
The standard model for burstable compute in a warehouse involves three distinct states:

1.  **Idle/Accumulation State:** The workload is minimal. The system consumes fewer resources than the baseline, accruing "burst debt" or "burst credits."
2.  **Burst State:** A complex query or ETL (Extract, Transform, Load) job arrives. The system exceeds the baseline, utilizing the maximum burst capacity to minimize latency.
3.  **Recovery/Throttled State:** If the burst state persists longer than the accrued credits allow, the system enforces a performance ceiling (the baseline) to protect the underlying shared infrastructure and maintain predictable cost structures.

## Common Patterns

### Periodic Ingestion
Data warehouses often receive large batches of data at specific intervals (e.g., hourly or nightly). Burstable compute allows the warehouse to process these spikes rapidly without requiring the user to pay for high-performance compute during the intervening idle periods.

### Ad-hoc Analytical Discovery
Data scientists and analysts often run unpredictable, complex queries. Burstable compute ensures that these "exploratory" queries do not hang or fail due to resource constraints, provided the warehouse has had sufficient idle time to recover.

### End-of-Cycle Reporting
Weekly or monthly financial reconciliations create massive, temporary demand. Burstable models accommodate these cycles by leveraging the credits earned during the lower-activity periods of the month.

## Anti-Patterns

### Sustained High Utilization
Using a burstable compute model for a workload that consistently runs at 80-100% capacity is an anti-pattern. Because the system cannot accumulate credits, it will be perpetually throttled to the baseline, resulting in poor performance compared to a fixed-capacity model.

### Under-Provisioned Baseline
Setting a baseline too low in an attempt to save costs can lead to "credit starvation." In this scenario, the system never earns enough credits to handle even minor spikes, leading to inconsistent user experiences.

### Ignoring Throttling Latency
Treating burstable compute as "infinite" compute is a mistake. Architects must account for the performance drop-off that occurs when credits are exhausted, which can cause cascading failures in time-sensitive data pipelines.

## Edge Cases

### Cold Starts and Initial Credit Balances
When a burstable warehouse is first provisioned, it may start with a "launch bonus" of credits or zero credits. This affects the system's ability to handle an immediate heavy workload upon deployment.

### Multi-Tenant Contention
In shared cloud environments, the "burst" capacity may be subject to the physical availability of resources on the underlying host. If multiple tenants attempt to burst simultaneously, the actual realized performance may be lower than the theoretical burst maximum.

### Credit Expiration
Some models implement a "use it or lose it" policy where credits expire after a certain period (e.g., 24 hours). This prevents users from hoarding credits for massive, unsustainable bursts that could destabilize the shared infrastructure.

## Related Topics
* **Auto-scaling:** The process of adding or removing discrete compute units.
* **Serverless Compute:** A model where the underlying infrastructure and scaling are entirely abstracted from the user.
* **Resource Governance:** The policies and tools used to monitor and limit compute consumption.
* **OLAP (Online Analytical Processing):** The typical workload category for data warehouses.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |