# Commit Message Standards

This document outlines the standards for writing clear, informative commit messages that enhance project history and collaboration.

## Commit Message Format

Follow the conventional commit format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types
- **feat**: A new feature
- **fix**: A bug fix
- **docs**: Documentation only changes
- **style**: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
- **refactor**: A code change that neither fixes a bug nor adds a feature
- **perf**: A code change that improves performance
- **test**: Adding missing tests or correcting existing tests
- **build**: Changes that affect the build system or external dependencies
- **ci**: Changes to our CI configuration files and scripts
- **chore**: Other changes that don't modify src or test files

### Scope
The scope should be the name of the module, component, or area affected by the change.

### Subject
- Use imperative, present tense ("change" not "changed" or "changes")
- Don't capitalize the first letter
- No period (.) at the end
- Keep it concise (typically < 50 characters)

### Body
- Just as in the subject, use imperative, present tense
- Include the motivation for the change and contrast this with previous behavior
- Wrap lines at 72 characters

### Footer
- Reference issues closed by this commit (e.g., "Fixes #123", "Closes #456")
- Reference breaking changes with "BREAKING CHANGE:" prefix

## Examples

Good commit messages:

```
feat(auth): add password reset functionality

Implement password reset flow including email verification
and token expiration handling.

Closes #1234
```

```
fix(api): resolve race condition in user updates

Use database transactions to ensure atomic updates
when modifying user profile data.

Fixes #5678
```

```
docs(readme): update installation instructions

Add detailed steps for different operating systems
and clarify dependency requirements.
```

## Best Practices

1. **Atomic Commits**: Each commit should represent a single logical change
2. **Frequent Commits**: Commit small, focused changes regularly
3. **Clear Separation**: Separate subject from body with a blank line
4. **Consistent Language**: Use the same language throughout the project
5. **Reference Issues**: Always reference related issues or tickets
6. **Avoid Vague Messages**: Don't use messages like "fix bug" or "update code"