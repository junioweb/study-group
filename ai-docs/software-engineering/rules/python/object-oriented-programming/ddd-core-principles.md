## Purpose
Establish foundational Domain-Driven Design principles for Python projects. Ensures domain model purity and proper separation of concerns.

### 1. Domain Model Purity
- **NO infrastructure dependencies** in domain layer:
  ```python
  # BAD: Domain model importing database libraries
  from sqlalchemy import Column, String  # ❌ Never in domain/
  
  # GOOD: Domain model completely isolated
  @dataclass(frozen=True)
  class OrderLine:
      orderid: str
      sku: str
      qty: int
  ```
- **All business rules** must live in domain entities/value objects, not in services or controllers
- **Domain exceptions** must be specific to business context:
  ```python
  class OutOfStock(DomainException): ...
  class InvalidSku(DomainException): ...
  ```

### 2. Entity & Value Object Rules
- **Entities** must:
  - Have explicit identity (UUID preferred)
  - Encapsulate state-changing logic (no public setters)
  - Implement proper equality based on identity
  ```python
  class Batch:
      def __init__(self, ref: str, sku: str, qty: int):
          self.reference = ref
          self.sku = sku
          self._purchased_quantity = qty
          self._allocations = set()
      
      def allocate(self, line: OrderLine):
          if self.can_allocate(line):
              self._allocations.add(line)
  ```
- **Value Objects** must:
  - Be immutable (`@dataclass(frozen=True)`)
  - Implement structural equality
  - Validate in constructor
  ```python
  @dataclass(frozen=True)
  class Money:
      amount: Decimal
      currency: str
      
      def __post_init__(self):
          if self.amount < 0:
              raise ValueError("Money cannot be negative")
  ```

### 3. Layered Architecture Enforcement
| Layer | Responsibilities | Dependencies Allowed |
|-------|------------------|----------------------|
| **domain/** | Entities, value objects, domain services, exceptions | None (pure Python) |
| **application/** | Use cases, service layer, DTOs | domain/ only |
| **infrastructure/** | Database, APIs, external services | domain/, application/ |
| **interfaces/** | API routes, CLI commands, UI handlers | application/, infrastructure/ |

- **Dependency flow must be unidirectional**: interfaces → infrastructure → application → domain
- **NO circular dependencies** between layers

### 4. Domain Service & Function Rules
- **Domain services** must be stateless functions when possible:
  ```python
  # GOOD: Stateless domain function
  def allocate(line: OrderLine, batches: List[Batch]) -> str:
      ...
  ```
- **Services must not contain infrastructure concerns** (logging, HTTP calls, DB access)
- **Prefer functions over classes** for domain services without identity

### 5. Anti-Patterns Explicitly Blocked
- **Anemic Domain Model**: Entities with only getters/setters and no behavior
- **Leaky Abstractions**: Domain objects exposing internal state for infrastructure to manipulate
- **God Objects**: Classes with multiple responsibilities violating SRP
- **Primitive Obsession**: Using raw strings/ints instead of value objects
- **Service Locator**: Hidden dependencies making code untestable

### 6. Testing Requirements
- **Domain layer must be 100% testable without infrastructure**
- **Unit tests must pass with zero external dependencies**
- **Test domain behavior, not implementation details**:
  ```python
  def test_allocating_to_a_batch_reduces_available_quantity():
      batch = Batch("b1", "SMALL-TABLE", qty=20, eta=date.today())
      line = OrderLine("o1", "SMALL-TABLE", 2)
      
      batch.allocate(line)
      
      assert batch.available_quantity == 18
  ```