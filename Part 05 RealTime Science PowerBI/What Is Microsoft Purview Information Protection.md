# [What Is Microsoft Purview Information Protection](Part 05 RealTime Science PowerBI/What Is Microsoft Purview Information Protection.md)

Canonical documentation for [What Is Microsoft Purview Information Protection](Part 05 RealTime Science PowerBI/What Is Microsoft Purview Information Protection.md). This document defines concepts, terminology, and standard usage.

## Purpose
Microsoft Purview Information Protection (formerly Microsoft Information Protection or MIP) is a framework designed to discover, classify, and protect sensitive information across its entire lifecycle, regardless of where it resides or travels. 

In a modern digital ecosystem, data is no longer confined to a single physical location or network perimeter. This topic addresses the problem of "data sprawl" and the risk of unauthorized disclosure by shifting the security focus from the container (the network or device) to the data itself. It provides a unified set of capabilities to ensure that sensitive information—such as intellectual property, personally identifiable information (PII), and financial records—is identified and safeguarded according to organizational policy.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative, focusing on the architectural and conceptual framework of the technology.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Data Discovery:** The mechanisms for identifying sensitive data across various repositories.
* **Classification Logic:** The taxonomy and methodology used to categorize information.
* **Labeling Framework:** The application of persistent metadata to files and emails.
* **Protection Actions:** Encryption, access restrictions, and visual markings.
* **Data Lifecycle Management:** How protection persists from creation to deletion.

**Out of scope:**
* **Specific Vendor Implementations:** Detailed "how-to" guides for specific software versions or UI navigation.
* **Licensing and Pricing:** Commercial tiers or subscription models.
* **Hardware-level Security:** Physical server security or disk-level encryption (BitLocker).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Sensitivity Label** | A persistent metadata tag applied to content that indicates its level of sensitivity and triggers specific protection actions. |
| **Sensitive Information Type (SIT)** | A pattern-based classifier (e.g., regex, keywords, checksums) used to identify specific data types like credit card numbers or tax IDs. |
| **Classifiers** | Automated engines (including machine learning/trainable classifiers) that identify content based on context rather than just patterns. |
| **Encryption** | The process of encoding information so that only authorized parties with the correct cryptographic keys can access it. |
| **Rights Management (RMS)** | A technology that uses encryption and policy-based permissions to restrict how a user can interact with a document (e.g., View Only, No Print). |
| **Content Explorer** | A centralized view for administrators to see the distribution and classification of data across the environment. |
| **Policy** | A set of rules that defines which labels are available to which users and how they are applied (manually or automatically). |

## Core Concepts

### 1. Data-Centric Security
Unlike traditional perimeter security, Information Protection attaches security properties directly to the data. If a protected file is moved from a secure cloud environment to an unmanaged USB drive, the protection (encryption and access rights) remains embedded in the file metadata.

### 2. The Metadata-Driven Framework
The system relies on metadata "labels." These labels are stored in cleartext within the file properties (for supported formats like OOXML) or as sidecar attributes. This allows third-party systems (DLP, CASB, Firewalls) to read the classification and enforce secondary actions without needing to decrypt the content.

### 3. Persistent Protection
Protection is enforced via the Azure Rights Management service. When a user opens a protected document, the application checks the user's identity against the policy embedded in the file's header. This ensures that access is validated at the time of consumption, not just at the time of transit.

### 4. Classification Taxonomy
Organizations define a hierarchy of sensitivity (e.g., Public > General > Confidential > Highly Confidential). This taxonomy provides a common language for both human users and automated systems to understand the value and risk associated with a specific piece of information.

## Standard Model

The standard model for implementing Microsoft Purview Information Protection follows a four-stage iterative lifecycle:

1.  **Know Your Data:** Use automated discovery to understand what sensitive data exists in the environment (on-premises, cloud, and endpoints).
2.  **Protect Your Data:** Apply sensitivity labels that include encryption, digital rights management, and visual markings (headers, footers, watermarks).
3.  **Prevent Data Loss:** Implement policies that detect and block the unauthorized sharing or transfer of labeled or sensitive data.
4.  **Govern Your Data:** Monitor data usage, audit access, and refine policies based on observed behavior and changing regulatory requirements.

## Common Patterns

*   **Manual Labeling:** Empowering users to choose the appropriate label based on their knowledge of the content. This is often used for subjective data.
*   **Default Labeling:** Automatically applying a baseline label (e.g., "General") to all new content to ensure a minimum level of classification.
*   **Auto-Labeling (Service-Side):** Using scanning engines to apply labels to data at rest in repositories like SharePoint or OneDrive based on SITs.
*   **Auto-Labeling (Client-Side):** Real-time scanning of content as a user types in an application (e.g., Word or Outlook), suggesting or mandating a label if sensitive patterns are detected.

## Anti-Patterns

*   **Over-Classification:** Creating too many labels or sub-labels, leading to "decision paralysis" for users and inconsistent data categorization.
*   **Labeling as Folder Management:** Using sensitivity labels to organize files by project or department rather than by the actual sensitivity of the information.
*   **Encryption Without Recovery:** Implementing strict encryption policies without a "break-glass" or "super-user" strategy, risking permanent data loss if the original owner leaves the organization.
*   **Set and Forget:** Deploying labels without ongoing auditing, leading to "stale" classifications that no longer reflect the data's actual risk.

## Edge Cases

*   **Non-Supported File Types:** While Office and PDF files have robust support, legacy formats or proprietary CAD/binary files may not support embedded metadata, requiring "container-level" protection.
*   **B2B Collaboration:** Sharing protected data with external partners requires identity federation or "one-time passcode" (OTP) mechanisms, which can fail if the partner's environment blocks specific authentication protocols.
*   **Double Key Encryption (DKE):** For highly regulated data, organizations may use two keys—one held by the vendor and one by the customer. If the customer loses their key, the data is unrecoverable even by the service provider.
*   **Offline Access:** Users may need to access protected content without an internet connection. Policies must define "offline lease" periods, balancing usability with the risk of delayed access revocation.

## Related Topics

*   **Data Loss Prevention (DLP):** The enforcement layer that uses Information Protection labels to block or alert on data egress.
*   **Data Lifecycle Management:** The practice of retaining or deleting data, which often uses the same classification engine.
*   **Zero Trust Architecture:** The broader security strategy where Information Protection serves as the "Verify Explicitly" pillar for data.
*   **eDiscovery:** The process of searching and retrieving labeled data for legal or regulatory investigations.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2025-05-22 | Initial canonical documentation defining the framework and core concepts. |