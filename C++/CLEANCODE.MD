# C++ Clean Code Guidelines

This document outlines clean code principles specifically for C++ development, building upon the general principles in `/CLEANCODE.MD`.

## Key C++ Principles

### 1. Modern C++ Features
- Prefer `auto` for type deduction to improve readability
- Use range-based for loops when possible
- Leverage smart pointers (`std::unique_ptr`, `std::shared_ptr`) for automatic memory management
- Apply move semantics (`std::move`, rvalue references) for performance

### 2. RAII (Resource Acquisition Is Initialization)
- Manage resources through constructors and destructors
- Never use raw `new` and `delete` - use containers and smart pointers
- Ensure exception safety through proper resource management

### 3. Const Correctness
- Use `const` liberally for parameters, return values, and member functions
- Apply `constexpr` for compile-time constants and functions
- Prefer `const&` for function parameters to avoid unnecessary copies

### 4. Header Organization
- Use header guards (`#pragma once` or `#ifndef/#define/#endif`)
- Minimize `#include` statements in header files
- Prefer forward declarations when possible
- Follow consistent include ordering (C standard, C++ standard, project headers)

## C++-Specific Clean Code Practices

### Naming Conventions
```cpp
// Classes and structs: PascalCase
class UserManager { };

// Functions and variables: camelCase
void calculateTotalPrice();

// Constants: kCamelCase or UPPER_SNAKE_CASE
const int kMaxBufferSize = 1024;
const double PI_CONSTANT = 3.14159;

// Private members: trailing underscore
class Example {
private:
    int count_;
    std::string name_;
};
```

### Function Design
```cpp
// Good: Small, focused functions
double calculateArea(double radius) {
    return PI_CONSTANT * radius * radius;
}

// Good: Clear parameter names
void processData(const std::vector<int>& input_data,
                std::vector<int>& output_results);

// Avoid: Long parameter lists
void badFunction(int a, int b, int c, int d, int e, int f, int g);
```

### Class Design
```cpp
// Good: Clear separation of concerns
class BankAccount {
public:
    // Constructor initializes all members
    explicit BankAccount(double initial_balance);

    // Const-correct member functions
    double getBalance() const;

    // Clear, focused methods
    void deposit(double amount);
    bool withdraw(double amount);

private:
    // Private helper methods
    bool isValidAmount(double amount) const;

    double balance_;
    int account_number_;
};
```

### Error Handling
```cpp
// Good: Use exceptions for error conditions
class FileHandler {
public:
    explicit FileHandler(const std::string& filename) {
        file_.open(filename);
        if (!file_.is_open()) {
            throw std::runtime_error("Failed to open file: " + filename);
        }
    }

private:
    std::ifstream file_;
};

// Good: RAII for resource management
class DatabaseConnection {
public:
    DatabaseConnection() {
        connection_ = connectToDatabase();
        if (!connection_) {
            throw std::runtime_error("Failed to connect to database");
        }
    }

    ~DatabaseConnection() {
        if (connection_) {
            disconnectFromDatabase(connection_);
        }
    }

    // Disable copy constructor and assignment
    DatabaseConnection(const DatabaseConnection&) = delete;
    DatabaseConnection& operator=(const DatabaseConnection&) = delete;

private:
    DatabaseHandle* connection_;
};
```

## C++ Anti-Patterns to Avoid

### 1. Raw Pointer Management
```cpp
// Bad: Manual memory management
int* ptr = new int(42);
// ... use ptr ...
delete ptr; // Easy to forget!

// Good: Smart pointers
auto ptr = std::make_unique<int>(42);
// Automatic cleanup when ptr goes out of scope
```

### 2. Macro Overuse
```cpp
// Bad: Complex macros
#define MAX(a,b) ((a) > (b) ? (a) : (b))

// Good: Inline functions or templates
template<typename T>
inline T max_value(const T& a, const T& b) {
    return (a > b) ? a : b;
}
```

### 3. Using Namespace std
```cpp
// Bad: Polluting global namespace
using namespace std;

// Good: Explicit qualification
std::vector<std::string> names;
std::cout << "Hello World" << std::endl;
```

## Performance Considerations

### 1. Move Semantics
```cpp
// Good: Return by value with move semantics
std::vector<int> createLargeVector() {
    std::vector<int> result(1000000);
    // ... populate result ...
    return result; // Automatically moved
}

// Good: Move when transferring ownership
std::unique_ptr<Data> processData(std::unique_ptr<Data> input) {
    // ... process input ...
    return input; // Automatically moved
}
```

### 2. Avoid Unnecessary Copies
```cpp
// Good: Pass by const reference for large objects
void processString(const std::string& text) {
    // ... process text without copying ...
}

// Good: Reserve space when size is known
std::vector<int> buildVector(int size) {
    std::vector<int> result;
    result.reserve(size); // Avoid reallocations
    for (int i = 0; i < size; ++i) {
        result.push_back(i);
    }
    return result;
}
```

## Testing Considerations

### 1. Dependency Injection
```cpp
// Good: Interface for testing
class DatabaseInterface {
public:
    virtual ~DatabaseInterface() = default;
    virtual bool save(const Record& record) = 0;
    virtual std::optional<Record> load(int id) = 0;
};

class MockDatabase : public DatabaseInterface {
public:
    bool save(const Record& record) override {
        // Mock implementation for testing
        return true;
    }

    std::optional<Record> load(int id) override {
        // Mock implementation for testing
        return std::nullopt;
    }
};
```

## Quick Reference

| Principle | Recommendation |
|-----------|----------------|
| Memory Management | Use smart pointers, RAII |
| Performance | Apply move semantics, reserve vectors |
| Safety | Use const correctness, avoid macros |
| Readability | Prefer auto, clear naming |
| Testing | Design for dependency injection |