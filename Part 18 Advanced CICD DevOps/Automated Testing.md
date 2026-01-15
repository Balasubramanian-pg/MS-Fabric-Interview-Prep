# Automated Testing

Canonical documentation for Automated Testing. This document defines concepts, terminology, and standard usage.

## Purpose
Automated Testing exists to validate software requirements and functional correctness through the execution of scripted sequences. Its primary objective is to provide a repeatable, objective, and efficient feedback loop regarding the state of a system. By replacing manual verification with programmatic execution, automated testing addresses the problems of human error, regression risks in complex systems, and the scalability constraints of manual quality assurance.

> [!NOTE]
> This documentation is intended to be implementation-agnostic and authoritative.

## Scope
Clarify what is in scope and out of scope for this topic.

**In scope:**
*   The theoretical framework of automated verification.
*   Classification of test levels and types.
*   Methodologies for test design and execution.
*   Principles of test maintenance and reliability.

**Out of scope:**
*   Specific vendor implementations (e.g., Selenium, Playwright, JUnit).
*   Manual exploratory testing techniques.
*   Hardware-specific validation (unless abstracted via software).
*   Programming language-specific syntax.

## Definitions
| Term | Definition |
|------|------------|
| **Assertion** | A boolean expression that validates whether a specific condition or outcome matches the expected result. |
| **Test Suite** | A logical collection of test cases intended to be executed together to test a specific behavior or component. |
| **Test Runner** | The engine or library responsible for executing test scripts and reporting results. |
| **Determinism** | The property of a test where the same input and state always produce the identical output. |
| **Flakiness** | A state where a test yields both passing and failing results without changes to the code or environment. |
| **Mock/Stub** | Test doubles used to simulate the behavior of external dependencies or complex components. |
| **Regression** | A software bug that appears in a previously functioning feature after a change has been made. |
| **Code Coverage** | A metric measuring the percentage of source code executed during the running of a test suite. |

## Core Concepts

### Determinism and Idempotency
For an automated test to be valuable, it must be deterministic. A test should fail only when there is a defect or a deliberate change in requirements. Idempotency ensures that running a test multiple times—or in different orders—does not alter the outcome or the state of the system in a way that affects subsequent tests.

### The Feedback Loop
Automated testing is the cornerstone of the modern development lifecycle. The "tightness" of the feedback loop (the time between writing code and receiving a test result) directly correlates with development velocity.

### Isolation
Tests should be isolated from one another. The failure of one test should not cause subsequent tests to fail, nor should the setup of one test provide the necessary state for another.

### Observability
Automated tests must provide clear, actionable output. A failure should indicate not just *that* something went wrong, but *where* and *why*, typically through stack traces, logs, or state snapshots.

## Standard Model

### The Testing Pyramid
The generally accepted model for automated testing is the **Testing Pyramid**, which categorizes tests based on their granularity and execution cost:

1.  **Unit Tests (Base):** High volume, fast execution, testing individual functions or classes in isolation.
2.  **Integration Tests (Middle):** Moderate volume, testing the interaction between two or more modules or services.
3.  **End-to-End (E2E) / UI Tests (Top):** Low volume, slow execution, testing the entire system flow from the user's perspective.

The model suggests that the majority of tests should be unit tests, as they are the least expensive to maintain and the fastest to run.

## Common Patterns

### Arrange-Act-Assert (AAA)
A pattern for structuring individual test cases:
*   **Arrange:** Set up the necessary preconditions and inputs.
*   **Act:** Execute the specific function or behavior being tested.
*   **Assert:** Verify that the outcome matches expectations.

### Page Object Model (POM)
Common in UI automation, this pattern abstracts the interface of a web page or screen into an object. This decouples the test logic from the underlying UI structure, making tests more resilient to layout changes.

### Data-Driven Testing
A pattern where the same test logic is executed multiple times using different sets of input data and expected results, often stored in external tables or files.

## Anti-Patterns

### The Ice Cream Cone (Inverted Pyramid)
A scenario where a project has a massive suite of slow, brittle E2E tests and very few unit tests. This leads to high maintenance costs and slow feedback.

### Brittle Tests
Tests that fail due to insignificant changes in the system (e.g., changing a CSS class name or a non-functional string) rather than actual logic errors.

### Hardcoded Delays (Sleeps)
Using fixed time delays to wait for asynchronous processes. This leads to "flaky" tests and unnecessarily long execution times. The preferred approach is **Conditional Waiting**.

### Testing Implementation Details
Writing tests that verify *how* a piece of code works rather than *what* it does. This makes refactoring difficult, as the tests fail even when the external behavior remains correct.

## Edge Cases

### Non-Deterministic Environments
Testing in environments where external factors (network latency, third-party API availability, or system clock) cannot be fully controlled. This requires the use of service virtualization or sophisticated mocking.

### Race Conditions
Scenarios where the outcome of a test depends on the sequence or timing of uncontrollable events. These are notoriously difficult to reproduce and automate reliably.

### State Persistence
Tests that interact with a database or file system must ensure the state is reset. Failure to do so can lead to "leaking" state, where a failure in one test run causes a failure in a completely unrelated run later.

## Related Topics
*   **Continuous Integration / Continuous Deployment (CI/CD):** The pipeline infrastructure that triggers automated tests.
*   **Test-Driven Development (TDD):** A methodology where tests are written before the functional code.
*   **Behavior-Driven Development (BDD):** An extension of TDD that uses natural language to define test scenarios.
*   **Mutation Testing:** A method of testing the quality of the tests themselves by injecting faults into the source code.

## Change Log
| Version | Date | Description |
|---------|------|-------------|
| 1.0 | 2026-01-15 | Initial AI-generated canonical documentation |