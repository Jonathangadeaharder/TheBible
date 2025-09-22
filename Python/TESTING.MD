# Python Testing Guidelines

This document outlines testing principles and practices specifically for Python development, building upon the general principles in `/TESTING.MD`.

## Test Structure and Organization

### Project Layout
```
project/
├── src/
│   └── mypackage/
├── tests/
│   ├── unit/
│   │   ├── test_models.py
│   │   └── test_utils.py
│   ├── integration/
│   │   └── test_api.py
│   └── conftest.py
├── pytest.ini
└── requirements-test.txt
```

### Test File Naming
- Test files: `test_*.py` or `*_test.py`
- Test functions: `test_*`
- Test classes: `Test*`

## Pytest Best Practices

### Basic Test Structure
```python
import pytest
from mypackage.module import MyClass

def test_basic_functionality():
    """Test basic functionality of MyClass."""
    obj = MyClass()
    result = obj.some_method()
    assert result == expected_value

def test_edge_case():
    """Test edge case handling."""
    obj = MyClass()
    with pytest.raises(ValueError):
        obj.some_method(invalid_input)
```

### Fixtures for Test Setup
```python
import pytest
from unittest.mock import Mock

@pytest.fixture
def mock_database():
    """Create a mock database connection."""
    return Mock()

@pytest.fixture
def sample_data():
    """Provide sample test data."""
    return {
        'users': [
            {'id': 1, 'name': 'Alice'},
            {'id': 2, 'name': 'Bob'}
        ]
    }

def test_user_processing(mock_database, sample_data):
    """Test user processing with fixtures."""
    # Test implementation using fixtures
    pass
```

### Parametrized Tests
```python
import pytest

@pytest.mark.parametrize("input_value,expected", [
    (0, 0),
    (1, 1),
    (10, 55),
    (-1, ValueError),
])
def test_fibonacci(input_value, expected):
    """Test fibonacci function with various inputs."""
    if expected == ValueError:
        with pytest.raises(ValueError):
            fibonacci(input_value)
    else:
        assert fibonacci(input_value) == expected
```

## Test Categories

### Unit Tests
Focus on testing individual functions and methods in isolation.

```python
def test_calculate_tax():
    """Unit test for tax calculation."""
    result = calculate_tax(100.0, 0.1)
    assert result == 10.0

def test_user_validation():
    """Unit test for user input validation."""
    user = User(email="invalid-email")
    with pytest.raises(ValidationError):
        user.validate()
```

### Integration Tests
Test interactions between components and external systems.

```python
def test_database_integration(database_connection):
    """Integration test with database."""
    user_repo = UserRepository(database_connection)
    user = User(name="Test User", email="test@example.com")

    # Test create
    created_user = user_repo.create(user)
    assert created_user.id is not None

    # Test retrieve
    retrieved_user = user_repo.get(created_user.id)
    assert retrieved_user.name == user.name
```

### End-to-End Tests
Test complete user workflows and system behavior.

```python
def test_user_registration_flow(client):
    """E2E test for user registration."""
    # Navigate to registration page
    response = client.get('/register')
    assert response.status_code == 200

    # Submit registration form
    response = client.post('/register', data={
        'username': 'testuser',
        'email': 'test@example.com',
        'password': 'securepassword'
    })

    # Verify redirect to dashboard
    assert response.status_code == 302
    assert response.location == '/dashboard'
```

## Mocking and Stubbing

### Mocking External Dependencies
```python
from unittest.mock import Mock, patch

@patch('mypackage.external_api.call_external_service')
def test_with_mocked_api(mock_api):
    """Test with mocked external API."""
    mock_api.return_value = {'status': 'success'}

    result = my_function_that_calls_api()
    assert result == 'processed'
    mock_api.assert_called_once_with(expected_params)

def test_with_manual_mock():
    """Test with manually created mock."""
    mock_service = Mock()
    mock_service.get_user.return_value = User(id=1, name='Test')

    processor = UserProcessor(mock_service)
    result = processor.process_user(1)

    assert result.name == 'Test'
    mock_service.get_user.assert_called_once_with(1)
```

