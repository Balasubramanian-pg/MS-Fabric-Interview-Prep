# What Is Customer Managed Keys Cmk Support In Fabric

Canonical documentation for What Is Customer Managed Keys Cmk Support In Fabric. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

Customer Managed Keys (CMK) support in Fabric is designed to address the need for secure and flexible key management in distributed ledger technology (DLT) networks. The primary problem it solves is the lack of control over encryption keys used to protect sensitive data in Fabric networks. Without CMK support, network administrators rely on default key management systems, which may not meet specific security requirements or compliance regulations. This can lead to risks such as unauthorized data access, non-compliance with regulatory requirements, and insufficient key rotation policies. By providing CMK support, Fabric enables organizations to manage their encryption keys securely, ensuring that sensitive data is protected according to their specific security policies and regulatory needs.

## 2. Conceptual Overview

The conceptual model of CMK support in Fabric involves several major components:
- **Customer Managed Keys (CMK):** These are encryption keys managed by the customer outside of the Fabric network. CMKs are used to encrypt and decrypt sensitive data within the network.
- **Key Management System (KMS):** This is a system used by the customer to generate, distribute, and manage CMKs. The KMS is responsible for secure key storage, rotation, and revocation.
- **Fabric Network:** This refers to the distributed ledger network where CMKs are utilized for data encryption and decryption.
- **Encryption and Decryption Processes:** These are the processes within the Fabric network that use CMKs to protect data.

These components interact to ensure that data within the Fabric network is encrypted and decrypted using keys that are securely managed by the customer, thereby enhancing data security and compliance.

## 3. Scope and Non-Goals

Clarify the explicit boundaries of this documentation.

**In scope:**
* Conceptual model of CMK support in Fabric
* Terminology and definitions related to CMK and key management in Fabric
* Core concepts and standard models for implementing CMK support

**Out of scope:**
* Tool-specific implementations of CMK support
* Vendor-specific behavior or configurations
* Operational or procedural guidance for managing CMKs

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

Provide precise, stable definitions for all key terms used throughout this document.

| Term | Definition |
|------|------------|
| CMK | Customer Managed Key: An encryption key managed by the customer outside of the Fabric network. |
| KMS | Key Management System: A system used to generate, distribute, and manage encryption keys. |
| Fabric Network | A distributed ledger network utilizing CMKs for data encryption and decryption. |
| Encryption | The process of converting plaintext data into unreadable ciphertext using a CMK. |
| Decryption | The process of converting ciphertext back into plaintext using a CMK. |

> [!TIP]
> Definitions should avoid contextual or time-bound language and remain valid as the ecosystem evolves.

## 5. Core Concepts

Explain the fundamental ideas that form the basis of this topic.

### 5.1 Customer Managed Keys (CMK)
CMKs are encryption keys that are generated, managed, and secured by the customer. They are used to encrypt and decrypt sensitive data within the Fabric network, ensuring that the customer has full control over the encryption process.

### 5.2 Key Management System (KMS)
A KMS is crucial for the secure management of CMKs. It provides functionalities such as key generation, distribution, rotation, and revocation, ensuring that CMKs are handled securely and in compliance with organizational security policies.

### 5.3 Concept Interactions and Constraints
The interaction between CMKs, KMS, and the Fabric network is constrained by the need for secure key management practices. This includes ensuring that CMKs are securely stored, rotated regularly, and revoked when necessary. The KMS must be integrated with the Fabric network to enable the use of CMKs for data encryption and decryption.

## 6. Standard Model

Describe the generally accepted or recommended model for this topic.

### 6.1 Model Description
The standard model for CMK support in Fabric involves the integration of a KMS with the Fabric network. The KMS generates and manages CMKs, which are then used by the Fabric network for data encryption and decryption. This model ensures that data within the Fabric network is protected by encryption keys that are securely managed by the customer.

### 6.2 Assumptions
This model assumes that the customer has a secure KMS in place and that the Fabric network is configured to support the use of CMKs.

### 6.3 Invariants
The invariants of this model include:
- CMKs are always generated and managed securely by the KMS.
- The Fabric network uses CMKs for all data encryption and decryption processes.
- The KMS and Fabric network are configured to ensure secure key storage, rotation, and revocation.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified.

## 7. Common Patterns

Document recurring, accepted patterns associated with this topic.

### Pattern A: Centralized Key Management
- **Intent:** To manage CMKs centrally for all applications within the Fabric network.
- **Context:** When multiple applications within the Fabric network require access to the same set of CMKs.
- **Tradeoffs:** Centralized management simplifies key distribution and rotation but may introduce a single point of failure if not properly secured.

## 8. Anti-Patterns

Describe common but discouraged practices.

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Hardcoding CMKs
- **Description:** Hardcoding CMKs directly into application code or configuration files.
- **Failure Mode:** Exposes CMKs to unauthorized access, making it easier for attackers to obtain the encryption keys.
- **Common Causes:** Lack of understanding of secure key management practices or shortcuts taken during development.

## 9. Edge Cases and Boundary Conditions

Explain unusual or ambiguous scenarios that may challenge the standard model.

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

- **Key Revocation:** What happens when a CMK needs to be revoked? How does the Fabric network handle data that was encrypted with the revoked key?
- **Key Expiration:** How are CMKs rotated or updated when they expire, and what impact does this have on the Fabric network?

## 10. Related Topics

List adjacent, dependent, or prerequisite topics.
- Key Management Systems (KMS)
- Encryption in Distributed Ledger Technology (DLT)
- Security and Compliance in Fabric Networks

## 11. References

Provide exactly **five** authoritative external references that substantiate or inform this topic.

1. **Hyperledger Fabric Documentation**  
   Hyperledger  
   https://hyperledger-fabric.readthedocs.io/en/release-2.2/  
   *Justification:* Official documentation for Hyperledger Fabric, including information on security and key management.
2. **NIST Special Publication 800-57**  
   National Institute of Standards and Technology  
   https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r5.pdf  
   *Justification:* Guidelines for key management, including generation, distribution, and storage of cryptographic keys.
3. **AWS Key Management Service (KMS)**  
   Amazon Web Services  
   https://aws.amazon.com/kms/  
   *Justification:* Example of a cloud-based key management service that can be integrated with Fabric for CMK support.
4. **Google Cloud Key Management Service (KMS)**  
   Google Cloud  
   https://cloud.google.com/kms  
   *Justification:* Another example of a cloud-based key management service, highlighting the variety of KMS solutions available for CMK management.
5. **ISO/IEC 27002:2013**  
   International Organization for Standardization  
   https://www.iso.org/standard/54533.html  
   *Justification:* International standard for information security controls, including key management, providing a framework for secure CMK practices.

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

> [!CAUTION]
> If fewer than five authoritative references exist, explicitly state this.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial documentation |

---

This documentation provides a comprehensive overview of Customer Managed Keys (CMK) support in Fabric, covering the conceptual model, terminology, core concepts, and standard practices. It serves as a stable reference for understanding and implementing CMK support in Fabric networks, ensuring secure and flexible key management for distributed ledger technology (DLT) applications.