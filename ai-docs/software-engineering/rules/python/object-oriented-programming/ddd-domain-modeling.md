## Purpose
Enforce pure domain modeling practices from "Cosmic Python". Keep domain layer completely isolated from infrastructure concerns.

### 1. Domain Model Isolation
- **Domain objects must never import infrastructure packages**:
  ```python
  # BAD: Domain model importing database libraries
  from sqlalchemy import Column, Integer  # ❌ Forbidden in domain/
  
  # GOOD: Domain model completely isolated
  @dataclass(frozen=True)
  class OrderLine:
      orderid: str
      sku: str
      qty: int
  ```
- **Business rules must live in domain entities/value objects**, not in services or controllers
- **Domain exceptions must be specific to business context**:
  ```python
  class OutOfStock(DomainException):
      def __init__(self, sku: str):
          self.sku = sku
          super().__init__(f"Out of stock for sku {sku}")
  ```

### 2. Entity & Value Object Implementation
- **Entities must**:
  - Have explicit identity (UUID or domain-specific identifier)
  - Encapsulate state-changing logic (no public setters)
  - Implement proper equality based on identity
  ```python
  class Batch:
      def __init__(self, ref: str, sku: str, qty: int, eta: Optional[date] = None):
          self.reference = ref
          self.sku = sku
          self.eta = eta
          self._purchased_quantity = qty
          self._allocations = set()
      
      def allocate(self, line: OrderLine):
          if self.can_allocate(line):
              self._allocations.add(line)
  ```
- **Value Objects must**:
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

### 3. Aggregate Design Rules
- **Aggregate root must enforce all invariants** within its boundary
- **Repositories must only return aggregate roots**, never child entities
- **Use optimistic concurrency control** with version numbers:
  ```python
  class Product:
      def __init__(self, sku: str, version_number: int = 0):
          self.sku = sku
          self.version_number = version_number
      
      def change_description(self, description: str):
          self.description = description
          self.version_number += 1  # For optimistic locking
  ```

### 4. Testing Requirements
- **Domain layer must be 100% testable without infrastructure**
- **Tests must express business rules in domain language**:
  ```python
  def test_allocating_to_a_batch_reduces_available_quantity():
      batch = Batch("batch-1", "SMALL-TABLE", qty=20, eta=date.today())
      line = OrderLine("order-1", "SMALL-TABLE", 2)
      
      batch.allocate(line)
      
      assert batch.available_quantity == 18
  ```

### 5. Anti-Patterns Explicitly Blocked
- **Anemic Domain Model**: Entities with only getters/setters and no behavior
- **Primitive Obsession**: Using raw strings/ints instead of value objects
- **Domain Objects with Infrastructure Concerns**: `.save()` methods on entities
- **Leaky Encapsulation**: Public attributes without behavior