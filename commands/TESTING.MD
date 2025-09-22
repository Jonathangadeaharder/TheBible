# Local Testing Standards Validation

## Objective

This command ensures your code complies with the testing standards defined in `/TESTING.MD`. It scans, validates, and reports deviations in test files to help you maintain quality standards locally.

## Usage

Run this command before committing code to validate your tests meet all standards:
```bash
./validate-tests.sh
```

Or as part of your local development workflow:
```bash
npm run validate-tests
```

---

## Validation Checks

### 1. Identify Test Scopes

**Action:**
- Scan the current directory to identify all test files (e.g., `*.test.js`, `*_test.py`, `*Tests.cs`).
- Identify the corresponding source code files to determine the scope for coverage analysis.

### 2. Validate Test Timeouts

**Action:**
- Parse the identified test files to extract the timeout configured for each test case.
- Compare the extracted timeout against the standard values.

**Validation Rules:**
- Unit Test Timeout <= `60000` ms
- Integration Test Timeout <= `300000` ms
- E2E Test Timeout <= `600000` ms

**Feedback:**
- Reports any tests that exceed timeout limits with file names and line numbers.
- Suggests reducing test complexity or increasing timeout with justification.

### 3. Analyze Test Coverage

**Action:**
- Execute the test suite for the current project.
- Generate a test coverage report for the source code.

**Validation Rules:**
- Line Coverage >= `80%`
- Branch Coverage >= `70%`

**Feedback:**
- Reports coverage statistics for each file.
- Highlights specific files and lines that lack coverage and require additional tests.
- Suggests areas where new tests would be most beneficial.

### 4. Verify Test Independence

**Action:**
- Execute the identified tests in a randomized order multiple times.
- If the test suite produces inconsistent results (passing sometimes, failing others), it indicates a dependency on execution order.

**Feedback:**
- Reports any non-deterministic tests with file names and line numbers.
- Provides suggestions for refactoring tests to ensure proper isolation.
- Lists potential shared state or external dependencies causing issues.

### 5. Validate Test Length Constraints

**Action:**
- Parse the identified test files to analyze the line count of each unit test.
- Extract tests that exceed the standard length limits.

**Validation Rules:**
- Unit Test Length <= `10` lines of code (LOC)
- Extended Unit Test Length <= `20` LOC (requires explanatory comment)

**Feedback:**
- Reports any tests that exceed length limits with file names and line numbers.
- For tests between 10-20 LOC, verifies that an explanatory comment in the format `// Extended test: [Reason]` or equivalent language-specific format is present.
- Suggests refactoring approaches to reduce test complexity.

## Output Format

The tool provides clear, actionable feedback:
- **Summary**: Overall compliance status and key metrics
- **Issues**: Detailed list of violations with file locations
- **Suggestions**: Specific recommendations for improvement
- **Examples**: Sample corrections where applicable

## Integration

This validation can be integrated into:
- Local development workflows
- Pre-commit hooks
- IDE plugins
- CI/CD pipelines (for reporting only, no blocking)