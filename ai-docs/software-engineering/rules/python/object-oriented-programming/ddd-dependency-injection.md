## Purpose
Implement proper dependency injection to maintain clean architecture boundaries and testability.

### 1. Dependency Inversion Enforcement
- **Domain layer owns all interfaces**:
  ```python
  # domain/notifications.py
  from abc import ABC, abstractmethod
  
  class AbstractNotifications(ABC):
      @abstractmethod
      def send(self, orderid: str, message: str) -> bool:
          pass
  ```
- **Infrastructure implements domain interfaces**:
  ```python
  # infrastructure/notifications.py
  class EmailNotifications(AbstractNotifications):
      def __init__(self, smtp_server: str):
          self.smtp_server = smtp_server
      
      def send(self, orderid: str, message: str) -> bool:
          # Implementation details
  ```
- **Composition root must be single entrypoint**:
  ```python
  # bootstrap.py
  def bootstrap(
      db_uri: str = settings.DB_URI,
      smtp_server: str = settings.SMTP_SERVER
  ) -> MessageBus:
      engine = create_engine(db_uri)
      session_factory = sessionmaker(bind=engine)
      
      notifications = EmailNotifications(smtp_server)
      uow = SqlAlchemyUnitOfWork(session_factory)
      
      dependencies = {
          "uow": uow,
          "notifications": notifications,
      }
      injected_handlers = {
          command_type: partial(handler, **dependencies)
          for command_type, handler in HANDLERS.items()
      }
      return MessageBus(injected_handlers)
  ```

### 2. Adapter Pattern Implementation
- **Adapters must implement domain interfaces**:
  ```python
  # infrastructure/pubsub.py
  class RedisEventPublisher(AbstractEventPublisher):
      def __init__(self, redis_client: redis.Redis):
          self.redis = redis_client
      
      def publish(self, channel: str, event: DomainEvent):
          self.redis.publish(channel, json.dumps(asdict(event)))
  ```
- **Fake adapters required for testing**:
  ```python
  # tests/fakes.py
  class FakeNotifications(AbstractNotifications):
      def __init__(self):
          self.sent = []
      
      def send(self, orderid: str, message: str) -> bool:
          self.sent.append((orderid, message))
          return True
  ```

### 3. DI Techniques in Python
- **Manual DI with partials** for simple cases:
  ```python
  # GOOD: Explicit dependency injection with partial
  allocate_handler = partial(allocate, uow=uow)
  ```
- **Handler classes** for complex dependencies:
  ```python
  # GOOD: Handler class with explicit dependencies
  class AllocateHandler:
      def __init__(self, uow: UnitOfWork, notifications: AbstractNotifications):
          self.uow = uow
          self.notifications = notifications
      
      def __call__(self, command: Allocate):
          with self.uow:
              batchref = allocate(command.orderid, command.sku, command.qty, self.uow)
              self.uow.commit()
              self.notifications.send(command.orderid, f"Allocated to {batchref}")
  ```
- **Bootstrap script** must handle all composition:
  ```python
  # app.py
  from bootstrap import bootstrap
  
  message_bus = bootstrap()
  
  @app.post("/allocate")
  def allocate_endpoint():
      message_bus.handle(Allocate(**request.json))
      return {"status": "accepted"}, 202
  ```

### 4. Testing Strategy
- **Unit tests must use fake dependencies only**:
  ```python
  def test_allocate_sends_email_on_successful_allocation():
      uow = FakeUnitOfWork()
      notifications = FakeNotifications()
      handler = AllocateHandler(uow, notifications)
      
      handler(Allocate("o1", "SKU", 10))
      
      assert notifications.sent == [("o1", "Allocated to batch1")]
  ```
- **Integration tests must use real dependencies with test containers**
- **Bootstrap must be tested with different configurations** (dev, prod, test)

### 5. Anti-Patterns Explicitly Blocked
- **Service locator pattern** (hidden dependencies)
- **Global state** (singletons, module-level variables)
- **Concrete implementations in domain layer**
- **Hidden dependencies** via `mock.patch` as primary testing strategy

### 6. Package Structure
```
src/
├── domain/          # Pure domain model, interfaces
├── service_layer/   # Use cases, command handlers
├── infrastructure/  # Database, APIs, external integrations
├── entrypoints/     # API routes, CLI commands, message consumers
└── bootstrap.py     # Composition root
```