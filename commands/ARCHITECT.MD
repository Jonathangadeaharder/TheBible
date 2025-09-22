# Local Architecture Decision Validation

## Objective

This command ensures your architecture decisions comply with the standards defined in `/ARCHITECT.MD`. It validates ADRs and helps document significant architectural choices during local development.

## Usage

Run this command when creating or reviewing architecture decisions:
```bash
./validate-adr.sh
```

Or as part of your local development workflow:
```bash
npm run validate-architecture
```

---

## Validation Checks

### 1. ADR Validation

**Action:**
- Scan for ADR files in the current project (typically in `docs/adr/` or similar)
- Parse ADR content to validate structure and completeness

**Validation Rules:**
- ADR must follow standard format (Title, Status, Context, Decision, Consequences, Alternatives)
- Status must be one of: Proposed, Accepted, Deprecated, Superseded
- Context section must clearly describe the problem and driving forces
- Decision section must explain the chosen approach
- Consequences section must list both positive and negative outcomes

**Feedback:**
- Reports any incomplete or malformed ADRs with specific issues
- Provides templates for proper ADR formatting
- Suggests improvements to existing ADRs

### 2. Significant Change Detection

**Action:**
- Analyze code changes to identify potential architectural impacts
- Look for modifications to core components, interfaces, or dependencies
- Detect introduction of new technologies or frameworks

**Detection Rules:**
- Changes affecting >10% of codebase
- Modifications to core architectural components
- Introduction of new external dependencies
- Changes to data models or APIs
- Modifications to deployment or infrastructure

**Feedback:**
- Alerts when significant architectural changes are detected without documentation
- Suggests creating an ADR for significant changes
- Recommends appropriate stakeholders for review

### 3. Alternative Analysis Verification

**Action:**
- Check that ADRs include consideration of alternatives
- Verify that rationale for chosen approach is sound
- Ensure trade-offs are clearly documented

**Validation Rules:**
- ADRs should list viable alternatives when practical
- Each alternative should include pros and cons
- Decision rationale should reference specific trade-offs
- Rejected alternatives should have clear reasons for rejection

**Feedback:**
- Suggests additional alternatives when analysis is limited
- Flags insufficient analysis of trade-offs
- Provides guidance on evaluating architectural decisions

### 4. Diagram Validation

**Action:**
- Check for accompanying architecture diagrams
- Validate diagram format and content
- Ensure diagrams align with ADR decisions

**Validation Rules:**
- Significant architectural changes should include diagrams
- Diagrams should follow C4 model when appropriate
- Diagram elements should match described components
- Diagrams should be versioned with ADRs

**Feedback:**
- Suggests creating diagrams for complex architectural changes
- Provides improvements to diagram clarity and completeness
- Ensures diagrams are properly linked from ADRs
- Offers templates for common diagram types

## Output Format

The tool provides clear, actionable feedback:
- **Summary**: Overall compliance status and key findings
- **Issues**: Detailed list of validation failures with file locations
- **Suggestions**: Specific recommendations for improvement
- **Templates**: Ready-to-use templates for proper documentation

## Integration

This validation can be integrated into:
- Local development workflows
- Pre-commit hooks
- IDE plugins
- Code review processes (for guidance only)