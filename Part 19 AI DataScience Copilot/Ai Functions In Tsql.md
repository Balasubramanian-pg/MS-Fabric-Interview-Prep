# [Ai Functions In Tsql](Part 19 AI DataScience Copilot/Ai Functions In Tsql.md)

Canonical documentation for [Ai Functions In Tsql](Part 19 AI DataScience Copilot/Ai Functions In Tsql.md). This document defines concepts, terminology, and standard usage.

## Purpose
The integration of Artificial Intelligence (AI) functions within Transact-SQL (T-SQL) addresses the historical decoupling of relational data management and machine learning inference. Traditionally, data had to be extracted from the database (ETL/ELT) to external environments for AI processing. AI functions in T-SQL allow for "bringing the model to the data," enabling developers to perform complex tasks—such as natural language processing, sentiment analysis, and vector transformations—directly within the query engine. This reduces data movement, minimizes latency, and simplifies the application architecture.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative. While specific syntax may vary across database engines (e.g., SQL Server, Azure SQL, Fabric), the underlying principles and logical structures remain consistent.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Interface Logic:** The mechanism by which T-SQL interacts with AI models (internal or external).
* **Data Types:** The representation of AI-specific data structures (e.g., Vectors) within a relational schema.
* **Functional Categories:** Classification of AI operations (Generative, Analytical, Transformative).
* **Security and Governance:** The theoretical framework for managing AI access within a database context.

**Out of scope:**
* **Specific Vendor Implementations:** Proprietary syntax for specific cloud providers or database versions.
* **Model Training:** The process of creating or fine-tuning models (this is a data science concern, not a T-SQL concern).
* **Hardware Acceleration:** Specifics of GPU or NPU configurations.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Vector** | A numerical representation of data (text, image, audio) in a multi-dimensional space. |
| **Embedding** | The process (or result) of converting unstructured data into a vector that captures semantic meaning. |
| **Inference** | The act of applying a trained AI model to new data to generate a prediction, classification, or completion. |
| **LLM (Large Language Model)** | A type of AI trained on vast amounts of text data capable of generating human-like responses. |
| **Semantic Search** | A search technique that retrieves results based on the intent and contextual meaning of the query rather than keyword matching. |
| **RAG (Retrieval-Augmented Generation)** | An architectural pattern where a database provides relevant context to an AI model to improve the accuracy of its output. |
| **Token** | The basic unit of text (words or sub-words) processed by an AI model, often used for metering and cost calculation. |

## Core Concepts

### 1. The AI-Relational Bridge
AI functions in T-SQL act as a bridge between structured relational tables and unstructured model outputs. These functions are typically exposed as Scalar-Valued Functions (returning a single value like a sentiment score) or Table-Valued Functions (returning multiple rows or complex objects like a list of extracted entities).

### 2. Vectorization and Distance Metrics
A core component of AI in T-SQL is the ability to store and query vector data. This involves:
* **Storage:** Using specialized binary or array types to hold high-dimensional vectors.
* **Calculation:** Using mathematical functions (e.g., Cosine Similarity, Euclidean Distance) to compare the proximity of vectors within a SQL query.

### 3. Synchronous vs. Asynchronous Execution
* **Synchronous:** The SQL query waits for the AI model to respond before returning results. This is suitable for real-time applications but carries latency risks.
* **Asynchronous:** The query triggers an AI task and continues, with the result being captured later via a callback or status check.

### 4. Model Locality
* **In-Engine Models:** Models hosted directly within the database process (e.g., ONNX models). These offer the lowest latency and highest security.
* **Remote/External Models:** Models accessed via API (e.g., OpenAI, Hugging Face). These offer the most power but introduce network dependency and egress considerations.

## Standard Model

The standard model for AI functions in T-SQL follows a three-tier execution flow:

1.  **Context Preparation:** The T-SQL engine selects the relevant rows and prepares the input (e.g., concatenating columns into a prompt or converting text to a vector).
2.  **Inference Execution:** The engine invokes the AI function. If the model is external, the engine handles the HTTP request/response and authentication.
3.  **Result Integration:** The output of the AI model is cast back into T-SQL data types (VARCHAR, JSON, BIT, etc.) and integrated into the final result set or used for further filtering/joining.

## Common Patterns

### Semantic Search (Vector Search)
Using embeddings to find records that are "conceptually similar" to a user's query.
* *Pattern:* `SELECT TOP 10 * FROM Documents ORDER BY VECTOR_DISTANCE(Embedding, @QueryVector)`

### Data Enrichment
Automatically populating columns based on the content of other columns.
* *Pattern:* Using an AI function in a `TRIGGER` or `UPDATE` statement to summarize a long text field or detect the language of a record.

### Retrieval-Augmented Generation (RAG)
Using T-SQL to fetch the most relevant data points and passing them to an LLM function to generate a grounded response.
* *Pattern:* `SELECT AI_GENERATE(CONCAT('Answer based on this context: ', ContextData, ' Question: ', @UserQuery))`

## Anti-Patterns

*   **RBAR (Row-By-Agonizing-Row) Inference:** Calling an external AI API for every row in a multi-million row dataset within a single SELECT statement. This leads to extreme latency and potential rate-limiting.
*   **Prompt Injection via Table Data:** Failing to sanitize or delimit data stored in tables before passing it into a generative AI function, allowing stored data to "hijack" the model's instructions.
*   **Storing Sensitive Data in External Prompts:** Passing PII (Personally Identifiable Information) to external AI models without proper masking or enterprise-grade privacy agreements.
*   **Over-reliance on Non-Deterministic Outputs:** Using AI functions for critical logic where a 100% consistent, repeatable result is required (e.g., financial calculations).

## Edge Cases

*   **Token Limits:** If the input data exceeds the model's context window, the T-SQL function may truncate data or return an error, leading to incomplete analysis.
*   **Model Versioning:** If a remote model is updated or deprecated, the behavior of a T-SQL query may change unexpectedly without any changes to the SQL code itself.
*   **Collation and Encoding:** AI models often expect UTF-8 encoding. T-SQL environments using legacy collations may experience data corruption or "hallucinations" during the conversion process.
*   **Rate Limiting:** High-concurrency SQL environments may hit API throttles on external AI providers, causing query timeouts in production.

## Related Topics
* **Vector Databases:** Specialized systems designed primarily for vector storage.
* **JSON Processing in T-SQL:** Essential for parsing the complex outputs often returned by AI models.
* **Managed Identities/Secret Management:** The standard for securing API keys used by AI functions.
* **ONNX (Open Neural Network Exchange):** The standard format for portable machine learning models used in-engine.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |