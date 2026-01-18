# [Api Rate Limits](Part 20 EdgeCases BossLevel/Api Rate Limits.md)

Canonical documentation for [Api Rate Limits](Part 20 EdgeCases BossLevel/Api Rate Limits.md). This document defines concepts, terminology, and standard usage.

## Purpose
[Api Rate Limits](Part 20 EdgeCases BossLevel/Api Rate Limits.md) exist to govern the consumption of resources within a distributed system. Their primary purpose is to ensure service availability, protect infrastructure from intentional or accidental exhaustion (Denial of Service), and maintain a predictable quality of service (QoS) for all consumers. By imposing constraints on the frequency of requests, a provider can manage load, prevent cascading failures, and establish tiered access models for monetization or priority-based resource allocation.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   Theoretical models of request governance.
*   Standard communication protocols for limit enforcement (HTTP semantics).
*   Identification strategies for request actors.
*   Architectural patterns for distributed and local enforcement.

**Out of scope:**
*   Specific vendor configurations (e.g., AWS WAF, Nginx, Cloudflare).
*   Programming language-specific library implementations.
*   Network-layer (Layer 3/4) DDoS mitigation techniques.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Rate Limit** | The maximum number of operations allowed within a specific time interval. |
| **Quota** | A long-term allocation of requests (e.g., monthly or daily) often tied to billing or subscription tiers. |
| **Throttling** | The process of restricting or slowing down requests once a limit has been reached. |
| **Window** | The specific duration of time used to calculate the current consumption (e.g., a second, minute, or hour). |
| **Burst** | A predefined allowance that permits a consumer to exceed the steady-state rate limit for a short duration. |
| **Identifier** | The attribute used to track usage, such as an API Key, User ID, or IP address. |
| **Backoff** | The strategy employed by a consumer to wait before retrying a rejected request. |

## Core Concepts
The fundamental ideas of rate limiting revolve around the intersection of identity, state, and enforcement.

### Identification
To apply a limit, the system must identify the actor. Common strategies include:
*   **Identity-based:** Using an API key or OAuth token. This is the most precise method.
*   **Source-based:** Using the originating IP address. This is less precise due to NAT (Network Address Translation) and shared proxies.
*   **Resource-based:** Limiting access to a specific expensive endpoint regardless of the user.

### State Management
Rate limiting requires maintaining a "counter" or "bucket" state. In a single-server environment, this is stored in memory. In distributed systems, this state is typically stored in a high-performance, low-latency data store (e.g., an in-memory cache) to ensure consistency across multiple application nodes.

### Enforcement Policy
Enforcement determines the action taken when a limit is exceeded. The most common action is **Rejection** (returning an error), but other policies include **Delaying** (queuing the request) or **Degradation** (returning a cached or simplified response).

## Standard Model
The generally accepted model for API Rate Limiting follows the HTTP/1.1 and HTTP/2 semantics for request governance.

### The Response Protocol
When a limit is exceeded, the server should return an **HTTP 429 Too Many Requests** status code. To provide a machine-readable contract, the following headers are standard:

*   `X-RateLimit-Limit`: The maximum number of requests permitted in the current window.
*   `X-RateLimit-Remaining`: The number of requests left in the current window.
*   `X-RateLimit-Reset`: The time (usually in UTC epoch seconds) when the current limit window resets.
*   `Retry-After`: A suggestion to the client on how long to wait (in seconds or a specific date) before retrying.

## Common Patterns
Recurring patterns or approaches used in the industry:

1.  **Fixed Window Counter:** Resets the count at the start of every calendar interval (e.g., exactly at the start of every minute). Simple to implement but susceptible to "bursting" at the window edges.
2.  **Sliding Window Log:** Tracks the timestamp of every request. Highly accurate but memory-intensive.
3.  **Token Bucket:** A "bucket" holds tokens that are added at a fixed rate. Each request consumes a token. This allows for natural "burstiness" while maintaining a long-term average rate.
4.  **Leaky Bucket:** Requests enter a bucket and are processed at a constant rate. If the bucket overflows, requests are discarded. This smooths out traffic spikes into a steady flow.
5.  **Tiered Limiting:** Applying different limits based on the consumer's metadata (e.g., "Free" vs. "Premium" tiers).

## Anti-Patterns
Common mistakes or discouraged practices:

*   **Silent Dropping:** Dropping requests without returning a 429 status code, making it impossible for clients to distinguish between a rate limit and a network failure.
*   **Global Locking:** Using a synchronous global lock for rate limiting in a distributed system, which introduces a single point of failure and significant latency.
*   **Inconsistent Headers:** Providing `X-RateLimit` headers that do not match the actual enforcement logic, leading to client-side integration errors.
*   **IP-Only Limiting for Authenticated Users:** Relying solely on IP addresses when a user identity is available, which unfairly penalizes users behind shared corporate or mobile gateways.

## Edge Cases
Explain unusual, ambiguous, or boundary scenarios.

*   **Clock Skew:** In distributed systems, if the enforcement nodes and the state store have different system times, the "Reset" time provided to the client may be inaccurate.
*   **Thundering Herd:** When a window resets, a large number of clients may simultaneously retry, causing a massive spike in load immediately at the start of the new window.
*   **Race Conditions:** In high-concurrency environments, two requests might check the remaining quota simultaneously, both see "1 remaining," and both proceed, effectively allowing $N+1$ requests.
*   **Distributed State Latency:** The time it takes to update a central counter across regions may allow a consumer to briefly exceed their limit before the state is synchronized.

## Related Topics
*   **API Governance:** The broader discipline of managing API lifecycles.
*   **Circuit Breaking:** A pattern to stop requests to a failing downstream service.
*   **Load Balancing:** Distributing traffic across multiple servers.
*   **Idempotency:** Ensuring that retrying a rate-limited request does not result in duplicate side effects.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |