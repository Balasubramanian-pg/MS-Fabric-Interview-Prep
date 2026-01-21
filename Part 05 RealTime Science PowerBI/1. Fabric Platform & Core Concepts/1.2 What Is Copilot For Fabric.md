# [What Is Copilot For Fabric](Part 05 RealTime Science PowerBI/What Is Copilot For Fabric.md)

Canonical documentation for [What Is Copilot For Fabric](Part 05 RealTime Science PowerBI/What Is Copilot For Fabric.md). This document defines concepts, terminology, and standard usage.

## Purpose
Copilot for Fabric exists to bridge the gap between natural language intent and technical execution within a unified data ecosystem. It addresses the increasing complexity of data estates by providing an intelligent assistance layer that accelerates the data-to-insights lifecycle. By leveraging Large Language Models (LLMs) integrated directly into data workflows, it reduces the cognitive load on data professionals, democratizes data access for non-technical users, and standardizes code generation across various analytical engines.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative regarding the functional architecture and theoretical application of AI assistance within the Fabric framework.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Generative Assistance:** The application of LLMs to generate code (SQL, DAX, Python), summaries, and visualizations.
* **Contextual Grounding:** The mechanism by which the AI understands the specific metadata and schema of a user's environment.
* **Human-in-the-loop (HITL):** The requirement for human verification of AI-generated outputs.
* **Unified Data Governance:** How AI assistance adheres to existing security and privacy boundaries within the platform.

**Out of scope:**
* **General Purpose AI:** Generic chatbots or LLMs not integrated into the data platform's specific engines.
* **Hardware Specifications:** The underlying GPU or infrastructure requirements for running the models.
* **Third-party Plugins:** External AI tools not native to the Fabric ecosystem.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Copilot** | An integrated AI assistant that uses LLMs to help users perform tasks through natural language interaction. |
| **Fabric** | A unified SaaS data platform that integrates data engineering, data science, data warehousing, and business intelligence. |
| **Grounding** | The process of providing the LLM with specific, relevant context (such as table schemas or metadata) to ensure accuracy and relevance. |
| **Semantic Link** | A bridge that allows the AI to understand the relationships between data models and code-based data structures. |
| **Prompt Engineering** | The practice of crafting specific inputs to guide the AI toward a desired, high-quality output. |
| **Hallucination** | A phenomenon where the AI generates syntactically correct but factually incorrect or non-existent code or data insights. |

## Core Concepts

### 1. Contextual Awareness
Copilot for Fabric does not operate in a vacuum. Its primary value is derived from its ability to "read" the environment. This includes understanding the active workspace, the specific schema of a Lakehouse or Warehouse, and the existing measures in a Power BI semantic model.

### 2. Multi-Engine Integration
The assistant is designed to be polymorphic, adapting its output based on the specific workload:
* **Data Engineering/Science:** Generates Python/PySpark code within notebooks.
* **Data Warehousing:** Generates T-SQL for data manipulation and querying.
* **Business Intelligence:** Generates DAX (Data Analysis Expressions) and creates visual report layouts.

### 3. Security and Privacy Boundaries
The AI operates within the existing security context of the user. It cannot access data that the user does not have permission to view. Furthermore, the data used for grounding is typically not used to train the global foundational models, ensuring enterprise data remains isolated.

## Standard Model

The standard model for Copilot for Fabric follows a **Request-Augment-Generate-Validate** cycle:

1.  **Request:** The user provides a natural language prompt (e.g., "Show me total sales by region for the last quarter").
2.  **Augment (Grounding):** The system retrieves relevant metadata (table names, column types, relationships) from the Fabric environment.
3.  **Generate:** The LLM processes the prompt and the metadata to produce a technical artifact (code, query, or visual).
4.  **Validate:** The user reviews the output, executes it, and provides feedback or refinement.

## Common Patterns

*   **Exploratory Data Analysis (EDA):** Using the assistant to quickly summarize a new dataset or identify outliers without writing manual boilerplate code.
*   **Code Translation:** Converting logic from one language to another (e.g., translating a SQL query into a PySpark DataFrame operation).
*   **Narrative Generation:** Automatically creating textual summaries of complex data visualizations to make reports more accessible.
*   **Boilerplate Acceleration:** Generating standard data ingestion or transformation scripts that follow organizational naming conventions.

## Anti-Patterns

*   **Blind Execution:** Running AI-generated code or queries without manual review. This can lead to data corruption or incorrect business decisions.
*   **Ambiguous Prompting:** Providing vague instructions (e.g., "Fix my data") without specifying the target table or the desired outcome.
*   **Over-Reliance for Logic:** Using the AI to define complex business logic that should be governed by domain experts and documented in a centralized semantic layer.
*   **Data Leakage via Prompts:** Including sensitive PII (Personally Identifiable Information) directly in the text of a prompt.

## Edge Cases

*   **Schema Ambiguity:** When two tables have similar names (e.g., `Sales_2023` and `Sales_Final`), the AI may select the wrong source without explicit clarification.
*   **Complex DAX Logic:** Highly nested or performance-sensitive DAX measures may be generated with suboptimal syntax that requires manual tuning for large datasets.
*   **Non-Standard Libraries:** In notebooks, the AI may suggest Python libraries that are not pre-installed in the Fabric environment, leading to execution errors.
*   **Language Nuance:** Regional dialects or industry-specific jargon may lead to misinterpretation of the user's intent.

## Related Topics

*   **Microsoft Fabric Architecture:** The underlying platform that hosts the Copilot.
*   **Generative AI Governance:** The broader framework for managing AI usage within an organization.
*   **OneLake:** The centralized data lake where the data being queried by Copilot resides.
*   **Power BI Semantic Models:** The logical layer that Copilot uses to generate business insights.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |