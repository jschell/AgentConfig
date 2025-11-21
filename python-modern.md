# Python Development - Modern (with uv)

This document outlines modern Python development practices using `uv` as the primary package manager.

## Package Management

**CRITICAL: ONLY use uv, NEVER pip**

### Core Commands

- **Installation**: `uv add package`
- **Running tools**: `uv run tool`
- **Development dependencies**: `uv add --dev package`
- **Upgrading packages**: `uv add --dev package --upgrade-package package`

### FORBIDDEN Practices

- ❌ `uv pip install` - NEVER use this command
- ❌ `@latest` syntax - NEVER use this
- ❌ `pip install` - NEVER use pip directly
- ❌ Any pip commands - uv replaces pip entirely

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

- **Command**: `uv run --frozen pytest`
- **Async testing**: Use anyio, NOT asyncio
- **Coverage**: Test edge cases and error conditions
- **Requirements**:
  - New features REQUIRE tests
  - Bug fixes REQUIRE regression tests

### Common Issues

If tests aren't finding the anyio pytest mark, try:
```bash
PYTEST_DISABLE_PLUGIN_AUTOLOAD="" uv run --frozen pytest
```

## Code Formatting

### Ruff

Ruff is the primary formatting and linting tool.

**Commands:**
- Format code: `uv run --frozen ruff format .`
- Check issues: `uv run --frozen ruff check .`
- Fix issues: `uv run --frozen ruff check . --fix`

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

**Tool**: `uv run --frozen pyright`

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

### CRITICAL: Hook Failure Protocol

**NEVER use `--no-verify` or skip hooks**

When hooks fail:
1. Read the complete error output
2. Identify which tool failed and why
3. Fix the issue properly
4. Re-run hooks to verify
5. Only commit after all hooks pass

### Updating Ruff in Pre-commit

1. Check PyPI for latest version
2. Update `rev` in `.pre-commit-config.yaml`
3. Commit the config change first
4. Test with `pre-commit run --all-files`

## Error Resolution

### Fix Order (Always follow this sequence)

1. **Formatting** - Run `ruff format` first
2. **Type errors** - Fix with `pyright`
3. **Linting** - Fix with `ruff check`

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

- ✅ Ran `uv run --frozen ruff format .`
- ✅ Ran `uv run --frozen ruff check . --fix`
- ✅ Ran `uv run --frozen pyright`
- ✅ Ran `uv run --frozen pytest`
- ✅ All tests passing
- ✅ No type errors
- ✅ Pre-commit hooks passing
- ✅ Changes are minimal and focused
- ✅ Following existing patterns
- ✅ Public APIs documented
- ✅ Tests added for new features/bug fixes
