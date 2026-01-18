# [Dataflow Gen2 Looping](Part 20 EdgeCases BossLevel/Dataflow Gen2 Looping.md)

Canonical documentation for [Dataflow Gen2 Looping](Part 20 EdgeCases BossLevel/Dataflow Gen2 Looping.md). This document defines concepts, terminology, and standard usage.

## Purpose
The purpose of looping within a Dataflow Gen2 environment is to facilitate the iterative processing of data subsets, parameters, or metadata structures. In modern data engineering, static transformations are often insufficient for handling dynamic datasets where the number of inputs, files, or API pages is unknown at design time. Looping provides the mechanism to apply a uniform set of transformation logic across a collection of items, ensuring scalability and reducing manual pipeline maintenance.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative. While Dataflow Gen2 often resides within specific cloud ecosystems, the principles of iterative data processing described here apply to the underlying engine's logic.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core logic of iterative execution (Internal vs. External).
* Theoretical boundaries of recursion and list-based iteration.
* State management across iterations.
* Performance implications of iterative processing in a distributed environment.

**Out of scope:**
* Specific vendor UI navigation or button-clicking instructions.
* Language-specific syntax (e.g., specific M-code or Python snippets) unless used for conceptual illustration.
* Licensing or pricing models for specific cloud providers.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Iteration** | A single execution of a specific set of instructions within a loop. |
| **Orchestration Loop** | A loop controlled by an external service that triggers the dataflow multiple times with different parameters. |
| **Functional Iteration** | A loop contained within the dataflow engine itself, typically using list-based transformations or recursion. |
| **Recursion** | A process where a function calls itself to solve a smaller instance of the same problem until a base case is met. |
| **State** | The data or context preserved and passed between successive iterations. |
| **Batching** | The grouping of data into subsets to be processed iteratively to manage memory or API limits. |
| **Idempotency** | The property where an iterative process can be executed multiple times without changing the result beyond the initial application. |

## Core Concepts

### 1. Declarative vs. Imperative Iteration
Dataflow engines are primarily declarative, focusing on the "what" rather than the "how." Looping introduces imperative logic into this environment. 
* **Declarative Looping:** Expressed as a transformation over a set (e.g., "Apply this logic to all items in this list").
* **Imperative Looping:** Expressed as a sequence of steps (e.g., "While X is true, do Y").

### 2. Internal vs. External Control Flow
Looping can occur at two distinct layers:
* **Internal (Engine Level):** The iteration happens within the dataflow's execution plan. This is highly efficient for data transformations but limited by the engine's memory and timeout constraints.
* **External (Orchestration Level):** An external pipeline calls the dataflow repeatedly. This is better for long-running tasks or when each iteration requires a fresh compute context.

### 3. Immutability in Looping
In a functional dataflow environment, data is generally immutable. Each iteration does not "change" the original data but rather produces a new version of the dataset. Understanding this is critical for managing memory and performance.

## Standard Model
The standard model for Dataflow Gen2 looping follows the **List-Transform-Combine** pattern:

1.  **Generate/Identify List:** Define the collection of items to iterate over (e.g., a list of dates, file paths, or ID ranges).
2.  **Define Transformation Logic:** Create a modular function or step that accepts a single item from the list as an input.
3.  **Iterative Application:** The engine applies the transformation logic to each item in the list. This may happen sequentially or in parallel depending on the engine's optimization.
4.  **Aggregation:** The results of all iterations are combined into a single unified output (e.g., a table or a consolidated file).

## Common Patterns

### Dynamic Parameterization
The dataflow is designed with parameters. An external loop passes different values (such as "Region" or "FiscalYear") to the dataflow, allowing one logic definition to serve multiple data segments.

### Recursive Hierarchy Flattening
Used when dealing with self-referencing data (e.g., Employee/Manager tables). The loop continues to join the table to itself until no further parent-child relationships are found.

### Paginated API Consumption
A loop that checks for a "NextPage" token in an API response. If the token exists, the loop triggers another request, appending the results until the token is null.

## Anti-Patterns

### Row-by-Row Processing (RBAR)
Attempting to loop through every individual row in a large dataset using iterative logic rather than set-based operations. This leads to extreme performance degradation.

### Deep Recursion without Base Case
Implementing recursive functions without a guaranteed exit condition, leading to stack overflow errors or infinite compute consumption.

### Stateful Dependency Loops
Designing a loop where Iteration $N$ depends on the output of Iteration $N-1$ in a way that prevents parallelization. This negates the benefits of distributed dataflow engines.

## Edge Cases

*   **Empty Iteration Sets:** If the list of items to iterate over is empty, the system must be designed to return an empty schema-compliant structure rather than failing.
*   **Schema Drift during Iteration:** When iterating over multiple files, if one file has a different schema than the others, the "Combine" step may fail or result in null-heavy datasets.
*   **Token Expiration:** In long-running loops (e.g., API pagination), authentication tokens may expire mid-loop, requiring logic to refresh credentials within the iterative process.
*   **Partial Success:** A scenario where 99 out of 100 iterations succeed. The system must define whether to commit the 99 or roll back the entire operation.

## Related Topics
* **Dataflow Orchestration:** The management of execution sequences.
* **Parameterization:** The use of variables to drive dynamic logic.
* **Set-based Operations:** The preferred alternative to looping for bulk data.
* **Error Handling and Retries:** Mechanisms for managing failed iterations.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-18 | Initial AI-generated canonical documentation |