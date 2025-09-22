# Local Commit Message Validation

## Objective

This command ensures your commit messages comply with the standards defined in `/COMMIT.MD`. It validates commit messages and helps you write properly formatted commits during local development.

## Usage

Run this command before pushing your commits to validate message quality:
```bash
./validate-commits.sh
```

Or integrate it as a git hook:
```bash
# Install as pre-commit hook
ln -s ../../validate-commits.sh .git/hooks/commit-msg
```

---

## Validation Checks

### 1. Commit Message Analysis

**Action:**
- Parse recent commit messages in your local branch
- Extract commit message components (type, scope, subject, body, footer)

**Validation Rules:**
- Must follow conventional commit format: `<type>(<scope>): <subject>`
- Type must be one of: feat, fix, docs, style, refactor, perf, test, build, ci, chore
- Subject line must be <= 50 characters
- Subject must use imperative mood
- Body lines should be wrapped at 72 characters
- Footer should contain issue references when applicable

**Feedback:**
- Reports any commits with invalid formatting
- Provides specific corrections for each violation
- Shows examples of correctly formatted commit messages
- Suggests improvements to clarity and structure

### 2. Atomic Commit Verification

**Action:**
- Analyze commit content to ensure each represents a single logical change
- Check for mixed concerns (e.g., feature implementation + formatting changes)

**Validation Rules:**
- Each commit should address one issue or feature
- Related changes should be grouped logically
- Avoid commits that mix different types of changes

**Feedback:**
- Identifies commits with mixed concerns
- Suggests squashing or splitting commits as appropriate
- Provides guidance on proper commit grouping
- Recommends when to use `git commit --amend`

### 3. Issue Reference Validation

**Action:**
- Check commit footers for issue references
- Verify referenced issues exist in the issue tracker (if connected)
- Ensure fix commits reference appropriate issues

**Validation Rules:**
- Fix commits should reference existing issues
- Feature commits may reference issues or epics
- Breaking changes must be clearly indicated

**Feedback:**
- Flags commits missing required issue references
- Suggests appropriate issue references based on commit content
- Explains the importance of linking commits to issues
- Provides templates for common reference formats

### 4. Commit Message Enhancement

**Action:**
- Suggest improvements to commit message clarity
- Recommend more descriptive subjects when vague language is detected
- Propose better categorization of changes

**Enhancement Rules:**
- Subjects should clearly indicate the impact of changes
- Bodies should explain the "why" behind changes
- Technical details should be included when relevant

**Feedback:**
- Provides suggestions for improving commit message quality
- Offers templates for common commit types
- Educates on effective commit message practices
- Recommends resources for learning more about commit conventions

## Output Format

The tool provides clear, actionable feedback:
- **Summary**: Overall commit quality score and key metrics
- **Issues**: Detailed list of violations with specific corrections
- **Suggestions**: Recommendations for improvement
- **Examples**: Well-formatted commit messages for reference

## Integration

This validation can be integrated into:
- Local development workflows
- Git commit hooks
- Git push hooks
- IDE plugins
- Editor configurations