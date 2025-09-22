# C# Testing Guide

> **Note**: For general testing principles and common anti-patterns to avoid, please refer to the main [TESTING.MD](../TESTING.MD) file.

## State-of-the-Art Testing Frameworks

### Unit Testing Framework
**xUnit.net** - The most modern and widely adopted testing framework for .NET

### Mocking Framework
**Moq** - The most popular and mature mocking framework for .NET

## Installation

```bash
dotnet add package xunit
dotnet add package xunit.runner.visualstudio
dotnet add package Moq
```

## Unit Test Example

```csharp
using Xunit;
using Moq;
using System;

public class UserServiceTests
{
    [Fact(Timeout = 60000)]
    public void GetUserById_WhenUserExists_ReturnsUser()
    {
        // Arrange
        var mockRepository = new Mock<IUserRepository>();
        var expectedUser = new User { Id = 1, Name = "John Doe" };
        mockRepository.Setup(repo => repo.GetById(1)).Returns(expectedUser);

        var userService = new UserService(mockRepository.Object);

        // Act
        var result = userService.GetUserById(1);

        // Assert
        Assert.NotNull(result);
        Assert.Equal("John Doe", result.Name);
        mockRepository.Verify(repo => repo.GetById(1), Times.Once);
    }

    [Theory(Timeout = 60000)]
    [InlineData(0)]
    [InlineData(-1)]
    public void GetUserById_WhenInvalidId_ThrowsArgumentException(int invalidId)
    {
        // Arrange
        var userService = new UserService(Mock.Of<IUserRepository>());

        // Act & Assert
        Assert.Throws<ArgumentException>(() => userService.GetUserById(invalidId));
    }
}
```

## Integration Test Example

```csharp
using Xunit;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;
using System.Threading.Tasks;

[Collection("DatabaseCollection")]
public class UserRepositoryIntegrationTests
{
    private readonly DatabaseFixture _fixture;

    public UserRepositoryIntegrationTests(DatabaseFixture fixture)
    {
        _fixture = fixture;
    }

    [Fact(Timeout = 300000)]
    public async Task GetById_WhenUserExists_ReturnsUserFromDatabase()
    {
        // Arrange
        var userId = await _fixture.CreateTestUserAsync("Jane Doe");

        // Act
        var user = await _fixture.UserRepository.GetById(userId);

        // Assert
        Assert.NotNull(user);
        Assert.Equal("Jane Doe", user.Name);
    }
}
```

## End-to-End Test Example

```csharp
using Xunit;
using System.Threading.Tasks;

public class UserEndToEndTests
{
    [Fact(Timeout = 600000)]
    public async Task FullUserWorkflow_CompletesSuccessfully()
    {
        // Arrange
        var testUser = new UserRegistrationDto
        {
            Name = "Test User",
            Email = "test@example.com"
        };

        // Act
        var userId = await CreateUserViaApi(testUser);
        var retrievedUser = await GetUserViaApi(userId);
        var success = await DeleteUserViaApi(userId);

        // Assert
        Assert.True(userId > 0);
        Assert.Equal(testUser.Name, retrievedUser.Name);
        Assert.True(success);
    }
}
```

## Best Practices

1. Follow the AAA pattern (Arrange, Act, Assert)
2. Use descriptive test method names
3. One assertion per test when possible
4. Mock external dependencies
5. Use Theory for parametrized tests
6. Separate unit and integration tests in different projects
7. Use in-memory databases for integration tests when possible
8. **Always set timeouts: 1 minute for unit tests, 5 minutes for integration tests, 10 minutes for end-to-end tests**
9. Use Timeout attribute to prevent tests from hanging indefinitely
10. Monitor test execution times to identify performance regressions
11. **Keep unit tests concise: maximum 10 lines of code (LOC), up to 20 LOC with explanatory comment**

## Mocking Anti-Patterns to Avoid

1. **The Mockery**: Avoid excessive mocking that tests mock interactions rather than real behavior. This often indicates classes with too many dependencies.

2. **Overusing Mocks for Implementation Details**: Don't mock to assert on internal calls or private behaviors. Focus on testing observable outcomes rather than implementation specifics.

3. **Leaky Mocks**: Ensure mocks don't leak between tests. Each test should have isolated mock configurations to maintain consistency.

4. **Mocking Everything Indiscriminately**: Don't mock all dependencies by default. Use mocks sparingly for true isolation, preferring fakes or stubs for simpler simulations.

Focus on testing behavior over internals. If mocking feels excessive, it might signal design issues like classes with too many responsibilities.