### Mocking Context Managers
```python
from unittest.mock import mock_open, patch

def test_file_processing():
    """Test file processing with mocked file operations."""
    mock_file_content = "line1\nline2\nline3"

    with patch('builtins.open', mock_open(read_data=mock_file_content)):
        result = process_file('fake_file.txt')
        assert len(result) == 3
```

## Test Configuration

### pytest.ini
```ini
[tool:pytest]
testpaths = tests
python_files = test_*.py *_test.py
python_classes = Test*
python_functions = test_*
addopts =
    --verbose
    --tb=short
    --strict-markers
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
    e2e: marks tests as end-to-end tests
```

### Test Requirements
```txt
pytest>=6.0.0
pytest-cov>=2.10.0
pytest-mock>=3.3.0
responses>=0.12.0
freezegun>=1.0.0
```

## Test Quality Standards

### Test Independence
```python
# Good: Independent tests
def test_user_creation():
    user = create_user(name="Alice")
    assert user.name == "Alice"

def test_user_deletion():
    user = create_user(name="Bob")
    delete_user(user.id)
    assert get_user(user.id) is None
```

### Test Length Constraints

Unit tests must be concise and focused:

*   **Maximum Length:** Unit tests should not exceed 10 lines of code (LOC).
*   **Extended Length:** Unit tests may extend to up to 20 LOC, but this requires a comment explaining why the test cannot be simplified while maintaining adequate coverage.
*   **Rationale:** Short tests are easier to understand, maintain, and debug. They also tend to be more focused on specific behaviors.

For tests exceeding 10 LOC, include a comment in this format:
```python
# Extended test: [Reason why this test cannot be simplified while maintaining adequate coverage]
def test_complex_scenario():
    # Test implementation that requires more lines
    pass
```

### Clear Assertions
```python
# Good: Specific assertions
def test_calculation():
    result = calculate_discount(100, 0.1)
    assert result == 90.0
    assert isinstance(result, float)

# Avoid: Generic assertions
def test_calculation_bad():
    result = calculate_discount(100, 0.1)
    assert result  # Too vague
```

### Proper Test Data
```python
# Good: Realistic test data
def test_email_validation():
    valid_emails = [
        "user@example.com",
        "test.user@domain.co.uk",
        "user+tag@site.org"
    ]

    for email in valid_emails:
        assert is_valid_email(email)
```

## Coverage and Quality Metrics

### Coverage Requirements
- **Line Coverage**: Minimum 80%
- **Branch Coverage**: Minimum 70%
- **Critical Path Coverage**: 100% for business logic

### Running Tests with Coverage
```bash
# Run tests with coverage
pytest --cov=mypackage --cov-report=html --cov-report=term

# Run tests with coverage thresholds
pytest --cov=mypackage --cov-fail-under=80
```

## Test Timeouts

### Timeout Standards
- **Unit Tests**: Maximum 1 second per test
- **Integration Tests**: Maximum 5 seconds per test
- **End-to-End Tests**: Maximum 30 seconds per test

### Implementing Timeouts
```python
import pytest

@pytest.mark.timeout(1)
def test_fast_unit_test():
    """Fast unit test with 1-second timeout."""
    result = quick_calculation()
    assert result == expected

@pytest.mark.timeout(5)
def test_integration_test():
    """Integration test with 5-second timeout."""
    result = complex_operation()
    assert result is not None
```

## Continuous Integration

### CI Pipeline Configuration
```yaml
# .github/workflows/test.yml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8, 3.9, 3.10]
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: ${{ matrix.python-version }}
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        pip install -r requirements-test.txt
    - name: Run tests
      run: |
        pytest --cov=mypackage --cov-report=xml
    - name: Upload coverage
      uses: codecov/codecov-action@v1
```

## Quick Reference

| Test Type | Scope | Speed | Coverage Target |
|-----------|-------|-------|-----------------|
| Unit | Individual functions/methods | Fast (<1s) | 80%+ |
| Integration | Component interactions | Medium (<5s) | 70%+ |
| E2E | Complete workflows | Slow (<30s) | Critical paths |

| Best Practice | Implementation |
|---------------|----------------|
| Test Independence | Each test sets up its own data |
| Clear Naming | `test_what_is_being_tested_expected_behavior` |
| Specific Assertions | Assert exact expected values |
| Mock External Dependencies | Use unittest.mock for isolation |
| Measure Coverage | Run with pytest-cov regularly |