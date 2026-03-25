---
name: stand-py
description: Python >= 3.11 coding standards. Use when writing Python code. Requires type hints, return types, Google-style docstrings, trailing commas, explicit kwargs, StrEnum with auto(), dataclasses, pytest-style tests.
---

# Python Code Standards

Standards for Python code (>= 3.11).

## Runtime

- All Python code should be run with `uv`

## PEP Compliance

- PEP 604: Use union types as `X | Y` (not `Union[X, Y]`)
- PEP 673: Use `Self` type for self-referential types

## Required Elements

- Type hints on ALL function parameters
- Return types on ALL functions
- Docstrings in Google Style Guide format for:
  - Modules
  - Classes
  - Functions/methods

## Docstring Format (Google Style)

```python
def function_with_docstring(
    param1: str,
    param2: int,
) -> bool:
    """Short description of function.

    Longer description if needed.

    Args:
        param1: Description of param1.
        param2: Description of param2.

    Returns:
        Description of return value.

    Raises:
        ValueError: When something is wrong.
    """
```

## Design Patterns

- Prefer `dataclasses` over `defaultdict`
- Each dataclass should be in a separate file
- String Enums should use `StrEnum` with `auto()`
- Use `auto()` with all Enums where it makes sense

## Formatting Rules

- More than 1 arg/param requires a trailing comma:
  ```python
  # Good
  def foo(bar: str, baz: int,) -> None:

  # Bad
  def foo(bar: str, baz: int) -> None:
  ```

- Be explicit with function calls when more than 1 arg:
  ```python
  # Good
  foo(bar=bar, baz=baz)

  # Bad
  foo(bar, baz)
  ```

- Single arg can be positional:
  ```python
  # OK
  foo(bar)
  ```

## Linting

- Use `lintro` for all linting (not native tools)
- Code must pass `lintro chk` with zero issues

## Ignoring Issues

- Ignoring issues is NOT permitted unless absolutely justified as a last resort
- Docstrings are required even for tests - no exceptions
- When an ignore IS necessary, add a comment explaining why:
  ```python
  # Ignore: External API returns dynamic structure
  data: Any = api.fetch()  # type: ignore[no-untyped-call]
  ```

## Testing (Pytest)

- NEVER use unittest style or test classes
- Use pytest-style test functions only
- Leverage `conftest.py` for shared fixtures
- Use fixtures for reusable setup/teardown
- Use `@pytest.mark.parametrize` to reduce duplication

```python
# WRONG
class TestFoo(unittest.TestCase):
    def test_bar(self):
        ...

# CORRECT
def test_foo_bar() -> None:
    """Verify foo handles bar correctly."""
    ...
```
