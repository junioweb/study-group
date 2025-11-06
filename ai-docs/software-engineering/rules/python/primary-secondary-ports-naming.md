**Purpose**  
Standardize port naming and testing strategy based on actor direction.

**Guidelines**
- **Primary (driving) ports** — *app is driven by external actor*:  
  - Naming: `*CommandPort`, `*QueryPort`  
  - Examples: `OrderCommandPort`, `UserQueryPort`  
  - Test with: **harnesses** (e.g., pytest calling port directly)  
  - Typically implemented by: API handlers, CLI commands, event listeners  

- **Secondary (driven) ports** — *app drives external system*:  
  - Naming: `*GatewayPort`, `*RepositoryPort`, `*PublisherPort`  
  - Examples: `PaymentGatewayPort`, `OrderRepositoryPort`  
  - Test with: **mocks/fakes** (e.g., `InMemoryOrderRepo`)  
  - Typically implemented by: DB clients, HTTP clients, message producers  

**Implementation**  
```python
# src/core/ports/order_commands.py
from typing import Protocol
from .models import Order, OrderId

class OrderCommandPort(Protocol):
    def create_order(self, order: Order) -> OrderId: ...
    def cancel_order(self, order_id: OrderId) -> None: ...

# src/core/ports/payment_gateway.py
from .models import PaymentRequest, PaymentResult

class PaymentGatewayPort(Protocol):
    def process_payment(self, req: PaymentRequest) -> PaymentResult: ...
```

**Anti-Patterns**  
- Naming ports `IService`, `IHandler` — vague  
- Using the same port for reading and writing (violates CQS)  
- Mocking primary ports in unit tests (should be called directly)