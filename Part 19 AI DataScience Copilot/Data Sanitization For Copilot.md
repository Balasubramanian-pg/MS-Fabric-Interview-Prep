# [Data Sanitization For Copilot](Part 19 AI DataScience Copilot/Data Sanitization For Copilot.md)

Canonical documentation for [Data Sanitization For Copilot](Part 19 AI DataScience Copilot/Data Sanitization For Copilot.md). This document defines concepts, terminology, and standard usage.

## Purpose
Data Sanitization for Copilot addresses the critical intersection of Large Language Model (LLM) utility and data governance. As AI assistants (Copilots) integrate deeply into enterprise workflows, they require access to vast amounts of organizational data to provide relevant context. However, this data often contains sensitive information, noise, or malicious payloads.

The purpose of data sanitization is to ensure that information provided to an LLM—whether through prompts, retrieval-augmented generation (RAG), or fine-tuning—is stripped of unauthorized sensitive elements while maintaining the semantic integrity required for the model to function effectively. This process mitigates risks related to data privacy breaches, regulatory non-compliance, and security vulnerabilities.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Privacy Protection:** Methods for identifying and neutralizing PII, PHI, and PCI.
* **Security Scrubbing:** Removal of secrets, API keys, and potential injection vectors.
* **Data Distillation:** Reducing noise to optimize token usage and model focus.
* **Transformation Logic:** Theoretical frameworks for redaction, anonymization, and pseudonymization.

**Out of scope:**
* **Specific vendor implementations:** (e.g., specific configurations for Microsoft Purview, Amazon Macie, or Google Cloud DLP).
* **Model Training:** The internal weights or architecture of the LLM itself.
* **Network Security:** Firewalls or transport layer security (TLS) protocols.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **PII** | Personally Identifiable Information; any data that can be used to identify a specific individual. |
| **Redaction** | The permanent removal or masking of sensitive information from a dataset. |
| **Anonymization** | The process of transforming data such that the subjects can no longer be identified, even by the data provider. |
| **Pseudonymization** | Replacing private identifiers with fake identifiers (pseudonyms) to allow for data linkage without revealing identity. |
| **Prompt Injection** | A security vulnerability where malicious inputs trick the LLM into executing unauthorized commands or revealing restricted data. |
| **Token Optimization** | The process of removing irrelevant data to ensure the most critical information fits within the model's context window. |
| **De-identification** | A general term for the process of removing the association between a set of data and the subject of that data. |

## Core Concepts

### 1. Semantic Preservation
The primary challenge of sanitization for Copilots is maintaining the "meaning" of the data. Unlike traditional database scrubbing, where a field can simply be deleted, an LLM needs the surrounding context to provide an accurate response. Sanitization must balance security with the preservation of the narrative or logical flow.

### 2. The Privacy-Utility Tradeoff
As the rigor of sanitization increases (e.g., moving from simple redaction to full anonymization), the utility of the data for the AI often decreases. Effective sanitization strategies seek the "Pareto optimal" point where risk is minimized without rendering the Copilot ineffective.

### 3. Multi-Layered Filtering
Sanitization is not a single event but a pipeline. It occurs at three primary stages:
*   **Ingestion-time:** Cleaning data before it enters a vector database or index.
*   **Request-time:** Scrubbing the user's prompt before it reaches the model.
*   **Response-time:** Filtering the model's output to ensure it hasn't hallucinated or leaked sensitive data from its training set.

## Standard Model

The standard model for Copilot Data Sanitization follows a linear pipeline:

1.  **Detection:** Utilizing Regular Expressions (Regex), Named Entity Recognition (NER), and checksum validation to identify sensitive patterns (emails, credit cards, social security numbers).
2.  **Classification:** Categorizing the detected data based on sensitivity levels (e.g., Public, Internal, Confidential, Highly Restricted).
3.  **Transformation:** Applying a sanitization technique based on the classification:
    *   *Masking:* Replacing `123-456-7890` with `XXX-XXX-XXXX`.
    *   *Generalization:* Replacing "Age 24" with "Age 20-30".
    *   *Synthetic Replacement:* Replacing a real name with a generic placeholder like `[USER_A]`.
4.  **Verification:** A secondary pass to ensure no "residual identifiers" remain that could allow for re-identification through inference.

## Common Patterns

### The "Placeholder" Pattern
Instead of deleting sensitive data, it is replaced with a descriptive placeholder (e.g., `<CITY_NAME>`). This allows the LLM to understand that a city was mentioned, preserving the grammatical structure and intent of the sentence.

### The "Differential Privacy" Pattern
Adding "noise" to a dataset so that statistical trends can be identified by the AI, but individual data points cannot be reconstructed.

### The "Scrub-and-Map" Pattern
Sensitive identifiers are replaced with tokens (e.g., `ID_001`). A secure, isolated mapping table is kept outside the AI's reach. If the AI generates a response referencing `ID_001`, a post-processing step swaps the real identifier back in for the human user.

## Anti-Patterns

*   **Hard-Coded Blacklists:** Relying solely on a list of "bad words" or known secrets. This fails to catch variations, typos, or new sensitive data types.
*   **Client-Side Only Sanitization:** Performing sanitization only in the user interface. Malicious actors can bypass the UI to send raw, unsanitized prompts directly to the API.
*   **Over-Redaction:** Scrubbing so much data that the LLM loses context, leading to "I don't know" responses or increased hallucinations as the model tries to fill in the gaps.
*   **Regex-Only Detection:** Relying purely on pattern matching for complex entities like names or addresses, which often requires context-aware Named Entity Recognition (NER).

## Edge Cases

*   **Code and Scripts:** Sanitizing data that contains code snippets is difficult, as removing "secrets" (like API keys) might break the syntax, making the code unreadable or non-functional for the Copilot's analysis.
*   **Inference Attacks:** An LLM might combine several pieces of non-sensitive information to infer a sensitive fact (e.g., combining "The CEO of the only tech company in Scranton" to identify a specific person).
*   **Multilingual Data:** Sanitization logic that works for English may fail for languages with different syntax or character sets (e.g., CJK languages), leading to leaked PII.
*   **Image/OCR Data:** When a Copilot processes images or PDFs, sanitization must occur at the OCR (Optical Character Recognition) level before the text is passed to the LLM.

## Related Topics

*   **Retrieval-Augmented Generation (RAG):** The architecture most commonly requiring robust data sanitization.
*   **Prompt Engineering:** The practice of crafting inputs to minimize the risk of data leakage.
*   **Data Sovereignty:** Legal requirements regarding where data is stored and processed, which dictates sanitization rigor.
*   **Zero-Trust Architecture:** A security model that assumes no entity is inherently trusted, necessitating sanitization at every boundary.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |