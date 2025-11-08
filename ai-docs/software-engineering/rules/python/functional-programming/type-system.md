## Rule: Type-Driven Domain Modeling
ID: `PY-FP-TYPING-001`
Scope: Core domain models and business logic (`src/core/`)
Quality Criterion: Compile-time safety and domain clarity.

### Core Principle
"Use the type system to make invalid states unrepresentable and document business rules at the type level."
(Complements immutable-value-objects.md and core-architecture-principles.md)

### Action:
Model domain invariants with types:
- Use `@dataclass(frozen=True)` for all value objects and entities
- Implement validation in `__post_init__` for complex invariants
- Create specific types for domain concepts (Email, Money, Quantity) rather than primitives
- Use sum types (via `Union` + validation) for mutually exclusive states

### Separate boundary and core types:
- Use Pydantic models ONLY at system boundaries (`src/adapters/`)
- Create explicit conversion functions from boundary to core types
- Never pass boundary types (Pydantic models) into domain logic
- Document conversion failures with domain-specific exceptions

### Type hinting standards:
- Enable strict mypy configuration with `disallow_untyped_defs = true`
- Use `TypeGuard` for validation functions
- Annotate higher-order functions with proper Callable types
- Prefer specific types over `Any` or `Dict[str, Any]`

### Implementation patterns:
```python
from dataclasses import dataclass, field
from decimal import Decimal, InvalidOperation
from typing import NewType, TypeGuard, Union, Literal, Callable
from pydantic import BaseModel  # Boundary concern only

# 1. Domain types with invariants
@dataclass(frozen=True)
class Email:
    value: str
    
    def __post_init__(self):
        if "@" not in self.value or "." not in self.value.split("@")[-1]:
            raise ValueError(f"Invalid email format: {self.value}")
    
    def __str__(self) -> str:
        return self.value

# 2. Sum types for state representation
PaymentStatus = Literal["pending", "completed", "failed", "refunded"]

@dataclass(frozen=True)
class Payment:
    id: str
    amount: Decimal
    status: PaymentStatus
    failure_reason: str | None = None
    
    def __post_init__(self):
        if self.status == "failed" and not self.failure_reason:
            raise ValueError("Failed payments require a reason")

# 3. Boundary to core conversion
class PaymentRequest(BaseModel):  # Boundary model only
    amount: float
    currency: str
    email: str

def payment_request_to_domain(request: PaymentRequest) -> Payment:
    """Convert boundary model to domain model with validation"""
    try:
        return Payment(
            id=generate_id(),
            amount=Decimal(str(request.amount)),
            status="pending",
            email=Email(request.email)
        )
    except (InvalidOperation, ValueError) as e:
        raise PaymentValidationError(f"Invalid payment data: {str(e)}")

# 4. Type guards for runtime validation
def is_valid_email(value: str) -> TypeGuard[Email]:
    try:
        Email(value)
        return True
    except ValueError:
        return False
```

### Verification:
```python
# Test domain invariants
def test_email_validation():
    with pytest.raises(ValueError):
        Email("invalid-email")
    
    assert str(Email("user@example.com")) == "user@example.com"

# Test boundary conversion
def test_payment_conversion():
    request = PaymentRequest(
        amount=99.99,
        currency="USD",
        email="user@example.com"
    )
    payment = payment_request_to_domain(request)
    assert payment.amount == Decimal("99.99")
    assert payment.status == "pending"

# mypy configuration (pyproject.toml)
[tool.mypy]
disallow_untyped_defs = true
warn_return_any = true
no_implicit_optional = true
strict_equality = true
check_untyped_defs = true
warn_unused_ignores = true
```

### Anti-Patterns
❌ Using strings for emails, IDs, or other domain concepts with invariants
❌ Mixing boundary and domain types in the same module
❌ Type assertions (`cast`, `# type: ignore`) without validation
❌ Optional fields without clear domain meaning
❌ Using dictionaries for domain objects instead of proper types
❌ Validation logic scattered outside of value objects
❌ Ignoring type errors rather than fixing the underlying model