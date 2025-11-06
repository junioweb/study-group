## Purpose
Structure service layer as the orchestration layer between domain logic and infrastructure concerns.

### 1. Service Layer Responsibilities
- **Must contain only orchestration logic**:
  - Transaction management (via Unit of Work)
  - Input validation (not business rules)
  - Calling domain logic
  - Handling domain exceptions
- **Service functions must be stateless**:
  ```python
  # GOOD: Stateful service as class with explicit state
  class AllocationService:
      def __init__(self, uow: UnitOfWork):
          self.uow = uow
      
      def allocate(self, orderid: str, sku: str, qty: int) -> str:
          with self.uow:
              line = OrderLine(orderid, sku, qty)
              batchref = allocate(line, self.uow.batches.list())
              self.uow.commit()
              return batchref
  ```
- **Function signatures must use primitive types or DTOs**, not domain objects:
  ```python
  # GOOD: Service interface uses primitives
  def allocate(orderid: str, sku: str, qty: int, uow: UnitOfWork) -> str:
      ...
  ```

### 2. Separation of Concerns
| Layer | Responsibilities | Dependencies Allowed |
|-------|------------------|----------------------|
| **Domain** | Business rules, entities, value objects | None (pure Python) |
| **Service Layer** | Use case orchestration, transaction boundaries | Domain only |
| **Infrastructure** | Database, APIs, external services | Domain, Service Layer |
| **Entry Points** | HTTP handlers, CLI commands, message consumers | Service Layer, Infrastructure |

- **Entry Points must be "thin"**:
  ```python
  # GOOD: Thin Flask endpoint
  @app.post("/allocate")
  def allocate_endpoint():
      try:
          allocate(request.json["orderid"], request.json["sku"], request.json["qty"], uow)
          return {"status": "ok"}, 201
      except OutOfStock as e:
          return {"message": f"Out of stock for sku {e.sku}"}, 400
  ```

### 3. Testing Strategy
- **Service layer tests must use fake repositories/UoW**:
  ```python
  def test_allocated_returns_allocation():
      uow = FakeUnitOfWork()
      uow.batches.add(Batch("batch1", "COMPLICATED-LAMP", 100, eta=None))
      
      result = allocate("o1", "COMPLICATED-LAMP", 10, uow)
      
      assert result == "batch1"
  ```
- **Tests must validate business outcomes**, not implementation details
- **Edge-to-edge tests** for complete workflow validation

### 4. Anti-Patterns Explicitly Blocked
- **Anemic Domain Model**: Moving business logic to services
- **Fat Controllers**: Entry points containing business logic
- **Service Locator**: Hidden dependencies in service layer
- **Transactional Scripts**: Services with complex conditional logic instead of domain objects

### 5. Dependency Injection
- **Explicit dependency injection** required for all services:
  ```python
  # GOOD: Explicit dependencies
  def allocate(orderid: str, sku: str, qty: int, uow: UnitOfWork) -> str:
      ...
  
  # BAD: Implicit dependencies
  def allocate(orderid: str, sku: str, qty: int) -> str:
      uow = get_current_uow()  # ❌ Hidden dependency
  ```