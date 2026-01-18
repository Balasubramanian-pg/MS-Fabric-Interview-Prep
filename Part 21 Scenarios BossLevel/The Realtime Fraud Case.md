# [The Realtime Fraud Case](Part 21 Scenarios BossLevel/The Realtime Fraud Case.md)

Canonical documentation for [The Realtime Fraud Case](Part 21 Scenarios BossLevel/The Realtime Fraud Case.md). This document defines concepts, terminology, and standard usage.

## Purpose
[The Realtime Fraud Case](Part 21 Scenarios BossLevel/The Realtime Fraud Case.md) represents the architectural and logical framework required to identify, evaluate, and mitigate fraudulent activity within a sub-second latency window. Its primary objective is to transition from reactive "detect and recover" models to proactive "prevent and protect" models. 

By processing transactions or user actions at the moment of occurrence, the Realtime Fraud Case minimizes financial loss, reduces operational overhead associated with chargebacks, and maintains the integrity of digital ecosystems. It addresses the problem of high-velocity exploitation where traditional batch processing is insufficient to stop a threat before the value has been exfiltrated.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Event Evaluation:** The logic governing how individual events are assessed for risk.
* **State Management:** The requirement for maintaining historical context (e.g., sliding windows) in a streaming environment.
* **Decision Orchestration:** The workflow from data ingestion to final disposition (Approve, Deny, Challenge).
* **Latency Budgets:** The constraints imposed by user experience and system timeouts.

**Out of scope:**
* **Specific Vendor Implementations:** Particular cloud providers, database engines, or third-party scoring APIs.
* **Legal/Regulatory Compliance:** Specific regional laws (e.g., GDPR, CCPA), though the system must support compliance-ready data handling.
* **Post-Mortem Forensic Analysis:** Batch-based investigation tools used after a fraud event has been finalized.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Latency Budget** | The maximum allowable time (usually in milliseconds) for the system to return a fraud decision without degrading the user experience. |
| **Feature Engineering** | The process of transforming raw event data into signals (e.g., "number of logins in the last 5 minutes") used for scoring. |
| **False Positive Ratio** | The frequency at which legitimate actions are incorrectly flagged as fraudulent, measured against total alerts. |
| **Velocity Check** | A calculation of the frequency of a specific action (e.g., card attempts) over a defined time window. |
| **Disposition** | The final action taken by the system (e.g., Allow, Block, Step-up Authentication). |
| **Shadow Mode** | A deployment state where a fraud model runs in production and generates scores, but its decisions are not enforced. |

## Core Concepts

### 1. The Event-Driven Paradigm
[The Realtime Fraud Case](Part 21 Scenarios BossLevel/The Realtime Fraud Case.md) treats every user interaction as a discrete event. Unlike batch systems that look at "tables," a realtime system looks at "streams." Each event must carry enough context or be enriched quickly enough to allow for an immediate decision.

### 2. Statefulness vs. Statelessness
*   **Stateless Evaluation:** Assessing an event based only on its own data (e.g., "Is this IP on a blocklist?").
*   **Stateful Evaluation:** Assessing an event based on its relationship to previous events (e.g., "Has this user tried five different credit cards in the last sixty seconds?").

### 3. The Latency-Accuracy Trade-off
In a realtime context, a perfect model that takes five seconds to run is often less valuable than a "good" model that takes 50 milliseconds. The system must prioritize throughput and low-latency response times to avoid interrupting the "happy path" of legitimate users.

## Standard Model
The generally accepted model for a Realtime Fraud Case follows a linear pipeline:

1.  **Ingestion:** Capture the raw event (transaction, login, profile change).
2.  **Enrichment:** Augment the event with external data (IP geolocation, device fingerprinting, account history).
3.  **Feature Calculation:** Compute realtime aggregates (velocity, frequency, deviation from "normal" behavior).
4.  **Scoring Engine:** Apply a combination of heuristic rules and Machine Learning (ML) models to produce a risk score.
5.  **Decisioning:** Map the risk score to a disposition (e.g., Score > 90 = Block).
6.  **Action/Feedback Loop:** Execute the decision and log the outcome for future model training.

## Common Patterns

### Velocity and Frequency Analysis
Monitoring how often an attribute (email, device ID, credit card) appears within a sliding window (e.g., 1 minute, 1 hour, 24 hours). Rapid spikes typically indicate automated attacks or "carding" attempts.

### Behavioral Biometrics
Analyzing *how* a user interacts with a system—such as typing speed, mouse movements, or touch pressure—to distinguish between a human owner and a bot or unauthorized user.

### Geofencing and Impossible Travel
Comparing the physical location of the current event with the location of the previous event. If the distance between the two exceeds the speed of commercial flight, the case is flagged for high risk.

## Anti-Patterns

### Synchronous External Dependencies
Relying on a slow, third-party API call within the critical path of a transaction. If the third party hangs, the entire checkout or login process fails.
*   *Correction:* Use asynchronous enrichment or cached data with a "fail-open" or "fail-closed" policy.

### Over-Reliance on Static Rules
Using only "If-Then" statements (e.g., "If amount > $500, then block"). Fraudsters quickly identify and circumvent static thresholds.
*   *Correction:* Supplement rules with probabilistic ML models that detect subtle patterns.

### Lack of Versioning in Features
Calculating features (like "average spend") differently in the production environment than they were calculated during the model training phase (Training-Serving Skew).
*   *Correction:* Use a unified Feature Store to ensure consistency between training and inference.

## Edge Cases

### The "Cold Start" Problem
When a new user or a new merchant joins the platform, there is no historical data to calculate velocities or behavioral norms. The system must rely on "Global" features or stricter initial thresholds until a baseline is established.

### High-Volume Bursts (Flash Crowds)
During events like "Black Friday" or major product launches, legitimate traffic patterns resemble "DDoS" or bot attacks. The system must be able to scale elastically and distinguish between high-intent human buyers and malicious scripts.

### Replay Attacks
A fraudster captures a legitimate, previously approved transaction and attempts to resubmit it. The system must maintain a "deduplication" state to recognize and reject identical event IDs within a specific timeframe.

## Related Topics
*   **Identity and Access Management (IAM):** The foundation of user verification.
*   **Machine Learning Operations (MLOps):** The lifecycle management of the models used in scoring.
*   **Streaming Systems:** The underlying infrastructure (e.g., message brokers and stream processors).
*   **Step-up Authentication:** The process of requesting MFA (Multi-Factor Authentication) when a fraud score is ambiguous.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |