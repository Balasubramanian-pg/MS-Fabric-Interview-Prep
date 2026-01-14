# What Is An Expression In Fabric Pipelines

Canonical documentation for What Is An Expression In Fabric Pipelines. This document defines concepts, terminology, and standard usage.

## Purpose
Expressions in Fabric Pipelines serve as the mechanism for dynamic configuration and runtime decision-making. In a data orchestration environment, static values are often insufficient for complex workflows. Expressions allow the pipeline to adapt to its environment by evaluating logic, manipulating data types, and referencing metadata from the execution context.

The primary purpose of an expression is to transform input data, system variables, or activity outputs into a usable value for a specific property at the moment of execution. This shifts the pipeline from a rigid, hard-coded sequence to a flexible, programmable workflow.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* The syntax and structural logic of expressions.
* The lifecycle of expression evaluation (from design-time to runtime).
* Data type handling and coercion within the expression engine.
* The relationship between expressions and the pipeline orchestration context.

**Out of scope:**
* Specific syntax for third-party scripting languages (e.g., Python, SQL) used within activities.
* Step-by-step UI tutorials for the Fabric Expression Builder.
* Performance tuning for specific cloud storage backends.

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Expression** | A combination of literals, operators, functions, and references that the orchestration engine evaluates to a single value. |
| **Evaluation** | The process of resolving an expression into its final result at runtime. |
| **Interpolation** | The practice of embedding expressions within a string literal, typically denoted by specific delimiters (e.g., `@{...}`). |
| **Literal** | A fixed value (string, number, boolean) that does not change during evaluation. |
| **Function** | A predefined operation that accepts arguments and returns a value (e.g., `concat()`, `utcnow()`). |
| **Context Reference** | A pointer to dynamic data available during execution, such as Pipeline Parameters, Variables, or Activity Outputs. |
| **Type Coercion** | The automatic or explicit conversion of a value from one data type to another during evaluation. |

## Core Concepts

### 1. Declarative Logic
Expressions in Fabric Pipelines are declarative. The user defines *what* the result should be based on available inputs, rather than writing the imperative steps to manage memory or CPU cycles. The orchestration engine handles the underlying execution logic.

### 2. Runtime Evaluation
Unlike static properties, expressions are evaluated at the moment the specific activity or trigger is invoked. This allows the pipeline to react to the state of the system, such as the success or failure of a previous task or the current timestamp.

### 3. The Expression Context
Every expression operates within a "Context." This context provides access to:
* **System Variables:** Metadata about the pipeline run (e.g., Run ID, Pipeline Name).
* **Parameters:** User-defined constants passed at the start of a run.
* **Variables:** Mutable state held during the pipeline execution.
* **Activity Outputs:** Data returned by previously completed activities in the same pipeline.

## Standard Model
The standard model for expressions follows a functional programming paradigm. An expression is structured as a tree of operations where the leaf nodes are constants or references, and the branches are functions or operators.

1.  **Trigger/Invocation:** The pipeline engine encounters a property marked as dynamic.
2.  **Reference Resolution:** The engine fetches the current values for any parameters or variables referenced.
3.  **Functional Execution:** Functions are executed in order of nesting (innermost to outermost).
4.  **Type Validation:** The final result is checked against the expected data type of the target property (e.g., a "Timeout" property must resolve to an Integer).
5.  **Assignment:** The resolved value is injected into the activity configuration.

## Common Patterns

### String Orchestration
Combining static text with dynamic metadata to generate file paths, table names, or connection strings. 
*Example: Generating a folder path based on the current date.*

### Conditional Parameterization
Using logical functions (e.g., `if`, `equals`, `or`) to change the behavior of an activity based on the output of a previous step.
*Example: Routing data to a "Error" folder if a row count is zero.*

### Type Conversion
Explicitly casting data types to ensure compatibility between different systems.
*Example: Converting a string-based timestamp from an API into a format compatible with a database.*

### Collection Iteration
Referencing the "item" context within an iterative loop (e.g., ForEach) to process a dynamic list of objects.

## Anti-Patterns

### Over-Nesting
Creating deeply nested functional expressions that are difficult to debug or maintain. 
*Correction: Break complex logic into multiple "Set Variable" activities to make the intermediate steps visible.*

### Hardcoding Environment Specifics
Including environment-specific strings (like server names or container paths) directly in expressions.
*Correction: Use Pipeline Parameters or Global Parameters to abstract environment-specific values.*

### Implicit Type Reliance
Relying on the engine to correctly guess a data type (e.g., assuming a string "100" will always be treated as an integer).
*Correction: Use explicit conversion functions (e.g., `int()`) to ensure predictable behavior.*

### Side-Effect Assumptions
Assuming that evaluating an expression will change the state of an external system. 
*Correction: Expressions should be treated as "pure functions" that only return a value; use Activities for state-changing operations.*

## Edge Cases

### Null and Empty Values
Evaluation of expressions involving `null` or empty strings can lead to unexpected results or pipeline failures. Robust expressions should include checks (e.g., `coalesce`) to provide default values.

### Circular References
An expression cannot reference a variable that is currently being set by that same expression, nor can it reference the output of an activity that has not yet executed. This results in a validation error.

### Large Payload References
Referencing the output of an activity that returns an exceptionally large JSON payload can cause memory pressure or latency during the evaluation phase.

### Escaping Delimiters
In scenarios where the interpolation delimiter (e.g., `@`) is required as a literal character in a string, it must be escaped (usually by doubling the character, `@@`) to prevent the engine from attempting to evaluate it.

## Related Topics
* **Pipeline Parameters:** The primary input mechanism for expressions.
* **Variables (Set/Append):** The mechanism for storing expression results for later use.
* **Control Flow Activities:** Activities (If Condition, Switch, ForEach) that rely heavily on expression evaluation.
* **Data Factory Expression Language:** The underlying syntax structure often utilized in these environments.

## Change Log

| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |