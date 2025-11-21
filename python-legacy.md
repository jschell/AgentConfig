# Python Development - Legacy (without uv)

This document outlines traditional Python development practices using pip and virtual environments.

## Package Management

### Core Commands

- **Installation**: `pip install package`
- **Development dependencies**: `pip install -e .[dev]` or `pip install -r requirements-dev.txt`
- **Upgrading packages**: `pip install --upgrade package`
- **Virtual environment**: `python -m venv venv` and `source venv/bin/activate`

### Best Practices

- Always use virtual environments
- Keep `requirements.txt` and `requirements-dev.txt` updated
- Pin versions for production dependencies
- Document installation steps clearly

## Code Quality Requirements

### Type Hints

- Type hints are REQUIRED for all code
- Use explicit type annotations for function parameters and return values
- Use `Optional[Type]` for values that can be None
- Perform explicit None checks for Optional types

### Documentation

- Public APIs MUST have docstrings
- Docstrings should explain the "what" and "why", not just the "how"
- Include parameter descriptions and return value documentation
- Use examples in docstrings for complex functions

### Code Structure

- Functions must be focused and small
- Follow existing patterns exactly
- Line length: 120 chars maximum (88 for Ruff formatting)
- Reduce code duplication - refactor when you see patterns
- Match the style of surrounding code

## Testing Framework

### Running Tests

- **Command**: `pytest` or `python -m pytest`
- **Async testing**: Use anyio, NOT asyncio
- **Coverage**: Test edge cases and error conditions
- **Requirements**:
  - New features REQUIRE tests
  - Bug fixes REQUIRE regression tests

### Test Configuration

Ensure `pytest.ini` or `pyproject.toml` is properly configured for:
- Test discovery paths
- Asyncio/anyio plugins
- Coverage settings
- Markers and fixtures

## Code Formatting

### Ruff

Ruff is the primary formatting and linting tool.

**Commands:**
- Format code: `ruff format .`
- Check issues: `ruff check .`
- Fix issues: `ruff check . --fix`

**Installation:**
```bash
pip install ruff
```

**Critical Issues to Watch:**
- Line length (88 chars default)
- Import sorting (I001)
- Unused imports
- Proper line wrapping:
  - Strings: use parentheses for continuation
  - Function calls: multi-line with proper indentation
  - Imports: split into multiple lines

**Line Wrapping Examples:**

```python
# Good - String with parentheses
message = (
    "This is a very long string that needs to be "
    "wrapped across multiple lines for readability"
)

# Good - Function call
result = some_function(
    parameter1="value1",
    parameter2="value2",
    parameter3="value3"
)

# Good - Imports
from some.long.module.path import (
    FirstClass,
    SecondClass,
    ThirdClass,
)
```

### Type Checking

**Tool**: `pyright` or `mypy`

**Installation:**
```bash
pip install pyright
# or
pip install mypy
```

**Requirements:**
- Explicit None checks for Optional types
- Type narrowing for string types
- Version warnings can be ignored if type checks pass

**Common Type Patterns:**

```python
# Explicit None checking
def process(value: Optional[str]) -> str:
    if value is None:
        return ""
    return value.upper()

# Type narrowing
def handle_input(data: Union[str, int]) -> str:
    if isinstance(data, str):
        return data.upper()
    return str(data)
```

## Pre-commit Hooks

### Configuration

- Config file: `.pre-commit-config.yaml`
- Runs automatically on git commit
- Tools included: Prettier (YAML/JSON), Ruff (Python)

### Setup

```bash
pip install pre-commit
pre-commit install
```

### CRITICAL: Hook Failure Protocol

**NEVER use `--no-verify` or skip hooks**

When hooks fail:
1. Read the complete error output
2. Identify which tool failed and why
3. Fix the issue properly
4. Re-run hooks to verify
5. Only commit after all hooks pass

### Updating Tools in Pre-commit

1. Check PyPI for latest versions
2. Update `rev` in `.pre-commit-config.yaml`
3. Run `pre-commit autoupdate` (optional)
4. Test with `pre-commit run --all-files`

## Error Resolution

### Fix Order (Always follow this sequence)

1. **Formatting** - Run `ruff format .` first
2. **Type errors** - Fix with `pyright` or `mypy`
3. **Linting** - Fix with `ruff check .`

### Common Issues and Solutions

#### Line Length Violations

```python
# Bad
some_very_long_function_call(parameter1, parameter2, parameter3, parameter4, parameter5)

# Good
some_very_long_function_call(
    parameter1,
    parameter2,
    parameter3,
    parameter4,
    parameter5
)
```

#### Type Errors

- Get full line context from the error
- Check for Optional types that need None checks
- Add type narrowing with isinstance()
- Verify function signatures match

#### Import Issues

- Remove unused imports
- Sort imports properly (Ruff handles this)
- Split long import lines

## Dependency Management

### Requirements Files

**requirements.txt** (production):
```
package1==1.2.3
package2>=2.0.0,<3.0.0
```

**requirements-dev.txt** (development):
```
-r requirements.txt
pytest>=7.0.0
ruff>=0.1.0
pyright>=1.1.0
pre-commit>=3.0.0
```

### Virtual Environment Workflow

```bash
# Create environment
python -m venv venv

# Activate (Linux/Mac)
source venv/bin/activate

# Activate (Windows)
venv\Scripts\activate

# Install dependencies
pip install -r requirements-dev.txt

# Deactivate when done
deactivate
```

## Git Integration

### Commit Trailers

For commits fixing bugs or adding features based on user reports:

```bash
git commit --trailer "Reported-by:<name>"
```

For commits related to a GitHub issue:

```bash
git commit --trailer "Github-Issue:#<number>"
```

**FORBIDDEN:**
- NEVER mention `co-authored-by` or similar aspects
- NEVER mention the tool used to create the commit message or PR

## Best Practices Checklist

Before committing, verify:

- ✅ Virtual environment activated
- ✅ Ran `ruff format .`
- ✅ Ran `ruff check . --fix`
- ✅ Ran `pyright` or `mypy`
- ✅ Ran `pytest`
- ✅ All tests passing
- ✅ No type errors
- ✅ Pre-commit hooks passing
- ✅ Changes are minimal and focused
- ✅ Following existing patterns
- ✅ Public APIs documented
- ✅ Tests added for new features/bug fixes
- ✅ Requirements files updated if dependencies changed
