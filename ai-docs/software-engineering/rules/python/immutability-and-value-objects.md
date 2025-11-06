**Purpose**  
Default to immutable, self-validating value objects in the *Functional Core*.

**Guidelines**
- All core data classes must be **immutable** (`@dataclass(frozen=True)`)  
- Validation in `__post_init__` or factory methods  
- Never expose mutable collections — return copies or tuples  
- Prefer `typing.NamedTuple` or `@dataclass` over plain `dict`/`list`

**Examples**  
```python
from dataclasses import dataclass
from decimal import Decimal

@dataclass(frozen=True)
class Money:
    amount: Decimal
    currency: str

    def __post_init__(self):
        if self.amount < 0:
            raise ValueError("Money cannot be negative")
        if self.currency not in {"USD", "BRL"}:
            raise ValueError(f"Unsupported currency: {self.currency}")

    def add(self, other: "Money") -> "Money":
        if self.currency != other.currency:
            raise ValueError("Cannot add different currencies")
        return Money(self.amount + other.amount, self.currency)
```

**Anti-Patterns**  
- `user.email = "new@ex.com"` — mutation  
- `def process(items: list): items.append(...)` — side effect on input  
- `{ "amount": 100, "currency": "USD" }` — untyped, mutable, unvalidated