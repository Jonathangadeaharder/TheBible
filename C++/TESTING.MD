# C++ Testing Guide

> **Note**: For general testing principles and common anti-patterns to avoid, please refer to the main [TESTING.MD](../TESTING.MD) file.

## State-of-the-Art Testing Frameworks

### Unit Testing Framework
**Google Test (gtest)** - Google's widely adopted C++ testing framework

### Mocking Framework
**Google Mock (gmock)** - Part of Google Test, provides powerful mocking capabilities

## Installation

Using vcpkg:
```bash
vcpkg install gtest
```

Using CMake:
```cmake
find_package(GTest REQUIRED)
target_link_libraries(your_target GTest::gtest GTest::gmock)
```

## Unit Test Example

```cpp
#include <gtest/gtest.h>
#include <gmock/gmock.h>
#include <memory>

// Class under test
class UserService {
public:
    virtual ~UserService() = default;
    virtual std::string getUserById(int id) = 0;
};

class UserServiceImpl : public UserService {
private:
    std::unique_ptr<UserService> userRepository;

public:
    UserServiceImpl(std::unique_ptr<UserService> repo) : userRepository(std::move(repo)) {}

    std::string getUserById(int id) override {
        if (id <= 0) {
            throw std::invalid_argument("Invalid user ID");
        }
        return userRepository->getUserById(id);
    }
};

// Mock class
class MockUserService : public UserService {
public:
    MOCK_METHOD(std::string, getUserById, (int id), (override));
};

// Unit tests
TEST(UserServiceImplTest, GetUserById_WhenValidId_ReturnsUser) {
    // Arrange
    auto mockRepo = std::make_unique<MockUserService>();
    EXPECT_CALL(*mockRepo, getUserById(1))
        .WillOnce(::testing::Return("John Doe"));

    UserServiceImpl service(std::move(mockRepo));

    // Act
    std::string result = service.getUserById(1);

    // Assert
    EXPECT_EQ(result, "John Doe");
}

TEST(UserServiceImplTest, GetUserById_WhenInvalidId_ThrowsException) {
    // Arrange
    auto mockRepo = std::make_unique<MockUserService>();
    UserServiceImpl service(std::move(mockRepo));

    // Act & Assert
    EXPECT_THROW(service.getUserById(0), std::invalid_argument);
    EXPECT_THROW(service.getUserById(-1), std::invalid_argument);
}
```

## Integration Test Example

```cpp
#include <gtest/gtest.h>
#include <fstream>
#include <filesystem>

class FileDatabaseIntegrationTest : public ::testing::Test {
protected:
    std::string testDir;

    void SetUp() override {
        testDir = "/tmp/test_db_" + std::to_string(time(nullptr));
        std::filesystem::create_directory(testDir);
    }

    void TearDown() override {
        std::filesystem::remove_all(testDir);
    }
};

TEST_F(FileDatabaseIntegrationTest, SaveAndLoadUserData_PersistsCorrectly) {
    // Arrange
    std::string filename = testDir + "/users.dat";
    std::string testData = "John Doe,25\nJane Smith,30";

    // Act
    // Save data to file
    std::ofstream outFile(filename);
    outFile << testData;
    outFile.close();

    // Load data from file
    std::ifstream inFile(filename);
    std::string loadedData;
    std::getline(inFile, loadedData);
    inFile.close();

    // Assert
    EXPECT_EQ(testData, loadedData);
}
```

## End-to-End Test Example

```cpp
#include <gtest/gtest.h>
#include <chrono>
#include <thread>

class ApplicationEndToEndTest : public ::testing::Test {
protected:
    void SetUp() override {
        // Setup test environment
        initializeTestEnvironment();
    }

    void TearDown() override {
        // Cleanup test environment
        cleanupTestEnvironment();
    }
};

TEST_F(ApplicationEndToEndTest, FullUserWorkflow_CompletesSuccessfully) {
    // Arrange
    UserRegistrationData testData{"John Doe", "john@example.com"};

    // Act
    auto startTime = std::chrono::steady_clock::now();

    UserId userId = createUserViaUI(testData);
    UserData retrievedUser = retrieveUserViaUI(userId);
    bool deletionSuccess = deleteUserViaUI(userId);

    auto endTime = std::chrono::steady_clock::now();
    auto duration = std::chrono::duration_cast<std::chrono::seconds>(endTime - startTime);

    // Assert
    EXPECT_GT(userId, 0);
    EXPECT_EQ(retrievedUser.name, testData.name);
    EXPECT_TRUE(deletionSuccess);
    EXPECT_LT(duration.count(), 600); // 10 minutes timeout
}
```

## Timeout Implementation Strategies

### Using CTest (Recommended)
If using CMake with CTest, you can set timeouts in your CMakeLists.txt:

```cmake
# Set default timeout for all tests
set(CTEST_TEST_TIMEOUT 60) # 1 minute default

# Override for specific tests
set_tests_properties(UnitTest1 PROPERTIES TIMEOUT 60)    # 1 minute
set_tests_properties(IntegrationTest1 PROPERTIES TIMEOUT 300)  # 5 minutes
set_tests_properties(EndToEndTest1 PROPERTIES TIMEOUT 600)     # 10 minutes
```

### Manual Timeout Implementation
For manual timeout checking within tests:

```cpp
#include <chrono>

TEST(ExampleTest, TestWithManualTimeout) {
    auto startTime = std::chrono::steady_clock::now();

    // Your test code here
    performTestOperation();

    auto currentTime = std::chrono::steady_clock::now();
    auto elapsedSeconds = std::chrono::duration_cast<std::chrono::seconds>(
        currentTime - startTime).count();

    // Assert timeout
    ASSERT_LT(elapsedSeconds, 60) << "Test exceeded 1 minute timeout";
}
```

## Best Practices

1. Follow the AAA pattern (Arrange, Act, Assert)
2. Use descriptive test case names
3. One assertion per test when possible
4. Mock external dependencies like databases and network calls
5. Use fixtures for shared test setup
6. Separate unit and integration tests
7. Test edge cases and error conditions
8. Use typed tests for testing templates
9. Keep tests fast and independent
10. **Always enforce timeouts: 1 minute for unit tests, 5 minutes for integration tests, 10 minutes for end-to-end tests**
11. Use CTest or similar test runner with timeout support
12. Monitor test execution times to identify performance regressions
13. Fail fast when tests exceed their time budgets
14. **Keep unit tests concise: maximum 10 lines of code (LOC), up to 20 LOC with explanatory comment**

## Mocking Anti-Patterns to Avoid

1. **The Mockery**: Avoid excessive mocking that tests mock interactions rather than real behavior. This often indicates classes with too many dependencies.

2. **Overusing Mocks for Implementation Details**: Don't mock to assert on internal calls or private behaviors. Focus on testing observable outcomes rather than implementation specifics.

3. **Leaky Mocks**: Ensure mocks don't leak between tests. Each test should have isolated mock configurations to maintain consistency.

4. **Mocking Everything Indiscriminately**: Don't mock all dependencies by default. Use mocks sparingly for true isolation, preferring fakes or stubs for simpler simulations.

Focus on testing behavior over internals. If mocking feels excessive, it might signal design issues like classes with too many responsibilities.