# Repository Restructuring Summary

## Problem Identified
The original `AGENTS.MD` files were misstructured - they contained workflow/orchestration information but were labeled as agent personas. This created a fundamental mismatch between the file purpose and content.

## Solution Implemented
Restructured the repository to properly separate three distinct concerns:

### 1. Guideline Files (Root Directory)
- **Purpose**: Define the "constitution" - standards, principles, and rationale
- **Examples**: ARCHITECT.MD, CLEANCODE.MD, COMMIT.MD, TESTING.MD
- **Format**: Principle-based documents with rules and best practices

### 2. Coordination Files (commands/ Directory)
- **Purpose**: Define the "management system" - human-centered workflows for team collaboration
- **Renamed from**: AGENTS.MD → COORDINATION.MD
- **Examples**: commands/ARCHITECT.MD, commands/CLEANCODE.MD
- **Format**: Process documents for team workflows, not agent identities

### 3. Persona Files (prompts/ Directory)
- **Purpose**: Define the "expert team" - specialized agent personas with specific skills
- **New Addition**: Created new directory structure with technology-specific personas
- **Examples**:
  - prompts/C#/AspNetCoreAgent.md
  - prompts/C#/EntityFrameworkAgent.md
  - prompts/C#/TestingAgent.md
  - prompts/C++/ModernCppAgent.md
  - prompts/C++/CppTestingAgent.md
  - prompts/React/ReactComponentAgent.md
  - prompts/React/ReactTestingAgent.md
  - prompts/Python/PythonCleanCodeAgent.md
  - prompts/Python/PythonTestingAgent.md
  - prompts/Python/PythonDataScienceAgent.md
- **Format**: True system prompts with persona, objectives, rules, capabilities, and examples

## Key Benefits
1. **Clear Separation of Concerns**: Each file type has a distinct purpose
2. **Proper System Prompts**: Agent personas now have proper identity, rules, and constraints
3. **Human-Centered Workflows**: Coordination files focus on team collaboration
4. **Principle-Based Foundation**: Guideline files remain the authoritative source
5. **Scalable Structure**: Easy to add new personas and workflows

## Files Modified
- Renamed root AGENTS.MD → COORDINATION.MD
- Renamed language-specific AGENTS.MD files → COORDINATION.MD
- Created new prompts/ directory structure
- Created 10 specialized agent persona files
- Updated README.md files to reflect new structure