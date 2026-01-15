# How Does Microsoft Entra Id Formerly Azure Ad B2B Work With Fabric

Canonical documentation for How Does Microsoft Entra Id Formerly Azure Ad B2B Work With Fabric. This document defines the conceptual model, terminology, constraints, and standard usage patterns.

> [!NOTE]
> This documentation is implementation-agnostic and intended to serve as a stable reference.

## 1. Purpose and Problem Space

The integration of Microsoft Entra ID (formerly Azure AD B2B) with Fabric is designed to address the challenges of identity and access management in collaborative environments. The primary purpose of this integration is to enable secure, seamless, and scalable collaboration between organizations by leveraging the capabilities of Microsoft Entra ID for identity management and Fabric for data integration and analytics. The class of problems it addresses includes identity governance, access control, and data security in B2B collaborations. Misunderstanding or inconsistent application of this integration can lead to security breaches, data leaks, and compliance issues, highlighting the importance of a well-documented and standardized approach.

## 2. Conceptual Overview

The conceptual model of Microsoft Entra ID working with Fabric involves several key components:
- **Microsoft Entra ID (formerly Azure AD B2B):** Provides identity and access management capabilities, enabling organizations to manage access to their resources securely.
- **Fabric:** Offers a platform for data integration, analytics, and collaboration, requiring secure and controlled access to shared data and resources.
- **Integration Layer:** Facilitates the connection between Microsoft Entra ID and Fabric, enabling the use of identities managed in Microsoft Entra ID for access control in Fabric.

These components interact to produce outcomes such as secure single sign-on (SSO), fine-grained access control, and automated user provisioning, which are essential for collaborative environments.

## 3. Scope and Non-Goals

Clarify the explicit boundaries of this documentation.

**In scope:**
* Conceptual overview of Microsoft Entra ID and Fabric integration
* Identity and access management principles
* Security considerations for B2B collaborations

**Out of scope:**
* Tool-specific implementations of Microsoft Entra ID and Fabric
* Vendor-specific behavior beyond standard APIs and interfaces
* Operational or procedural guidance for deployment and maintenance

> [!IMPORTANT]
> Out-of-scope items may be addressed in companion or derivative documentation.

## 4. Terminology and Definitions

Provide precise, stable definitions for all key terms used throughout this document.

| Term | Definition |
|------|------------|
| Microsoft Entra ID | Microsoft's identity and access management service, formerly known as Azure AD B2B, designed for business-to-business collaborations. |
| Fabric | A platform for data integration, analytics, and collaboration, requiring secure access control. |
| Identity Governance | The process of managing digital identities and their associated access rights across systems and applications. |
| Access Control | The process of granting or denying access to resources based on user identities and permissions. |

> [!TIP]
> Definitions should avoid contextual or time-bound language and remain valid as the ecosystem evolves.

## 5. Core Concepts

Explain the fundamental ideas that form the basis of this topic.

### 5.1 Identity Management
High-level explanation: Identity management involves the creation, maintenance, and termination of digital identities and their associated access rights. In the context of Microsoft Entra ID and Fabric, it's crucial for ensuring that only authorized users have access to shared resources.

### 5.2 Access Control
High-level explanation: Access control refers to the mechanisms and policies that regulate who can access specific resources within Fabric. Microsoft Entra ID plays a critical role in this by providing the identity information needed to enforce access control decisions.

### 5.3 Concept Interactions and Constraints
The core concepts of identity management and access control interact through the integration layer, which must handle constraints such as user authentication, authorization, and auditing. The integration must also consider the scalability and security requirements of both Microsoft Entra ID and Fabric.

## 6. Standard Model

Describe the generally accepted or recommended model for this topic.

### 6.1 Model Description
The standard model involves using Microsoft Entra ID as the central identity management system, with Fabric integrating through standard protocols (e.g., OAuth, OpenID Connect) to leverage identities for access control. This model ensures a unified identity experience across the collaborative environment.

### 6.2 Assumptions
- The model assumes that both Microsoft Entra ID and Fabric are properly configured and maintained.
- It also assumes that the integration layer is securely implemented, following best practices for authentication and authorization.

### 6.3 Invariants
- The model must always maintain the integrity of user identities and access rights.
- It must ensure that access to resources in Fabric is granted based on the user's identity and permissions managed in Microsoft Entra ID.

> [!IMPORTANT]
> Deviations from the standard model must be explicitly documented and justified.

## 7. Common Patterns

Document recurring, accepted patterns associated with this topic.

### Pattern A: Secure Collaboration
- **Intent:** Enable secure collaboration between organizations by leveraging Microsoft Entra ID for identity management and Fabric for data integration and analytics.
- **Context:** Typically applied in B2B collaborations where secure access to shared resources is critical.
- **Tradeoffs:** Offers enhanced security and compliance but may require additional setup and maintenance efforts.

## 8. Anti-Patterns

Describe common but discouraged practices.

> [!WARNING]
> These anti-patterns frequently lead to correctness, maintainability, or scalability issues.

### Anti-Pattern A: Local Identity Management
- **Description:** Managing identities locally within Fabric without integrating with Microsoft Entra ID.
- **Failure Mode:** Leads to identity silos, increased management complexity, and potential security vulnerabilities.
- **Common Causes:** Lack of understanding of the benefits of centralized identity management or underestimation of the complexity of local identity management.

## 9. Edge Cases and Boundary Conditions

Explain unusual or ambiguous scenarios that may challenge the standard model.

> [!CAUTION]
> Edge cases are often under-documented and a common source of incorrect assumptions.

- **Scenario:** A user is a member of multiple organizations, each with its own Microsoft Entra ID tenant, and needs access to resources in Fabric that are shared across these organizations.
- **Challenge:** Ensuring that the user's access rights are correctly managed and enforced across different Microsoft Entra ID tenants and within Fabric.

## 10. Related Topics

List adjacent, dependent, or prerequisite topics.
- Identity and Access Management (IAM)
- Cloud Security
- B2B Collaboration Platforms
- Data Integration and Analytics

## 11. References

Provide exactly **five** authoritative external references that substantiate or inform this topic.

1. **Microsoft Entra ID Documentation**  
   Microsoft Corporation  
   https://docs.microsoft.com/en-us/azure/active-directory/  
   *Justification:* Official documentation for Microsoft Entra ID, providing detailed information on its capabilities and integration possibilities.
2. **Fabric Documentation**  
   Fabric Team  
   https://fabric.io/docs  
   *Justification:* Official documentation for Fabric, offering insights into its features and integration requirements.
3. **NIST Special Publication 800-63**  
   National Institute of Standards and Technology  
   https://pages.nist.gov/800-63-3/  
   *Justification:* A authoritative guide on digital identity guidelines, relevant to understanding identity management principles.
4. **OAuth 2.0 Specification**  
   Internet Engineering Task Force (IETF)  
   https://datatracker.ietf.org/doc/html/rfc6749  
   *Justification:* Standard specification for authorization, crucial for understanding the integration mechanisms between Microsoft Entra ID and Fabric.
5. **OpenID Connect Specification**  
   OpenID Foundation  
   https://openid.net/specs/openid-connect-core-1_0.html  
   *Justification:* Standard specification for identity layer on top of the OAuth 2.0 protocol, relevant to the authentication aspects of the integration.

> [!IMPORTANT]
> These references are normative unless explicitly marked as informative.

## 12. Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial documentation |

---