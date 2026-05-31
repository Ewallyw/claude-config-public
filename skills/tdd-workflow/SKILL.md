---
name: tdd-workflow
description: Use this skill when writing new features, fixing bugs, or refactoring code. Enforces test-driven development with coverage requirements including unit, integration, and E2E tests.
---

# Test-Driven Development Workflow

This skill ensures all code development follows TDD principles with comprehensive test coverage.

## When to Activate
- Writing new features or functionality
- Fixing bugs or issues
- Refactoring existing code
- Adding API endpoints

## Core Principles

### 1. Tests BEFORE Code
ALWAYS write tests first, then implement code to make tests pass.

### 2. Coverage Requirements
- Minimum 80% coverage (unit + integration + E2E)
- All edge cases covered, error scenarios tested, boundary conditions verified

### 3. Git Checkpoints
- Create a checkpoint commit after each TDD stage
- One commit for RED (failing test), one for GREEN (fix), optional one for refactor
- Verify commits are on the current active branch before continuing

## TDD Steps

### Step 1: Write User Journeys
```
As a [role], I want to [action], so that [benefit]
```

### Step 2: Generate Test Cases
```python
class TestSemanticSearch:
    def test_returns_relevant_results(self):
        ...
    def test_handles_empty_query(self):
        ...
    def test_fallback_when_service_unavailable(self):
        ...
```

### Step 3: Run Tests — RED
```bash
pytest          # Python
npm test        # JavaScript
```
Tests MUST fail for the right reason (missing implementation, not syntax error).

### Step 4: Implement — GREEN
Write minimal code to make tests pass. No extra features.

### Step 5: Run Tests — GREEN
```bash
pytest          # Python
npm test        # JavaScript
```
All tests pass. If not, fix code, not tests.

### Step 6: Refactor
Improve code quality while keeping tests green. Remove duplication, improve naming, enhance readability.

### Step 7: Verify Coverage
```bash
pytest --cov --cov-report=term-missing    # Python
npm test -- --coverage                    # JavaScript
```
Target: 80%+ coverage.

## Python Example

```python
# test_calculator.py — RED phase
def test_add_returns_sum():
    calc = Calculator()
    assert calc.add(2, 3) == 5

def test_divide_by_zero_raises():
    calc = Calculator()
    with pytest.raises(ValueError):
        calc.divide(10, 0)

# calculator.py — GREEN phase
class Calculator:
    def add(self, a, b):
        return a + b
    
    def divide(self, a, b):
        if b == 0:
            raise ValueError("Cannot divide by zero")
        return a / b
```

## Best Practices
1. **Write Tests First** — Always TDD
2. **One Assert Per Test** — Focus on single behavior
3. **Descriptive Test Names** — Explain what's tested
4. **Arrange-Act-Assert** — Clear test structure
5. **Mock External Dependencies** — Isolate unit tests
6. **Test Edge Cases** — Null, empty, boundary values
7. **Test Error Paths** — Not just happy paths
8. **Keep Tests Fast** — Unit tests < 50ms each
9. **Clean Up After Tests** — No side effects
10. **Review Coverage Reports** — Identify gaps

## Success Metrics
- 80%+ code coverage achieved
- All tests passing (green)
- No skipped or disabled tests
- Tests catch bugs before production
