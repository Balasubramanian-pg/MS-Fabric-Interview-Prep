# [What Is An Eventstream](Part 05 RealTime Science PowerBI/What Is An Eventstream.md)

Canonical documentation for [What Is An Eventstream](Part 05 RealTime Science PowerBI/What Is An Eventstream.md). This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of an eventstream is to provide a continuous, append-only, and immutable record of discrete occurrences (events) within a system. It addresses the need for capturing "state-in-motion" rather than just "state-at-rest." 

In traditional request-response architectures, the current state of an object is often the only data preserved. An eventstream solves the problem of data loss regarding *how* a system reached its current state by preserving every transition as a unique, timestamped fact. This enables decoupled communication, historical auditing, real-time analytics, and system resilience.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The logical structure and characteristics of an eventstream.
* The relationship between producers, consumers, and the stream.
* Fundamental properties such as immutability, ordering, and persistence.
* Theoretical models for processing and storage.

**Out of scope:**
* Specific vendor implementations (e.g., Apache Kafka, Amazon Kinesis, Azure Event Hubs).
* Physical hardware or networking protocols used to transmit streams.
* Programming language-specific client libraries.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| Event | A record of a fact that happened in the past; it is immutable and contains a timestamp. |
| Stream | An unbounded sequence of events ordered by time or sequence number. |
| Producer | An entity or process that generates events and appends them to the stream. |
| Consumer | An entity or process that reads and processes events from the stream. |
| Offset/Sequence Number | A unique identifier representing the position of an event within a stream. |
| Payload | The actual data or state change information contained within an event. |
| Metadata | Contextual information about the event (e.g., source ID, schema version, trace headers). |
| Retention | The policy defining how long an event remains available in the stream before being purged. |

## Core Concepts

### Immutability
Once an event is written to an eventstream, it cannot be modified or deleted. If the state represented by an event needs to change, a new event must be appended to the stream to represent that change. This ensures a reliable audit trail.

### Temporal Ordering
Events are naturally ordered. While global ordering across all streams is difficult in distributed systems, an individual eventstream guarantees that events are processed in the order they were received by the stream coordinator.

### Unboundedness
Unlike a file or a batch of data, an eventstream has no defined end. It is a continuous flow of information that persists as long as the system generating the events is active.

### Decoupling
Producers do not know who the consumers are, nor do they wait for a response. This asynchronous nature allows systems to scale independently and remain resilient to downstream failures.

## Standard Model
The standard model for an eventstream is the **Distributed Append-only Log**.

1.  **Append-only:** New data is always added to the end of the log.
2.  **Log-structured Storage:** The stream is treated as a linear sequence of records.
3.  **Consumer Pointers:** Consumers maintain their own "cursor" or "offset." This allows multiple consumers to read the same stream at different speeds and at different points in time without interfering with one another.
4.  **Partitioning:** To scale, a logical stream may be divided into multiple partitions. Ordering is typically guaranteed within a partition but not necessarily across partitions.

## Common Patterns

### Event Sourcing
Using the eventstream as the primary source of truth for an application's state. The current state is derived by "replaying" the stream from the beginning.

### Change Data Capture (CDC)
Observing and capturing changes made to a database and streaming those changes as events to other systems (e.g., search indexes or data warehouses).

### Pub/Sub (Publish/Subscribe)
A pattern where producers (publishers) categorize events into streams (topics) and consumers (subscribers) express interest in specific streams to receive updates.

### Stream Processing
The act of performing real-time computations on events as they pass through the stream (e.g., filtering, aggregating, or transforming data).

## Anti-Patterns

### The "God Stream"
Placing unrelated event types into a single stream. This forces consumers to process and discard large amounts of irrelevant data, increasing latency and complexity.

### Using Eventstreams for Request-Response
Attempting to use an eventstream as a synchronous communication channel. This introduces high latency and violates the decoupled nature of streaming architectures.

### Large Payloads
Storing massive binary objects (e.g., high-resolution images or large PDF files) directly in the event payload. Eventstreams are optimized for small, discrete messages; large blobs should be stored in external storage with a reference link in the event.

### Ignoring Schema Evolution
Failing to version the structure of events. As systems evolve, consumers may break if they encounter an event format they do not recognize.

## Edge Cases

### Out-of-Order Delivery
In distributed environments, events may arrive at the stream in a different order than they were generated. Systems must be designed to handle "late-arriving" data using watermarking or windowing techniques.

### Exactly-Once Semantics (EOS)
The challenge of ensuring that an event is processed exactly once, even in the event of network failures or retries. This usually requires idempotent consumers or transactional producers.

### Poison Pill Events
An event that is malformed or cannot be processed by a consumer. If not handled (e.g., via a Dead Letter Queue), it can cause the consumer to crash repeatedly, stalling the entire stream.

## Related Topics
* **Message Queues:** Often confused with eventstreams, but typically focus on transient task distribution rather than persistent history.
* **CQRS (Command Query Responsibility Segregation):** A pattern often used in conjunction with eventstreams to separate read and write models.
* **Idempotency:** A critical property for consumers to ensure that processing the same event multiple times has the same effect as processing it once.
* **Data Lakehouses:** Modern storage architectures that often ingest data directly from eventstreams for long-term analysis.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |