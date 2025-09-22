# Automated Workflows

This directory contains the "enforcement code" - automated workflows that ensure compliance with the principles defined in the root guideline files.

## Purpose

Each workflow file in this directory defines step-by-step procedures for automated agents to enforce the corresponding standards from the root guideline files:

| Guideline File | Command Workflow | Purpose |
|----------------|------------------|---------|
| [/ARCHITECT.MD](../ARCHITECT.MD) | [ARCHITECT.MD](./ARCHITECT.MD) | Enforce architecture decision records and documentation |
| [/CLEANCODE.MD](../CLEANCODE.MD) | [CLEANCODE.MD](./CLEANCODE.MD) | Enforce clean code standards and practices |
| [/COMMIT.MD](../COMMIT.MD) | [COMMIT.MD](./COMMIT.MD) | Validate commit message format and quality |
| [/TESTING.MD](../TESTING.MD) | [TESTING.MD](./TESTING.MD) | Enforce test coverage, timeouts, and quality standards |

## How It Works

1. **Developer writes code** following the principles in the root guideline files
2. **Changes are submitted** via pull request or push
3. **Automated workflows execute** to validate compliance with standards
4. **Violations are flagged** with specific corrective actions
5. **Corrective measures are taken** automatically or manually
6. **Compliance is verified** before changes are merged

## Workflow Structure

Each command workflow follows a consistent structure:

### Objective
Defines what the workflow aims to accomplish and which guideline it enforces.

### Triggers
Specifies when the workflow executes (e.g., on pull request, push to main branch).

### Step-by-Step Workflow
Detailed procedures including:
- **Actions**: What the workflow does
- **Validation Rules**: Standards being enforced
- **Corrective Actions**: How violations are handled

### Outputs
Describes the workflow's results (status checks, comments, commits, issues).

## Integration

These workflows are designed to integrate with common CI/CD platforms and can be adapted to:
- GitHub Actions
- GitLab CI/CD
- Jenkins Pipelines
- Other automation platforms

Each workflow is self-contained and can be implemented independently based on your toolchain.