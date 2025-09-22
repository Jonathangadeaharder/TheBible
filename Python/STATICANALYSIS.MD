# Python Static Analysis Guidelines

This document outlines static analysis tools and practices for Python development to ensure code quality, security, and maintainability.

## Essential Static Analysis Tools

### 1. Linters

#### Flake8
Flake8 combines multiple tools to check style and complexity.

**Installation:**
```bash
pip install flake8
```

**Configuration (.flake8):**
```ini
[flake8]
max-line-length = 88
extend-ignore = E203, W503
exclude =
    .git,
    __pycache__,
    .venv,
    migrations
max-complexity = 10
```

#### Pylint
Pylint is a comprehensive linter with extensive checking capabilities.

**Installation:**
```bash
pip install pylint
```

**Configuration (.pylintrc):**
```ini
[MASTER]
ignore-patterns = test_.*,conftest.py

[MESSAGES CONTROL]
disable =
    missing-docstring,
    too-few-public-methods,
    too-many-arguments,
    too-many-locals

[FORMAT]
max-line-length = 88
```

### 2. Formatters

#### Black
Black is an uncompromising code formatter.

**Installation:**
```bash
pip install black
```

**Usage:**
```bash
black .  # Format entire project
black src/module.py  # Format specific file
```

**Configuration (pyproject.toml):**
```toml
[tool.black]
line-length = 88
target-version = ['py38']
include = '\.pyi?$'
extend-exclude = '''
/(
  # directories
  migrations
  | \.git
  | \.venv
)/
'''
```

#### Isort
Isort sorts and organizes import statements.

**Installation:**
```bash
pip install isort
```

**Configuration (.isort.cfg):**
```ini
[settings]
profile = black
multi_line_output = 3
line_length = 88
known_first_party = myproject
known_third_party = django,requests,setuptools
```

### 3. Type Checkers

#### MyPy
MyPy performs static type checking for Python.

**Installation:**
```bash
pip install mypy
```

**Configuration (mypy.ini):**
```ini
[mypy]
python_version = 3.8
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
disallow_incomplete_defs = True
check_untyped_defs = True
disallow_untyped_decorators = True

[mypy-tests.*]
disallow_untyped_defs = False
```

#### Pyright
Pyright is Microsoft's fast type checker.

**Installation:**
```bash
npm install -g pyright
# or
pip install pyright
```

**Configuration (pyrightconfig.json):**
```json
{
    "include": [
        "src"
    ],
    "exclude": [
        "**/node_modules",
        "**/__pycache__"
    ],
    "pythonVersion": "3.8",
    "typeCheckingMode": "basic"
}
```

## Security Analysis Tools

### Bandit
Bandit finds common security issues in Python code.

**Installation:**
```bash
pip install bandit
```

**Usage:**
```bash
bandit -r .  # Scan entire project
bandit src/module.py  # Scan specific file
```

**Configuration (.bandit):**
```ini
[bandit]
exclude_dirs: /test/,/migrations/
skips: B101,B102
```

### Safety
Safety checks dependencies for known security vulnerabilities.

**Installation:**
```bash
pip install safety
```

**Usage:**
```bash
safety check  # Check current environment
safety check -r requirements.txt  # Check requirements file
```

## Code Quality Metrics

### Radon
Radon computes various code metrics.

**Installation:**
```bash
pip install radon
```

**Usage:**
```bash
radon cc .  # Cyclomatic complexity
radon mi .  # Maintainability index
radon raw .  # Raw metrics
```

### Vulture
Vulture finds dead code in Python programs.

**Installation:**
```bash
pip install vulture
```

**Usage:**
```bash
vulture .  # Find unused code
vulture . --min-confidence 80  # Higher confidence threshold
```

## Integrated Analysis Tools

### Pre-commit
Pre-commit manages and maintains multi-language pre-commit hooks.

**Installation:**
```bash
pip install pre-commit
```

**Configuration (.pre-commit-config.yaml):**
```yaml
repos:
  - repo: https://github.com/psf/black
    rev: 22.3.0
    hooks:
      - id: black

  - repo: https://github.com/pycqa/isort
    rev: 5.10.1
    hooks:
      - id: isort

  - repo: https://github.com/pycqa/flake8
    rev: 4.0.1
    hooks:
      - id: flake8

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v0.950
    hooks:
      - id: mypy
```

