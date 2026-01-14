# How Do You Connect To Onpremises Data

Canonical documentation for How Do You Connect To Onpremises Data. This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of on-premises data connectivity is to bridge the gap between decentralized compute environments (such as public clouds, edge locations, or third-party SaaS platforms) and data assets residing within a private, firewalled infrastructure. 

This topic addresses the fundamental challenge of maintaining data security and sovereignty while enabling modern applications to consume legacy or localized data. It provides a framework for establishing secure, performant, and reliable communication channels across disparate network boundaries.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Architectural patterns for traversing network boundaries.
* Security mechanisms for data in transit.
* Theoretical models for hybrid data integration.
* Connectivity topologies (Point-to-Site, Site-to-Site, etc.).

**Out of scope:**
* Specific vendor implementations (e.g., Azure Integration Runtime, AWS Direct Connect, Google Cloud Interconnect).
* Physical hardware installation guides.
* Database-specific query optimization.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **On-premises** | Data or infrastructure located within a private network, typically behind a corporate firewall. |
| **Gateway** | A software or hardware component that acts as a bridge, translating protocols and managing secure transit between networks. |
| **Egress** | Data traffic exiting a private network toward an external destination. |
| **Ingress** | Data traffic entering a private network from an external source. |
| **Tunneling** | The process of encapsulating one network protocol within another to secure or route data through a restricted path. |
| **DMZ (Demilitarized Zone)** | A physical or logical subnetwork that contains and exposes an organization's external-facing services to an untrusted network. |
| **Data Sovereignty** | The concept that data is subject to the laws and governance structures within the nation or network where it is collected. |

## Core Concepts
The fundamental ideas governing on-premises connectivity revolve around the tension between **accessibility** and **security**.

### 1. Network Boundary Traversal
Most on-premises environments are protected by firewalls that block unsolicited inbound traffic. Connectivity solutions must determine whether to "punch a hole" (Ingress) or establish an outbound-initiated connection (Egress) to facilitate data flow.

### 2. Identity and Authentication
Connecting to on-premises data requires a trust relationship. This involves not only network-level authentication (certificates, VPN keys) but also application-level authorization (database credentials, API keys).

### 3. Latency and Throughput
Unlike cloud-to-cloud communication, on-premises connectivity is often limited by the physical distance and the quality of the internet service provider (ISP). Bandwidth management and compression are core considerations.

## Standard Model
The generally accepted model for on-premises connectivity is the **Hybrid Connectivity Framework**. This model categorizes connections based on their architectural depth:

1.  **The Agent/Gateway Layer:** A lightweight service installed inside the private network. It initiates an outbound connection to a cloud relay. This avoids opening inbound firewall ports.
2.  **The Transport Layer:** The encrypted "pipe" (TLS/SSL) through which data travels.
3.  **The Orchestration Layer:** The external service that requests data, which is then fulfilled by the internal agent.

## Common Patterns

### Outbound-Initiated Gateway (The "Relay" Pattern)
The most common modern pattern. An internal agent polls an external service for tasks. When a request arrives, the agent fetches the data locally and pushes it out. This is highly secure as it requires no inbound firewall changes.

### Virtual Private Network (VPN)
A Site-to-Site or Point-to-Site encrypted tunnel that makes the remote environment appear as part of the local network. This is standard for bulk data movement and administrative access.

### Dedicated Private Circuit
A physical, private connection provided by a telecommunications carrier that bypasses the public internet entirely. This is used for high-volume, low-latency requirements where security and performance are paramount.

### Reverse Proxy / DMZ
Exposing a specific API or service endpoint within a DMZ. External callers hit the proxy, which then forwards the request to the internal data source after rigorous inspection.

## Anti-Patterns

*   **Public Exposure:** Directly exposing a database port (e.g., TCP 1433 or 3306) to the public internet.
*   **Hardcoded Credentials:** Storing local database credentials within cloud-based application code rather than using a secure vault or identity injection.
*   **Bypassing the Security Stack:** Implementing "shadow IT" connectivity solutions that circumvent corporate firewalls or monitoring tools.
*   **Over-Provisioning Access:** Granting a connectivity agent "Domain Admin" or "Root" privileges when it only needs read access to a specific schema.

## Edge Cases

### Air-Gapped Systems
Systems with no physical or logical connection to external networks. Connectivity usually requires "Data Sneakers" (physical media transfer) or highly controlled unidirectional security gateways (Data Diodes).

### Intermittent Connectivity
Environments with unstable network links (e.g., maritime, remote mining). These require "Store and Forward" patterns where data is cached locally and synced whenever a connection is established.

### Legacy Protocol Translation
Connecting to mainframes or industrial equipment using non-IP protocols. This requires a specialized "Protocol Gateway" to translate legacy signals into modern formats like JSON or Parquet before transmission.

## Related Topics
*   **Data Integration (ETL/ELT):** The process of moving and transforming the data once the connection is established.
*   **Zero Trust Architecture:** A security model that assumes no inherent trust, requiring continuous verification for on-premises access.
*   **Network Latency Optimization:** Techniques for reducing the "round-trip time" (RTT) in hybrid environments.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |