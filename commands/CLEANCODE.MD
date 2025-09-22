# Local Clean Code Standards Validation

## Objective

This command ensures your code complies with the clean code standards defined in `/CLEANCODE.MD`. It scans, validates, and reports deviations in source code files to help you maintain quality standards during local development.

## Usage

Run this command before committing code to validate your changes meet clean code standards:
```bash
./validate-clean-code.sh
```

Or as part of your local development workflow:
```bash
npm run validate-code-quality
```

---

## Validation Checks

### 1. Identify Source Files

**Action:**
- Scan the current directory to identify all source code files
- Exclude generated files, test files, and configuration files from analysis
- Focus on files that have been recently modified

### 2. Validate Naming Conventions

**Action:**
- Parse source files to extract variable, function, and class names
- Check names against language-specific conventions

**Validation Rules:**
- Names must be intention-revealing and avoid disinformation
- Avoid noise words (data, info, manager) unless contextually appropriate
- Use pronounceable and searchable names
- Follow language-specific casing conventions (camelCase, PascalCase, snake_case, etc.)

**Feedback:**
- Reports poorly named identifiers with file locations
- Provides suggestions for better names
- Explains why current names are problematic

### 3. Analyze Function Complexity

**Action:**
- Measure function length and cyclomatic complexity
- Count function parameters
- Identify nested conditional statements

**Validation Rules:**
- Functions should be < 50 lines (ideally < 20)
- Cyclomatic complexity should be <= 10
- Function parameters should be <= 3 (ideally <= 2)
- Nesting depth should be <= 3 levels

**Feedback:**
- Reports complex functions with complexity metrics
- Suggests refactoring approaches (extract method, parameter objects, etc.)
- Prioritizes functions that need immediate attention

### 4. Check Comment Quality

**Action:**
- Identify comments in source code
- Analyze comment content and placement
- Detect commented-out code

**Validation Rules:**
- Comments should explain "why" not "what"
- Avoid redundant comments that restate obvious code
- Remove or update outdated comments
- Eliminate commented-out code

**Feedback:**
- Reports low-quality comments with suggestions for improvement
- Identifies commented-out code that should be removed
- Suggests converting comments to better function/variable names

### 5. Validate Formatting Consistency

**Action:**
- Check indentation consistency
- Validate spacing around operators and keywords
- Ensure consistent brace/bracket placement
- Verify line length limits

**Validation Rules:**
- Use consistent indentation (typically 2 or 4 spaces)
- Maintain consistent spacing around operators
- Follow language-specific formatting conventions
- Keep lines reasonably short (generally < 100 characters)

**Feedback:**
- Reports formatting inconsistencies with file locations
- Suggests auto-formatting tools for your language
- Provides specific formatting guidelines

### 6. Identify Code Smells

**Action:**
- Detect duplicated code blocks
- Identify long parameter lists
- Find inappropriate intimacy between classes/modules
- Spot feature envy (functions that use more data from another class)

**Feedback:**
- Reports code smells with refactoring suggestions
- Prioritizes issues based on severity and impact
- Provides links to relevant clean code principles

## Output Format

The tool provides clear, actionable feedback:
- **Summary**: Overall code quality score and key metrics
- **Issues**: Detailed list of violations with file locations and severity
- **Suggestions**: Specific refactoring recommendations
- **Examples**: Sample improvements where applicable

## Integration

This validation can be integrated into:
- Local development workflows
- Pre-commit hooks
- IDE plugins
- Code review processes (for guidance only)