### Prospector
Prospector combines multiple tools into a single command.

**Installation:**
```bash
pip install prospector
```

**Configuration (.prospector.yaml):**
```yaml
strictness: medium
doc-warnings: false
test-warnings: false
member-warnings: false

pyflakes:
  run: true

pylint:
  run: true
  options:
    disable:
      - missing-docstring
      - too-few-public-methods

mypy:
  run: true
```

## CI/CD Integration

### GitHub Actions Example
```yaml
name: Code Quality
on: [push, pull_request]
jobs:
  static-analysis:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.8
    - name: Install dependencies
      run: |
        pip install -r requirements.txt
        pip install -r requirements-dev.txt
    - name: Run Black
      run: black --check .
    - name: Run isort
      run: isort --check-only .
    - name: Run Flake8
      run: flake8 .
    - name: Run MyPy
      run: mypy .
    - name: Run Bandit
      run: bandit -r .
    - name: Run Safety
      run: safety check
```

### GitLab CI Example
```yaml
stages:
  - test
  - quality

static_analysis:
  stage: quality
  image: python:3.8
  before_script:
    - pip install -r requirements.txt
    - pip install -r requirements-dev.txt
  script:
    - black --check .
    - isort --check-only .
    - flake8 .
    - mypy .
    - bandit -r .
  only:
    - merge_requests
```

## Editor Integration

### VS Code Settings
```json
{
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true,
    "python.formatting.provider": "black",
    "python.formatting.blackArgs": ["--line-length", "88"],
    "editor.formatOnSave": true,
    "[python]": {
        "editor.codeActionsOnSave": {
            "source.organizeImports": true
        }
    }
}
```

### Vim/Neovim with ALE
```vim
let g:ale_linters = {
\   'python': ['flake8', 'mypy', 'bandit'],
\}
let g:ale_fixers = {
\   'python': ['black', 'isort'],
\}
let g:ale_fix_on_save = 1
```

## Best Practices

### 1. Tool Selection
Choose tools based on your needs:
- **Minimal setup**: Start with Black + Flake8
- **Comprehensive**: Add MyPy, Bandit, and Radon
- **Enterprise**: Include all tools with strict configurations

### 2. Configuration Management
- Keep configurations in version control
- Use standard configuration file names
- Document custom rules and exceptions

### 3. Gradual Adoption
```bash
# 1. Format existing code
black .

# 2. Sort imports
isort .

# 3. Fix obvious issues
autoflake --remove-all-unused-imports --recursive --remove-unused-variables --in-place .

# 4. Run linters and fix issues incrementally
```

### 4. False Positive Management
- Use inline comments to disable specific warnings:
  ```python
  x = 1  # noqa: E225
  ```
- Configure tool exclusions for legitimate cases
- Regular review of suppressed warnings

## Quality Gates

### Merge Requirements
- All linters must pass without errors
- Type checker must pass with no errors
- Security scanner must find no high-severity issues
- Code coverage must not decrease

### Thresholds
| Metric | Target |
|--------|--------|
| Cyclomatic Complexity | < 10 per function |
| Maintainability Index | > 20 |
| Code Coverage | > 80% |
| Security Issues | 0 high/critical |

## Troubleshooting Common Issues

### MyPy Import Errors
```ini
[mypy]
mypy_path = src
explicit_package_bases = True
```

### Black/Isort Conflicts
```toml
[tool.isort]
profile = "black"
```

### Large Project Performance
- Use exclude patterns in configurations
- Run tools on changed files only in CI
- Consider incremental analysis tools

## Quick Reference

| Tool | Purpose | Command |
|------|---------|---------|
| Black | Code formatting | `black .` |
| Isort | Import sorting | `isort .` |
| Flake8 | Style checking | `flake8 .` |
| MyPy | Type checking | `mypy .` |
| Bandit | Security scanning | `bandit -r .` |
| Safety | Dependency checking | `safety check` |

| Integration | Setup |
|------------|-------|
| Pre-commit | `pre-commit install` |
| CI/CD | Add tool commands to pipeline |
| Editors | Configure language server and linters |