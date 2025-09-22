# C# Clean Code Guidelines

This document outlines C#-specific clean code principles that build upon the general clean code principles.

## Naming Conventions

### Classes and Interfaces
- Use PascalCase for class names: `UserService`, `FileProcessor`
- Interface names should start with 'I': `IUserRepository`, `IDisposable`
- Use noun phrases for classes: `UserManager`, `FileHandler`

### Methods and Variables
- Use PascalCase for public methods: `CalculateTotal()`, `GetUserById()`
- Use camelCase for private fields and local variables: `userName`, `totalAmount`
- Use descriptive verb-noun combinations for methods: `SaveUser()`, `ValidateInput()`

### Constants and Enums
- Use PascalCase for constants: `public const int MaxRetryAttempts = 3;`
- Use PascalCase for enum names and values: `FileStatus.Active`, `LogLevel.Error`

## C#-Specific Best Practices

### Properties and Fields
- Prefer auto-properties when no custom logic is needed:
  ```csharp
  public string UserName { get; set; }
  ```
- Use private setters for controlled mutability:
  ```csharp
  public string Id { get; private set; }
  ```

### Null Handling
- Use nullable reference types (C# 8.0+) to make null intentions explicit
- Prefer null-forgiving operator `!` only when you're certain about non-null values
- Use null-coalescing operators for concise null handling:
  ```csharp
  var displayName = user?.Profile?.DisplayName ?? "Unknown";
  ```

### Async/Await Patterns
- Always use `async`/`await` instead of `.Result` or `.Wait()`
- Name async methods with `Async` suffix: `GetDataAsync()`, `ProcessFileAsync()`
- Avoid `async void` except for event handlers

### Dependency Injection
- Use constructor injection for required dependencies
- Use property injection only for optional dependencies
- Register services with appropriate lifetimes (Transient, Scoped, Singleton)

### Exception Handling
- Catch specific exceptions rather than generic `Exception`
- Use custom exception types for domain-specific errors
- Don't catch and ignore exceptions silently
- Log exceptions with sufficient context for debugging

## Language-Specific Patterns

### LINQ and Collections
- Use LINQ methods for readable data transformations
- Prefer `IEnumerable<T>` for method parameters to enable deferred execution
- Use collection interfaces (`ICollection<T>`, `IList<T>`) in APIs for flexibility

### String Handling
- Use interpolated strings for readability: `$"Hello {name}"`
- Use `StringBuilder` for concatenating many strings in loops
- Prefer `string.IsNullOrEmpty()` and `string.IsNullOrWhiteSpace()` for validation

### Resource Management
- Implement `IDisposable` for classes managing unmanaged resources
- Use `using` statements for automatic resource disposal
- Prefer `using` declarations (C# 8.0+) for cleaner code:
  ```csharp
  using var fileStream = new FileStream(path, FileMode.Open);
  ```

## Framework-Specific Guidelines

### ASP.NET Core
- Follow the MVC pattern for web applications
- Use middleware for cross-cutting concerns
- Configure services in `Startup.cs` or `Program.cs`
- Use attribute routing for REST APIs
- Validate input at DTO boundaries

### Entity Framework
- Use Code First approach for database modeling
- Prefer eager loading with `Include()` to avoid N+1 problems
- Use migrations for schema changes
- Avoid exposing `DbContext` directly from services

## Testing Considerations

### Unit Testing
- Mock dependencies injected through constructors
- Test one behavior per test method
- Use descriptive test method names following the pattern: `MethodName_StateUnderTest_ExpectedBehavior`

### Integration Testing
- Test database interactions with in-memory providers when possible
- Use WebApplicationFactory for testing ASP.NET Core endpoints
- Isolate tests with transactions that can be rolled back

_Last Updated: 2025-09-21_
_Next Review: 2026-03-21_

## See Also

- [Clean Code Principles](../CLEANCODE.MD)
- [C# Testing Guidelines](./TESTING.MD)
- [C# Architecture Guidelines](./ARCHITECT.MD)