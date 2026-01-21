# What Is The Foreach Activity

Canonical documentation for What Is The Foreach Activity. This document defines concepts, terminology, and standard usage.

## Purpose
The Foreach Activity exists to provide a structured control flow mechanism for repeating a defined set of operations across a collection of discrete items. It addresses the problem of processing variable-length datasets where the specific number of iterations is unknown at design time. By decoupling the logic of the task from the volume of the data, it enables scalable, automated, and consistent processing of multiple entities (such as files, database records, or API responses) within a single workflow definition.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
* Core logic of collection-based iteration.
* Execution modes (sequential vs. parallel).
* Scope and variable isolation within iterations.
* Lifecycle of an iterative control flow.

**Out of scope:**
* Specific vendor implementations (e.g., Azure Data Factory, AWS Step Functions, Power Automate).
* Programming language-specific syntax (e.g., C# `foreach`, Python `for`).
* Condition-based loops (e.g., `While` or `Until` loops).

## Definitions
Provide precise definitions for key terms.

| Term | Definition |
|------|------------|
| **Collection** | An ordered or unordered set of data elements provided as input to the activity. |
| **Item** | A single element within a collection representing the current context of an iteration. |
| **Iteration** | A single execution cycle of the activity's internal logic for one specific item. |
| **Parallelism** | The simultaneous execution of multiple iterations to reduce total processing time. |
| **Sequential Execution** | The execution of iterations one after another, where each must complete before the next begins. |
| **Payload** | The data or logic contained within the body of the Foreach Activity that is executed for every item. |
| **Iterator Variable** | A local reference or pointer used within the payload to access the properties of the current item. |

## Core Concepts

### 1. Collection-Driven Logic
Unlike a "For" loop that typically relies on a numeric index or a "While" loop that relies on a boolean condition, the Foreach Activity is driven by the existence of data. If the input collection contains $N$ items, the activity is conceptually defined to trigger $N$ times.

### 2. The Execution Body
The Foreach Activity acts as a container. The "Body" or "Payload" consists of one or more activities that are treated as a single unit of work. This unit is instantiated for every item in the collection.

### 3. Scope Isolation
Each iteration should ideally operate within its own scope. While iterations may share global state, the primary design of a Foreach Activity ensures that the specific item data for "Iteration A" does not leak into "Iteration B."

## Standard Model

The standard model for a Foreach Activity follows a four-stage lifecycle:

1.  **Initialization:** The activity receives an input collection. The system validates that the input is iterable (e.g., an array or list).
2.  **Evaluation:** The system determines the execution strategy (Sequential or Parallel). If parallel, it determines the "Batch Count" or "Concurrency Limit."
3.  **Execution:**
    *   The iterator variable is bound to the current item.
    *   The internal payload activities are triggered.
    *   The system monitors the success or failure of the internal activities.
4.  **Termination:** The activity concludes once all items have been processed or a failure threshold is met. It then returns an aggregate status to the parent workflow.

## Common Patterns

### Fan-Out / Fan-In
The Foreach Activity is frequently used to "Fan-Out" work. A single process identifies a list of tasks (e.g., 1,000 files to move), and the Foreach Activity distributes these tasks. A subsequent activity "Fans-In" by summarizing the results once the Foreach completes.

### Batching
In scenarios with high-volume collections, the Foreach Activity is configured to process items in batches (e.g., 50 at a time) to prevent resource exhaustion in the underlying infrastructure.

### Item Filtering
A pattern where the first activity inside the Foreach body is a conditional check. If the item does not meet certain criteria, the rest of the payload is skipped for that specific iteration.

## Anti-Patterns

### Shared State Contention
Attempting to write to the same global variable or resource from multiple parallel iterations. This leads to race conditions and unpredictable data states.

### Excessive Nesting
Placing multiple Foreach Activities inside one another (Deep Nesting). This exponentially increases complexity and makes debugging and error handling significantly more difficult.

### Overloading the Body
Placing too many complex activities inside the Foreach body. This can lead to long-running iterations that are difficult to recover if a transient failure occurs near the end of the sequence.

### Ignoring Item Failures
Designing a Foreach Activity that continues to the next item upon failure without logging or handling the error, leading to "silent" data loss or incomplete processing.

## Edge Cases

*   **Empty Collections:** When the input collection is empty, the Foreach Activity should complete successfully with zero iterations. It should not trigger an error unless explicitly configured to require at least one item.
*   **Null Inputs:** If the input collection is `null` rather than an empty list, the activity must define whether it fails or treats it as an empty collection.
*   **Collection Modification:** If the collection being iterated upon is modified by the activities *inside* the loop, the behavior is often undefined and can lead to infinite loops or skipped items.
*   **Large Payloads:** If the collection contains millions of items, the overhead of managing the iteration state may exceed the memory limits of the orchestrator.

## Related Topics
*   **Control Flow:** The broader category of activities that manage the order of execution.
*   **Parallelism and Concurrency:** Concepts governing the simultaneous execution of tasks.
*   **Error Handling Patterns:** Strategies for managing failures within iterative loops (e.g., Continue on Error vs. Fail Fast).
*   **Array Manipulation:** The methods used to prepare collections for the Foreach Activity.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-14 | Initial AI-generated canonical documentation |