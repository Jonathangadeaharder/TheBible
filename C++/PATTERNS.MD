# C++ Design Patterns - Essentials

## Key Principle

Focus on RAII and modern C++ features rather than complex pattern implementations.

## Essential Patterns

### RAII (Resource Acquisition Is Initialization)
**When to use**: Automatic resource management

```cpp
#include <fstream>
#include <memory>

class FileHandler {
private:
    std::ifstream file;

public:
    FileHandler(const std::string& filename) : file(filename) {
        if (!file.is_open()) {
            throw std::runtime_error("Could not open file");
        }
    }

    ~FileHandler() {
        if (file.is_open()) {
            file.close();
        }
    }

    // Delete copy constructor and assignment operator
    FileHandler(const FileHandler&) = delete;
    FileHandler& operator=(const FileHandler&) = delete;
};
```

### Smart Pointers
**When to use**: Automatic memory management

```cpp
#include <memory>

// Unique pointer - exclusive ownership
std::unique_ptr<MyClass> ptr = std::make_unique<MyClass>();

// Shared pointer - shared ownership
std::shared_ptr<MyClass> sharedPtr = std::make_shared<MyClass>();

// Weak pointer - non-owning reference to shared pointer
std::weak_ptr<MyClass> weakPtr = sharedPtr;
```

### Strategy Pattern
**When to use**: Switch between different algorithms

```cpp
#include <iostream>
#include <memory>

// Strategy interface
class PaymentStrategy {
public:
    virtual ~PaymentStrategy() = default;
    virtual bool processPayment(double amount) = 0;
};

// Concrete strategies
class CreditCardPayment : public PaymentStrategy {
public:
    bool processPayment(double amount) override {
        std::cout << "Processing credit card payment of $" << amount << std::endl;
        return true;
    }
};

// Context class
class PaymentProcessor {
private:
    std::unique_ptr<PaymentStrategy> strategy_;

public:
    explicit PaymentProcessor(std::unique_ptr<PaymentStrategy> strategy)
        : strategy_(std::move(strategy)) {}

    bool executePayment(double amount) {
        return strategy_->processPayment(amount);
    }
};
```

## Quick Reference

| Pattern | Problem | When to Use |
|---------|---------|-------------|
| RAII | Resource management | All resource handling |
| Smart Pointers | Memory management | Dynamic allocation |
| Strategy | Algorithm switching | Multiple approaches |
| Observer | Event notification | Loose coupling |

## Modern C++ Guidelines

1. Prefer stack allocation over heap allocation
2. Use smart pointers instead of raw pointers
3. Leverage move semantics for performance
4. Use const correctness consistently
5. Apply the Rule of Five (destructor, copy/move constructor, copy/move assignment)