## Purpose
Apply SOLID principles effectively in Python codebases to enable maintainable, extensible systems.

### 1. Single Responsibility Principle (SRP)
- **Classes/functions must have exactly one reason to change**:
  ```python
  # BAD: Violates SRP - handles calculation and persistence
  class OrderProcessor:
      def process(self, order):
          total = self.calculate_total(order)
          self.save_to_database(order)
          self.send_confirmation_email(order)
  
  # GOOD: SRP compliant - each class has single responsibility
  class OrderCalculator:
      def calculate_total(self, order): ...
  
  class OrderRepository:
      def save(self, order): ...
  
  class OrderNotifier:
      def send_confirmation(self, order): ...
  ```
- **Violations blocked**:
  - Classes with "and" in their names (`OrderProcessorAndLogger`)
  - Entities with serialization methods (`to_json()`)
  - Services with mixed concerns (business logic + logging)

### 2. Open/Closed Principle (OCP)
- **Extend behavior via polymorphism, never modification**:
  ```python
  # GOOD: Strategy pattern for tax calculation
  class TaxCalculator(ABC):
      @abstractmethod
      def calculate(self, amount: Decimal) -> Decimal: ...
  
  class IcmsCalculator(TaxCalculator):
      def calculate(self, amount: Decimal) -> Decimal:
          return amount * Decimal("0.17")
  
  class IpiCalculator(TaxCalculator):
      def calculate(self, amount: Decimal) -> Decimal:
          return amount * Decimal("0.10")
  ```
- **Enforced rules**:
  - Conditional logic based on type (`if type == "X"`) triggers refactoring
  - New business variants implemented via new classes, not modified existing code
  - Abstract factories required for runtime strategy selection

### 3. Liskov Substitution Principle (LSP)
- **Subclasses must honor base contracts**:
  - No strengthened preconditions (child class can't require stricter inputs)
  - No weakened postconditions (child can't return `None` where base promises value)
- **Strict enforcement**:
  - `isinstance()`/`type()` checks forbidden in domain layer
  - Contract tests required for all polymorphic hierarchies:
    ```python
    def test_subclass_honors_base_contract():
        for subclass in [CreditCardProcessor, PixProcessor]:
            processor = subclass()
            assert isinstance(processor, PaymentProcessor)
            # Validate core behavior preservation
    ```

### 4. Interface Segregation Principle (ISP)
- **Role-based interfaces only**:
  ```python
  # BAD: Fat interface forcing clients to depend on unused methods
  class UserService:
      def get_user(self, id): ...
      def create_user(self, data): ...
      def send_marketing_email(self, user): ...
      def generate_report(self): ...
  
  # GOOD: Segregated interfaces for different clients
  class UserQueryService(Protocol):
      def get_user(self, id): ...
  
  class UserCommandService(Protocol):
      def create_user(self, data): ...
  
  class MarketingService(Protocol):
      def send_marketing_email(self, user): ...
  ```
- **Validation rule**:
  - Interfaces with >5 methods require architect approval
  - New clients trigger new interfaces, NOT modification of existing ones

### 5. Dependency Inversion Principle (DIP)
- **High-level modules must not depend on low-level modules**:
  ```python
  # BAD: High-level module depends on low-level implementation
  class OrderService:
      def __init__(self):
          self.payment_processor = StripePaymentProcessor()  # ❌ Concrete dependency
  
  # GOOD: Both depend on abstraction
  class OrderService:
      def __init__(self, payment_processor: PaymentProcessor):  # ✅ Abstraction
          self.payment_processor = payment_processor
  ```
- **Implementation rules**:
  - Interfaces belong to the client module, not the implementation module
  - Dependencies injected at composition root (bootstrap)
  - No direct instantiation of infrastructure objects in domain/application layers

### 6. Testing Requirements
- **Contract tests**: Validate all implementations honor base behavior (LSP)
- **ISP validation**: Interfaces must have >80% method usage by clients
- **OCP validation**: Adding new functionality must not require modifying existing tests

### 7. Anti-Patterns Explicitly Blocked
- `instanceof`/`type()` checks in domain logic
- Classes with >3 reasons to change
- Interfaces with unused methods for specific clients
- Direct instantiation of dependencies (`new ConcreteClass()` outside bootstrap)