# What Is A Webhook Activity

Canonical documentation for What Is A Webhook Activity. This document defines concepts, terminology, and standard usage.

## Purpose
A Webhook Activity represents a discrete unit of work within a workflow or orchestration system that facilitates communication between a source system and an external destination via HTTP-based callbacks. 

The primary purpose of a Webhook Activity is to bridge the gap between isolated environments. It allows a process to either push data to an external service or pause its execution until an external event occurs. This addresses the problem of inter-system synchronization in distributed architectures, enabling event-driven integration without the overhead of continuous polling.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The lifecycle of an automated HTTP callback request.
* The role of the activity within a larger process orchestration.
* Data exchange patterns (Push, Pull, and Wait).
* Security and reliability requirements for activity execution.

**Out of scope:**
* Specific vendor implementations (e.g., GitHub Webhooks, Slack Apps).
* Programming language-specific SDKs.
* General web server configuration (e.g., Nginx or Apache setup).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Webhook Activity** | A managed step in a workflow that executes an HTTP request to a predefined URL to signal an event or request an action. |
| **Payload** | The data packet transmitted during the activity, typically formatted as JSON or XML. |
| **Endpoint** | The destination URL provided by the receiving system to accept the webhook request. |
| **Callback** | An executable piece of code or a URL passed as an argument to allow the receiver to notify the sender of completion. |
| **Idempotency** | The property of an activity where multiple identical requests have the same effect as a single request. |
| **Secret/Token** | A shared credential used to sign or authorize the request to ensure authenticity. |

## Core Concepts
The Webhook Activity is built upon three fundamental pillars:

1.  **Event-Driven Execution:** Unlike scheduled tasks, a Webhook Activity is triggered by a state change within a system. It is reactive by nature.
2.  **Asynchronous Communication:** The activity typically initiates a request and does not require the source system to maintain an open connection while the destination processes the data.
3.  **Inversion of Control:** The source system (the producer) determines when the data is sent, but the destination system (the consumer) determines how that data is processed.

## Standard Model
The standard model for a Webhook Activity follows a structured lifecycle:

1.  **Trigger:** A condition within the parent workflow is met, initiating the activity.
2.  **Payload Construction:** The system gathers relevant context (metadata, state, timestamps) and serializes it into a standard format.
3.  **Dispatch:** The activity executes an HTTP request (usually `POST`) to the configured endpoint.
4.  **Handshake/Delivery:** The receiving system acknowledges receipt with a `2xx` status code.
5.  **Processing (External):** The destination system performs its internal logic.
6.  **Completion/Callback (Optional):** If the activity is "blocking," it waits for a return signal from the destination to proceed to the next step in the workflow.

## Common Patterns
*   **Fire-and-Forget:** The activity sends the payload and considers the task complete once a `202 Accepted` or `200 OK` is received.
*   **Wait for Callback:** The activity sends a request and enters a "Pending" state. It only resumes or completes when the external system calls a specific return URL (callback) provided in the initial payload.
*   **Fan-out:** A single event triggers multiple Webhook Activities to different endpoints simultaneously.
*   **Signed Payloads:** The activity includes a cryptographic signature (e.g., HMAC) in the header to allow the receiver to verify the sender's identity.

## Anti-Patterns
*   **Synchronous Processing of Long Tasks:** The receiver attempting to perform heavy processing before returning an HTTP response, leading to timeouts in the source activity.
*   **Lack of Idempotency:** Designing activities that cause side effects (like double-billing) if the same webhook is delivered twice due to network retries.
*   **Hardcoding Endpoints:** Embedding destination URLs directly into application code rather than using a dynamic registry or configuration layer.
*   **Ignoring Response Codes:** Treating all delivery attempts as successful regardless of the HTTP status code returned by the destination.

## Edge Cases
*   **Network Partitions:** The request is sent and processed by the receiver, but the acknowledgment is lost. The sender perceives a failure despite a successful execution.
*   **Payload Versioning:** When the schema of the payload changes, but the receiver expects an older version, leading to silent data loss or parsing errors.
*   **Circular Dependencies:** System A triggers a webhook to System B, which in turn triggers a webhook back to System A, potentially creating an infinite loop.
*   **Rate Limiting:** The destination system may throttle incoming requests, requiring the Webhook Activity to implement sophisticated back-off strategies.

## Related Topics
*   **Event-Driven Architecture (EDA):** The broader architectural style that utilizes webhooks.
*   **API Management:** The governance of the endpoints that Webhook Activities target.
*   **Message Queues:** Often used in conjunction with webhooks to buffer requests and ensure delivery.
*   **HTTP/REST Standards:** The underlying protocol standards for webhook communication.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |