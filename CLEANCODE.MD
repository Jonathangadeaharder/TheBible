# Clean Code Principles

This document outlines the principles for writing clean, maintainable code across all languages and frameworks.

## Key Principles

### 1. Meaningful Names
- Use intention-revealing names for variables, functions, and classes
- Avoid disinformation and noise words (data, info, manager)
- Make meaningful distinctions between similar concepts
- Use pronounceable and searchable names

### 2. Functions
- Keep functions small and focused on a single responsibility
- Functions should do one thing and do it well
- Use function names that clearly express their purpose
- Minimize the number of function arguments (ideally < 3)

### 3. Comments
- Express yourself in code rather than comments when possible
- Comments should explain "why" not "what"
- Keep comments up to date with code changes
- Remove commented-out code

### 4. Formatting
- Maintain consistent indentation and spacing
- Place related concepts near each other
- Order functions from high-level to low-level abstraction
- Keep lines reasonably short (generally < 100 characters)

### 5. Error Handling
- Use exceptions rather than return codes
- Handle errors at the appropriate level
- Don't return or pass null
- Provide context with exceptions

### 6. Boundaries
- Encapsulate third-party library dependencies
- Avoid passing raw boundary interfaces inward
- Use Adapter pattern to convert foreign interfaces
- Minimize exposure to third-party code

## Language-Specific Guidelines

Refer to language-specific directories for implementation details:
- [C# Clean Code](./C#/CLEANCODE.MD)
- [C++ Clean Code](./C++/CLEANCODE.MD)
- [React Clean Code](./React/CLEANCODE.MD)
- [Python Clean Code](./Python/CLEANCODE.MD)