## Purpose
Implement Repository and Unit of Work patterns correctly for transactional consistency and testability.

### 1. Repository Pattern Requirements
- **Interface must be defined in domain layer**, implementation in infrastructure:
  ```python
  # domain/repositories.py
  from typing import Protocol, Optional
  
  class AbstractRepository(Protocol):
      def add(self, batch: Batch) -> None: ...
      def get(self, reference: str) -> Optional[Batch]: ...
      def list(self) -> List[Batch]: ...
  
  # infrastructure/repository.py
  class SqlAlchemyRepository(AbstractRepository):
      def __init__(self, session: Session):
          self.session = session
      
      def add(self, batch: Batch):
          self.session.add(batch)
  ```
- **Repositories must only handle aggregates**, never child entities directly
- **Minimal interface principle**: Only essential methods (`add()`, `get()`, domain-specific queries)

### 2. Unit of Work Implementation
- **Must use context manager pattern** for transaction boundaries:
  ```python
  class UnitOfWork:
      def __init__(self, session_factory):
          self.session_factory = session_factory
      
      def __enter__(self):
          self.session = self.session_factory()
          self.batches = SqlAlchemyRepository(self.session)
          return self
      
      def __exit__(self, *args):
          self.rollback()
      
      def commit(self):
          self.session.commit()
      
      def rollback(self):
          self.session.rollback()
  ```
- **Service layer must use UoW explicitly**:
  ```python
  def allocate_endpoint(uow: UnitOfWork, orderid: str, sku: str, qty: int):
      with uow:
          line = OrderLine(orderid, sku, qty)
          allocate(line, uow.batches.list())
          uow.commit()
  ```

### 3. Mapping Rules
- **Domain-ignorant mapping**: ORM configurations must not pollute domain model
- **Imperative mapping preferred** over declarative for DDD:
  ```python
  # infrastructure/mapping.py
  def start_mappers():
      mapper_registry.map_imperatively(
          Batch,
          batches_table,
          properties={
              "_allocations": relationship(OrderLine, collection_class=set)
          }
      )
  ```

### 4. Testing Strategy
- **Fake implementations for unit tests**:
  ```python
  class FakeRepository(AbstractRepository):
      def __init__(self):
          self._batches = set()
      
      def add(self, batch):
          self._batches.add(batch)
      
      def get(self, reference):
          return next((b for b in self._batches if b.reference == reference), None)
  ```
- **Integration tests must verify real repository behavior** against database
- **UoW tests must validate commit/rollback behavior** with real transactions

### 5. Anti-Patterns Explicitly Blocked
- **Active Record pattern** in domain layer
- **Repositories returning query objects** (leaks infrastructure)
- **Multiple UoW instances** in same transaction scope
- **Domain entities aware of persistence** (no `.save()` methods on entities)