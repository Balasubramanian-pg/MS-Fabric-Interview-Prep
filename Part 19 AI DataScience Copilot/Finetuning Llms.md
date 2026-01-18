# [Finetuning Llms](Part 19 AI DataScience Copilot/Finetuning Llms.md)

Canonical documentation for [Finetuning Llms](Part 19 AI DataScience Copilot/Finetuning Llms.md). This document defines concepts, terminology, and standard usage.

## Purpose
Finetuning exists to adapt a pre-trained Large Language Model (LLM) to specific domains, tasks, or behavioral constraints. While pre-training provides a model with broad linguistic capabilities and general world knowledge, it often lacks the precision, formatting, or specialized expertise required for production-grade applications. Finetuning addresses the "Alignment Problem" by narrowing the model's output distribution to match a target dataset or objective, thereby increasing utility and reliability in specialized contexts.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* **Methodologies:** Supervised Fine-Tuning (SFT), Parameter-Efficient Fine-Tuning (PEFT), and Preference Alignment.
* **Theoretical Boundaries:** The mechanics of weight updates, gradient descent in the context of LLMs, and the trade-offs between plasticity and stability.
* **Data Strategy:** Principles of dataset curation and formatting for specialized training.

**Out of scope:**
* **Specific Vendor Implementations:** Proprietary cloud platforms (e.g., OpenAI Fine-tuning API, AWS Bedrock) or specific software libraries (e.g., Hugging Face PEFT, DeepSpeed).
* **Hardware Orchestration:** Specific GPU/TPU cluster configurations or networking interconnects.
* **Prompt Engineering:** Techniques that do not involve updating model weights.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Base Model** | A model pre-trained on a massive, diverse corpus using self-supervised learning, lacking specific task alignment. |
| **Catastrophic Forgetting** | A phenomenon where a model loses its ability to perform general tasks or recall pre-trained knowledge after intensive finetuning on a narrow dataset. |
| **Supervised Fine-Tuning (SFT)** | The process of training a model on a curated dataset of prompt-response pairs to teach it specific behaviors or formats. |
| **PEFT** | Parameter-Efficient Fine-Tuning; techniques that update only a small subset of model parameters to reduce computational overhead. |
| **LoRA** | Low-Rank Adaptation; a specific PEFT method that injects trainable rank decomposition matrices into the transformer layers. |
| **Alignment** | The process of ensuring the model's outputs conform to human values, safety guidelines, and intended utility. |
| **Objective Function** | The mathematical function (e.g., Cross-Entropy Loss) that the finetuning process seeks to minimize. |
| **Learning Rate** | A hyperparameter that determines the step size at each iteration while moving toward a minimum of a loss function. |

## Core Concepts

### 1. Transfer Learning
Finetuning is a form of transfer learning. It assumes that the features and representations learned during pre-training (syntax, semantics, logic) are foundational and that only the "top-level" mapping needs adjustment to suit a specific application.

### 2. The Loss Surface
During finetuning, the model navigates a high-dimensional loss surface. Because the model is already in a "local minimum" (the pre-trained state), finetuning aims to find a nearby minimum that satisfies the new constraints of the specialized data without deviating so far that it loses general intelligence.

### 3. Weight Plasticity
This refers to the degree to which model weights can be modified. High plasticity allows for rapid learning of new domains but increases the risk of catastrophic forgetting. Low plasticity preserves general knowledge but may fail to capture the nuances of the target dataset.

## Standard Model

The generally accepted pipeline for developing a specialized LLM follows a three-stage progression:

1.  **Pre-training:** Self-supervised learning on trillions of tokens to create a Base Model.
2.  **Supervised Fine-Tuning (SFT):** Training on 10,000–100,000 high-quality instruction-following examples. This transforms a "base" model into an "assistant" model.
3.  **Alignment (RLHF/DPO):** Using Reinforcement Learning from Human Feedback (RLHF) or Direct Preference Optimization (DPO) to refine the model based on human rankings of output quality, safety, and helpfulness.

## Common Patterns

### Instruction Tuning
The most common pattern for general-purpose assistants. The dataset consists of a "System Prompt," a "User Query," and a "Desired Response." This teaches the model the conversational "turn-taking" structure.

### Domain Adaptation
Used for highly technical fields (e.g., legal, medical, or proprietary codebases). The model is exposed to raw text from the domain to learn specialized vocabulary and conceptual relationships before undergoing instruction tuning.

### Adapter-Based Tuning (PEFT)
Instead of updating all billions of parameters, "adapters" (small modules) are inserted between layers. Only these adapters are trained, while the original weights remain frozen. This allows for modularity and significantly lower hardware requirements.

## Anti-Patterns

*   **Data Contamination:** Including evaluation or test data within the finetuning set, leading to artificially high performance metrics that do not generalize to real-world usage.
*   **Overfitting on Small Datasets:** Using too many epochs on a small dataset, causing the model to memorize specific examples rather than learning the underlying logic.
*   **Ignoring the Base Model's "Nature":** Attempting to finetune a model to perform a task that is fundamentally at odds with its pre-training (e.g., forcing a model pre-trained only on English to perform high-level legal analysis in a language it barely knows).
*   **Opaque Evaluation:** Relying solely on "Loss" or "Perplexity" metrics without qualitative human evaluation or benchmark testing.

## Edge Cases

*   **Low-Resource Languages:** Finetuning for languages with limited pre-training representation often requires "vocabulary expansion" and specialized tokenization strategies to be effective.
*   **Extremely Long Contexts:** Finetuning a model to handle context windows significantly larger than its pre-trained limit (e.g., moving from 4k to 128k tokens) requires specialized positional encoding adjustments (e.g., RoPE scaling).
*   **Conflicting Knowledge:** When the finetuning data contradicts the pre-trained knowledge (e.g., a model pre-trained on the general internet being finetuned on a fictional universe's history). The model may "hallucinate" by blending the two realities.

## Related Topics
*   **Retrieval-Augmented Generation (RAG):** An alternative to finetuning for providing models with external, up-to-date knowledge without weight updates.
*   **Quantization:** The process of reducing the precision of model weights, often performed after finetuning to optimize deployment.
*   **Prompt Engineering:** The practice of optimizing inputs to elicit better performance without modifying model weights.
*   **Model Distillation:** Training a smaller "student" model to mimic the behavior of a larger "teacher" model that has already been finetuned.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |