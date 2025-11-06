## Purpose
Implement event-driven architecture and CQRS patterns for scalable, maintainable systems.

### 1. Command-Query Separation
- **Commands must**:
  - Use imperative naming (`CreateBatch`, `AllocateOrder`)
  - Modify only one aggregate
  - Return no data (only success/failure status)
- **Queries must**:
  - Use noun-based naming (`GetBatch`, `ListOrders`)
  - Be side-effect free
  - Return DTOs or primitive data structures only
- **API endpoints must follow separation**:
  ```python
  # POST endpoint (command)
  @app.post("/batches")
  async def add_batch(batch_cmd: dict):
      cmd = CreateBatch(
          ref=batch_cmd["ref"],
          sku=batch_cmd["sku"],
          qty=batch_cmd["qty"],
          eta=batch_cmd.get("eta")
      )
      message_bus.handle(cmd)  # No return data
      return {"status": "accepted"}, 202
  
  # GET endpoint (query)
  @app.get("/allocations/{orderid}")
  async def allocations(orderid: str) -> dict:
      return views.get_allocations(orderid)  # Read model only
  ```

### 2. Event-Driven Integration
- **Domain events must be immutable**:
  ```python
  @dataclass(frozen=True)
  class BatchCreated:
      ref: str
      sku: str
      qty: int
      eta: Optional[date]
  
  @dataclass(frozen=True)
  class Allocated:
      orderid: str
      batchref: str
      sku: str
  ```
- **Message bus must separate command/event handling**:
  ```python
  class MessageBus:
      def handle_command(self, command):
          handler = self.command_handlers[type(command)]
          return handler(command)  # Raise on failure
      
      def handle_event(self, event):
          for handler in self.event_handlers[type(event)]:
              try:
                  handler(event)
              except Exception as e:
                  log_error(f"Handler failed: {e}")  # Continue processing
  ```

### 3. Read Model Implementation
- **Read models must be denormalized** for query performance
- **Update via event handlers**:
  ```python
  def update_read_model_on_allocated(event: Allocated):
      with engine.begin() as conn:
          conn.execute(
              "INSERT INTO allocations_view (orderid, sku, batchref) VALUES (:orderid, :sku, :batchref)",
              dict(orderid=event.orderid, sku=event.sku, batchref=event.batchref)
          )
  ```
- **Multiple read models allowed** for different query patterns (SQL, Redis, etc.)

### 4. Transactional Boundaries
- **Commands must be transactional**: All changes in single UoW
- **Events must be published after commit**:
  ```python
  with uow:
      result = command_handler(command)
      uow.commit()
  
  for event in uow.collect_new_events():
      message_bus.handle_event(event)
  ```
- **Read models accept eventual consistency** (not part of transaction)

### 5. Anti-Patterns Explicitly Blocked
- **Returning domain entities from queries**
- **Commands that return data** (violates CQS)
- **Synchronous HTTP calls between services** (use events instead)
- **Event handlers with side effects on write model**
- **Fat events containing full entity state** (include only needed data)

### 6. Testing Requirements
- **Command handlers must be tested with unit tests** and fake UoW
- **Event handlers must be tested in isolation** with fake dependencies
- **End-to-end tests must verify eventual consistency** between write and read models
- **Idempotency tests required** for all event handlers