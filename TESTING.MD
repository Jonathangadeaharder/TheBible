# Testing Principles and Standards

This document outlines the standards for all automated testing. Adherence to these principles ensures code quality, reliability, and maintainability.

## Test Pyramid

Our testing strategy follows the test pyramid model, prioritizing tests that are fast, reliable, and isolated. The required distribution is:
- **Unit Tests:** Form the foundation.
- **Integration Tests:** Verify interactions between components.
- **End-to-End (E2E) Tests:** Validate critical user journeys.

## Test Execution Timeouts

To prevent stalled tests from blocking CI/CD pipelines, the following timeouts are standard:

*   **Unit Tests:** 1 minute (60,000 ms)
*   **Integration Tests:** 5 minutes (300,000 ms)
*   **End-to-End (E2E) Tests:** 10 minutes (600,000 ms)

## Test Coverage Thresholds

All new and modified code must meet these minimum coverage thresholds to ensure adequate testing:

*   **Line Coverage:** 80%
*   **Branch Coverage:** 70%

## Test Independence

Tests must be fully independent and isolated. They must not share state or depend on the execution order of other tests. Each test is responsible for its own setup and teardown.

## Test Length Constraints

Unit tests must be concise and focused:

*   **Maximum Length:** Unit tests should not exceed 10 lines of code (LOC).
*   **Extended Length:** Unit tests may extend to up to 20 LOC, but this requires a comment explaining why the test cannot be simplified while maintaining adequate coverage.
*   **Rationale:** Short tests are easier to understand, maintain, and debug. They also tend to be more focused on specific behaviors.

For tests exceeding 10 LOC, include a comment in this format:
```comment
# Extended test: [Reason why this test cannot be simplified while maintaining adequate coverage]
```