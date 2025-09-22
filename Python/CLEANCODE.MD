# Python Clean Code Guidelines

This document outlines clean code principles specifically for Python development, building upon the general principles in `/CLEANCODE.MD`.

## Key Python Principles

### 1. PEP 8 Compliance
- Follow PEP 8 style guide for Python code
- Use 4 spaces per indentation level
- Limit lines to 79 characters
- Use blank lines to separate functions and classes

### 2. Pythonic Idioms
- Use list comprehensions and generator expressions
- Leverage context managers (`with` statements)
- Apply unpacking and multiple assignment
- Use built-in functions and libraries when available

### 3. Type Hints
- Use type hints for function parameters and return values
- Apply type hints to complex data structures
- Consider using `typing` module for advanced typing

### 4. Docstrings
- Write docstrings for all public modules, functions, classes, and methods
- Follow PEP 257 for docstring conventions
- Use meaningful, descriptive docstrings

## Python-Specific Clean Code Practices

### Naming Conventions
```python
# Modules and packages: lowercase_with_underscores
import data_processor

# Classes: CapWords (PascalCase)
class UserManager:
    pass

# Functions and variables: lowercase_with_underscores
def calculate_total_price():
    pass

# Constants: UPPERCASE_WITH_UNDERSCORES
MAX_BUFFER_SIZE = 1024
PI_CONSTANT = 3.14159

# Private members: leading underscore
class Example:
    def __init__(self):
        self._internal_count = 0
        self.__very_private = "secret"
```

### Function Design
```python
# Good: Small, focused functions
def calculate_area(radius: float) -> float:
    """Calculate the area of a circle given its radius."""
    return PI_CONSTANT * radius * radius

# Good: Clear parameter names with defaults
def process_data(input_data: list,
                output_format: str = 'json') -> dict:
    """Process input data and return formatted results."""
    # ... process data ...
    return results

# Avoid: Long parameter lists - use kwargs or dataclasses
def bad_function(a, b, c, d, e, f, g, h, i, j):
    pass
```

### Class Design
```python
# Good: Clear separation of concerns
class BankAccount:
    """Represents a bank account with basic operations."""

    def __init__(self, initial_balance: float = 0.0):
        """Initialize account with optional initial balance."""
        self._balance = initial_balance
        self._transactions = []

    @property
    def balance(self) -> float:
        """Get current account balance."""
        return self._balance

    def deposit(self, amount: float) -> None:
        """Deposit money into the account."""
        if amount <= 0:
            raise ValueError("Deposit amount must be positive")
        self._balance += amount
        self._transactions.append(('deposit', amount))

    def withdraw(self, amount: float) -> bool:
        """Withdraw money from the account."""
        if amount <= 0 or amount > self._balance:
            return False
        self._balance -= amount
        self._transactions.append(('withdraw', amount))
        return True

# Good: Dataclasses for simple data containers
from dataclasses import dataclass

@dataclass
class Person:
    name: str
    age: int
    email: str
```

### Error Handling
```python
# Good: Specific exception handling
def read_config_file(filename: str) -> dict:
    """Read configuration from JSON file."""
    try:
        with open(filename, 'r') as file:
            return json.load(file)
    except FileNotFoundError:
        logger.warning(f"Config file {filename} not found, using defaults")
        return {}
    except json.JSONDecodeError as e:
        logger.error(f"Invalid JSON in config file {filename}: {e}")
        raise ValueError(f"Invalid config file format: {filename}")

# Good: Context managers for resource management
def process_file(filename: str) -> str:
    """Process a file and return its contents."""
    with open(filename, 'r', encoding='utf-8') as file:
        return file.read().strip()
```

## Python Anti-Patterns to Avoid

### 1. Mutable Default Arguments
```python
# Bad: Mutable default argument
def bad_function(items=[]):
    items.append("new_item")
    return items

# Good: Use None as default
def good_function(items=None):
    if items is None:
        items = []
    items.append("new_item")
    return items
```

### 2. Deep Nesting
```python
# Bad: Deep nesting
if condition1:
    if condition2:
        if condition3:
            do_something()

# Good: Early returns and guard clauses
if not condition1:
    return
if not condition2:
    return
if not condition3:
    return
do_something()
```

### 3. Broad Exception Handling
```python
# Bad: Catching all exceptions
try:
    risky_operation()
except:
    pass  # Silent failure

# Good: Specific exception handling
try:
    risky_operation()
except SpecificException as e:
    logger.error(f"Operation failed: {e}")
    handle_specific_error(e)
```

## Performance Considerations

### 1. Efficient Data Structures
```python
# Good: Use sets for membership testing
valid_names = {'alice', 'bob', 'charlie'}
if name in valid_names:  # O(1) lookup
    process_name(name)

# Good: Use generators for large datasets
def process_large_file(filename):
    with open(filename) as file:
        for line in file:
            yield process_line(line)
```

### 2. String Concatenation
```python
# Bad: Inefficient string concatenation in loops
result = ""
for item in items:
    result += str(item)  # Creates new string each time

# Good: Use join for multiple concatenations
result = "".join(str(item) for item in items)

# Good: Use format strings for readability
message = f"Hello {name}, you have {count} messages"
```

## Testing Considerations

### 1. Pytest Best Practices
```python
# Good: Clear test naming and structure
def test_bank_account_deposit():
    """Test that deposit increases balance correctly."""
    account = BankAccount(100.0)
    account.deposit(50.0)
    assert account.balance == 150.0

# Good: Parametrized tests
@pytest.mark.parametrize("amount,expected", [
    (100, 200),
    (50, 150),
    (0, 100),
])
def test_deposit_various_amounts(amount, expected):
    account = BankAccount(100.0)
    account.deposit(amount)
    assert account.balance == expected

# Good: Fixtures for test setup
@pytest.fixture
def sample_account():
    return BankAccount(1000.0)

def test_withdraw_success(sample_account):
    result = sample_account.withdraw(100.0)
    assert result is True
    assert sample_account.balance == 900.0
```

## Quick Reference

| Principle | Recommendation |
|-----------|----------------|
| Style | Follow PEP 8 |
| Typing | Use type hints |
| Documentation | Write clear docstrings |
| Error Handling | Be specific with exceptions |
| Performance | Use appropriate data structures |
| Testing | Write focused, isolated tests |