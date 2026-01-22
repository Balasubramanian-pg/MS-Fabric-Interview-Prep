# How Do You Monitor Eventstream Lag

Canonical documentation for How Do You Monitor Eventstream Lag. This document defines concepts, terminology, and standard usage.

## Purpose
Eventstream lag monitoring exists to quantify the temporal and numerical gap between data production and data consumption within asynchronous messaging systems. In distributed architectures, processing is decoupled; therefore, the rate of consumption rarely matches the rate of production perfectly. Monitoring this lag is critical for ensuring data freshness, maintaining Service Level Agreements (SLAs), and identifying systemic bottlenecks or failures before they result in data loss or significant business impact.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core metrics for measuring processing delays.
* Methodologies for calculating lag across distributed partitions.
* Theoretical frameworks for observability in append-only logs.
* Strategies for alerting and threshold definition.

**Out of scope:**
* Specific vendor implementations (e.g., Apache Kafka, AWS Kinesis, Azure Event Hubs).
* Performance tuning of specific consumer applications.
* Hardware-level network latency monitoring.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Lag** | The delta between the latest available message in a stream and the last message successfully processed by a consumer. |
| **Offset** | A unique identifier (usually a sequential integer) assigned to each record within a stream partition. |
| **High Watermark** | The offset of the last record that has been successfully written to and replicated within the stream. |
| **Consumer Offset** | The offset of the last record successfully acknowledged by a specific consumer or consumer group. |
| **Time-to-Process (TTP)** | The duration between the event's ingestion timestamp and the moment of successful processing. |
| **Partition** | A logical division of a stream used to scale consumption through parallelism. |
| **Throughput** | The volume of records processed per unit of time. |

## Core Concepts
Monitoring eventstream lag relies on two primary dimensions: **Quantity** and **Time**.

### 1. Record-Based Lag (Offset Lag)
This is the numerical difference between the producer's current position and the consumer's current position. 
*   *Formula:* `Lag = High Watermark - Current Consumer Offset`
*   *Utility:* Best for understanding the "backlog" size and resource requirements.

### 2. Time-Based Lag (Temporal Lag)
This measures the age of the oldest unprocessed message.
*   *Formula:* `Lag = Current Wall-Clock Time - Timestamp of Last Processed Message`
*   *Utility:* Best for measuring data freshness and SLA compliance.

### 3. Consumer Group Abstraction
In modern systems, lag is rarely monitored for a single process but rather for a "Consumer Group." Monitoring must aggregate lag across all members of the group to provide a holistic view of the system's health.

## Standard Model
The standard model for monitoring lag involves a three-tier observability approach:

1.  **The Broker Perspective:** The stream coordinator tracks the "High Watermark" for every partition. This is the source of truth for how much data exists.
2.  **The Consumer Perspective:** The consumer tracks its "Current Offset." This is the source of truth for how much data has been handled.
3.  **The Observer (Monitor):** A third-party process or internal logic that periodically samples both the Broker and Consumer perspectives to calculate the delta.

### Calculation Logic
To provide an authoritative lag metric, the monitor must:
*   Sample offsets for all partitions in a stream.
*   Sum the lag across all partitions to determine "Total Group Lag."
*   Identify the "Max Partition Lag" to detect processing skews (where one consumer is slower than others).

## Common Patterns
*   **External Probing:** Using a dedicated monitoring service that queries the stream metadata to compare offsets. This avoids adding overhead to the consumers.
*   **Intercepting/Sidecar Monitoring:** Attaching a monitoring agent to the consumer process that reports the timestamp of the record currently being processed.
*   **Threshold-Based Alerting:** Setting static or dynamic alerts (e.g., "Alert if lag > 10,000 records" or "Alert if TTP > 5 minutes").
*   **Predictive Lag Analysis:** Using throughput trends to estimate "Time to Recover" (how long it will take to clear the current lag at current processing speeds).

## Anti-Patterns
*   **Monitoring Only Total Lag:** Ignoring per-partition lag can hide "hot partitions" or "stuck consumers" where the total lag looks acceptable but specific data subsets are not being processed.
*   **Using Wall-Clock Time on Producers:** Relying on the producer's local clock for timestamps can lead to inaccurate lag metrics due to clock drift. Use the stream's ingestion timestamp whenever possible.
*   **Ignoring Idle Consumers:** Assuming zero lag means a healthy system. If a consumer is dead and the producer is not sending data, lag will remain zero. Monitoring must include "Consumer Heartbeat" or "Liveness" checks.
*   **Reactive Scaling Only:** Scaling consumers only when lag is high, without considering the cause (e.g., a downstream database failure), which may exacerbate the problem.

## Edge Cases
*   **Log Compaction:** In systems that delete old keys, offsets may not be contiguous. Lag calculations must account for the fact that the numerical difference in offsets may not represent the actual number of records remaining.
*   **Replays and Rewinds:** When a consumer is intentionally reset to an earlier offset, lag will artificially spike. Monitoring systems should be able to distinguish between "organic lag" and "administrative rewinds."
*   **Cold Starts:** New consumer groups starting from the "earliest" offset will report massive lag that is expected behavior, not a system failure.
*   **Skewed Data Distribution:** If a specific key (e.g., a high-volume tenant) causes one partition to be significantly larger than others, the consumer for that partition will lag while others remain idle.

## Related Topics
*   **Backpressure Handling:** How systems react when lag exceeds capacity.
*   **Dead Letter Queues (DLQ):** Where messages go when they cannot be processed, preventing infinite lag.
*   **Stream Partitioning Strategies:** How data is distributed, which directly impacts lag distribution.
*   **Observability (Metrics, Logs, Traces):** The broader context of system monitoring.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |