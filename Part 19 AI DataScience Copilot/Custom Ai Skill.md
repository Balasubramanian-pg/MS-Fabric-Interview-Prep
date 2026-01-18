# [Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md)

Canonical documentation for [Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md). This document defines concepts, terminology, and standard usage.

## Purpose
The [Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md) exists to provide a modular, reusable, and encapsulated unit of specialized intelligence within an AI ecosystem. While general-purpose Large Language Models (LLMs) possess broad reasoning capabilities, they often lack the specific domain logic, data constraints, or operational guardrails required for enterprise-grade tasks. 

[Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md)s address the need for functional abstraction, allowing developers and system architects to define specific "capabilities" that can be invoked by an orchestrator, an agent, or a user. This approach moves AI development away from monolithic prompting toward a component-based architecture where discrete tasks—such as data extraction, sentiment analysis, or complex calculation—are isolated and optimized.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Functional Encapsulation:** The definition of boundaries for specific AI-driven tasks.
* **Interface Standards:** The conceptual inputs and outputs required for skill execution.
* **Grounding and Context:** The theoretical framework for providing skills with domain-specific knowledge.
* **Lifecycle Management:** The stages of a skill from definition to retirement.

**Out of scope:**
* **Specific vendor implementations:** (e.g., OpenAI GPTs, Microsoft Copilot Studio, AWS Bedrock Agents).
* **Hardware requirements:** Infrastructure-level specifications for hosting models.
* **Programming language syntax:** Specific code snippets for calling APIs.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Skill** | A discrete, modular unit of AI logic designed to perform a specific task or set of related tasks. |
| **Orchestrator** | The central logic layer that determines which skill to invoke based on user intent or system state. |
| **Grounding** | The process of linking a skill to verifiable, external data sources to ensure accuracy and relevance. |
| **Manifest** | A metadata definition describing the skill's purpose, required inputs, and expected output format. |
| **Few-Shot Examples** | A set of input-output pairs provided within the skill definition to guide the AI's behavior. |
| **Temperature** | A hyperparameter defining the randomness or creativity of the skill's output. |
| **Tooling** | External functions or APIs that a skill is authorized to call to perform actions in the physical or digital world. |

## Core Concepts

### 1. Encapsulation
A [Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md) must be self-contained. It should include its own instructions (system prompts), data schemas, and constraints. This ensures that the skill behaves consistently regardless of the broader system context in which it is deployed.

### 2. Deterministic vs. Probabilistic Balance
While the underlying engine of a skill is often probabilistic (AI-based), a [Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md) aims to introduce a degree of predictability. This is achieved through rigorous schema enforcement and structured output (e.g., JSON or XML).

### 3. Statelessness
Ideally, a skill should be stateless, meaning it processes the current input based on its internal configuration without relying on hidden historical data, unless that history is explicitly passed as part of the input context.

### 4. Intent Mapping
The process by which a system identifies that a specific [Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md) is the correct tool to address a specific request.

## Standard Model
The standard model for a [Custom Ai Skill](Part 19 AI DataScience Copilot/Custom Ai Skill.md) follows a "Request-Process-Response" pipeline:

1.  **Invocation:** The orchestrator receives a trigger and identifies the relevant skill via its Manifest.
2.  **Contextualization:** The system injects relevant data (Grounding) and user-specific parameters into the skill's environment.
3.  **Execution:** The AI processes the input according to the skill's internal logic, constraints, and few-shot examples.
4.  **Validation:** The output is checked against the defined schema (e.g., ensuring a "Financial Analysis" skill actually returned a currency value).
5.  **Delivery:** The structured or natural language output is returned to the orchestrator or end-user.

## Common Patterns

### The Specialist Pattern
A skill focused on a narrow domain (e.g., "Legal Document Summarizer"). It uses highly specific terminology and strict formatting rules.

### The Router Pattern
A skill whose primary purpose is to analyze an input and determine which *other* skills should be invoked, acting as a sub-orchestrator.

### The Transformation Pattern
A skill designed to convert data from one format to another, such as turning unstructured meeting notes into a structured JIRA ticket schema.

### The Validation Pattern
A skill that acts as a "critic," reviewing the output of another skill or user input to ensure it meets quality or safety standards.

## Anti-Patterns

*   **The Swiss Army Knife:** Creating a single skill that attempts to handle too many disparate tasks, leading to "prompt bleed" and decreased accuracy.
*   **Hardcoded Logic in Prompts:** Embedding volatile data (like current dates or specific user names) directly into the skill's core instructions rather than passing them as dynamic context.
*   **Opaque Outputs:** Returning long strings of unstructured text when the downstream system requires structured data, leading to brittle integrations.
*   **Infinite Loops:** Designing skills that can call themselves or each other without a clear termination condition or "max depth" constraint.

## Edge Cases

*   **Ambiguous Intent:** When a user request maps equally well to two different skills. The system must have a tie-breaking logic or a clarification prompt.
*   **Token Overflow:** When the grounding data or context required for the skill exceeds the underlying model's context window.
*   **Model Drift:** When the underlying LLM is updated by the provider, causing a previously stable skill to produce different results or fail validation.
*   **Hallucination in Constraints:** When the AI acknowledges the skill's constraints but ignores them due to the strength of its pre-trained associations.

## Related Topics

*   **Retrieval-Augmented Generation (RAG):** The primary method for grounding skills in external data.
*   **Agentic Workflows:** The orchestration of multiple skills to achieve a complex goal.
*   **Prompt Engineering:** The craft of writing the internal instructions for a skill.
*   **Semantic Versioning:** The practice of versioning skills as they evolve to prevent breaking changes in the orchestrator.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |