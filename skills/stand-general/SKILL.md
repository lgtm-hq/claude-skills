---
name: stand-general
description: Global coding standards for all projects and languages. Use when writing any code. Covers linting with lintro, testing with coverage, semantic commits, PR creation, and code review with CodeRabbit.
---

# Coding Standards

Global standards that apply to all projects and languages.

## Linting

- All linting MUST be done with `lintro`
- Available commands: `lintro fmt`, `lintro chk`, `lintro tst`
- Native tools should NOT be used directly

## Ignoring Issues

- Ignoring issues is NOT permitted unless it's the absolute last resort and completely justified
- This includes docstrings for tests - they are still required
- When an ignore IS applied, a comment MUST explain why:
  ```python
  # Ignore: Third-party library returns untyped data
  value: Any = external_lib.get_data()  # type: ignore[no-untyped-call]
  ```

## Testing

- Test runs should ALWAYS generate a coverage report
- New code requires new tests
- Code changes require updated tests

## Commits

Pre-commit requirements:
1. All linting must pass (`lintro chk`)
2. All tests must pass (`lintro tst`)
3. Docker builds must pass (where applicable)

Commit rules:
- Every commit must be signed/verified (`git commit -S`)
- Use semantic prefixes: `fix:`, `feat:`, `chore:`, `docs:`, `refactor:`, `test:`, `build:`, `ci:`, `perf:`, `style:`
- Use imperative mood ("Add feature" not "Added feature")

## Pull Requests

- Use the PR template from the current repository
- Assign PR to me (`--assignee @me`)
- Add appropriate labels

## Code Review

- Use CodeRabbit with `--prompt-only` flag
- For uncommitted changes: `coderabbit --prompt-only -t uncommitted`
- LIMIT: Maximum 3 CodeRabbit runs per change set

## Language-Specific

See also:
- `/stand-py` - Python >= 3.11 standards
- `/stand-ts` - TypeScript/JavaScript standards
