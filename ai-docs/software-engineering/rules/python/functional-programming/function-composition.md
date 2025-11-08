## Rule: Declarative Function Composition
ID: `PY-FP-COMPOSE-001`
Scope: Business logic and data transformation modules (`src/core/`, `src/services/`)
Quality Criterion: Code readability and business logic clarity.

### Core Principle
"Build complex operations by composing small, reusable pure functions rather than writing monolithic procedures."
(Complements functional-code-imperative-shell.md and core-architecture-principles.md)

### Action:
Structure transformations as composable pipelines:
- Use `toolz.compose` for mathematical composition (right-to-left)
- Use `toolz.pipe` for dataflow composition (left-to-right)
- Create small functions with single responsibilities (≤10 lines)
- Type hint transformation functions as `Callable[[A], B]`

### Naming conventions for composition:
- Pipeline functions: `process_X`, `transform_Y`, `calculate_Z`
- Transformation steps: `validate_X`, `enrich_Y`, `format_Z`
- Use domain terminology rather than technical terms

### Handle partial application explicitly:
- Use `functools.partial` for parameter binding
- Create specialized transformation factories when needed
- Avoid implicit state in composed functions

### Implementation patterns:
```python
from toolz import compose, pipe, curry
from typing import Callable, Dict, Any

# 1. Single-responsibility transformations
def validate_user(data: Dict[str, Any]) -> Dict[str, Any]:
    if not data.get("email"):
        raise ValueError("Email is required")
    return data

def normalize_email(data: Dict[str, Any]) -> Dict[str, Any]:
    data["email"] = data["email"].lower().strip()
    return data

def enrich_with_defaults(data: Dict[str, Any]) -> Dict[str, Any]:
    return {**{"role": "user", "active": True}, **data}

# 2. Composition options
# Right-to-left mathematical composition
create_user = compose(
    persist_user,
    enrich_with_defaults,
    normalize_email,
    validate_user
)

# Left-to-right dataflow composition (often more readable)
def process_payment(payment_data: Dict[str, Any]) -> PaymentResult:
    return pipe(
        payment_data,
        validate_payment,
        calculate_fees,
        apply_discounts,
        execute_transaction,
        format_receipt
    )

# 3. Parameterized transformations
@curry
def apply_tax(rate: float, amount: float) -> float:
    return amount * (1 + rate)

apply_vat = apply_tax(0.20)  # Specialized function for VAT
```

### Verification:
```python
# Test entire pipeline
def test_user_creation_pipeline():
    input_data = {"email": "  TEST@EXAMPLE.COM  ", "name": "Alice"}
    result = create_user(input_data)
    assert result.email == "test@example.com"  # Normalized
    assert result.role == "user"  # Default applied
    assert result.is_valid  # Validation passed

# Test individual components
def test_normalize_email():
    data = {"email": "  TEST@EXAMPLE.COM  "}
    result = normalize_email(data)
    assert result["email"] == "test@example.com"
```

### Anti-Patterns
❌ Deeply nested function calls: `f(g(h(x)))`
❌ Temporary variables for intermediate results in linear pipelines
❌ Composition with side effects (breaking referential transparency)
❌ Using class methods for pure transformations (favor free functions)
❌ Over-engineering simple operations with unnecessary composition