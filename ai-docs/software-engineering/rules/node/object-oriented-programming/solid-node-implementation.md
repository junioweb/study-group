## Purpose
Apply SOLID principles effectively in Node.js/TypeScript OOP codebases with concrete implementation patterns and JavaScript-specific patterns.

## Guidelines
Single Responsibility Principle implementation:
- Functions ≤15 lines, classes ≤5 public methods
- Name classes with single concern (avoid "and", "or", "manager")
- Extract cross-cutting concerns via decorators or composition

```typescript
// ✅ GOOD: SRP via composition
export class OrderProcessor {
  constructor(
    private readonly calculator: OrderCalculator,
    private readonly repository: OrderRepository
  ) {}
  
  async process(order: Order): Promise<void> {
    const total = this.calculator.calculateTotal(order);
    order.setTotal(total);
    await this.repository.save(order);
  }
}

// ✅ GOOD: Decorator for cross-cutting concern
export function logExecution(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  
  descriptor.value = function (...args: any[]) {
    console.log(`Executing ${propertyKey} with args:`, args);
    const result = originalMethod.apply(this, args);
    console.log(`Result of ${propertyKey}:`, result);
    return result;
  };
  
  return descriptor;
}

class OrderService {
  @logExecution
  calculateTotal(order: Order): number {
    // business logic
  }
}
```

Open/Closed Principle in Node.js:
- Prefer composition over inheritance
- Use interfaces/abstract classes for abstraction boundaries
- Strategy pattern for variant behaviors

```typescript
// Interface for tax strategy
export interface TaxStrategy {
  calculate(amount: number): number;
}

// Concrete strategies
export class IcmsStrategy implements TaxStrategy {
  calculate(amount: number): number {
    return amount * 0.17;
  }
}

export class IpiStrategy implements TaxStrategy {
  calculate(amount: number): number {
    return amount * 0.10;
  }
}

// Context that uses strategies
export class OrderService {
  constructor(private readonly taxStrategy: TaxStrategy) {}
  
  calculateTax(amount: number): number {
    return this.taxStrategy.calculate(amount);
  }
}
```

Liskov Substitution Principle safeguards:
- Never strengthen preconditions in subclasses
- Never weaken postconditions in subclasses
- Avoid type checking in domain code

```typescript
// ❌ BAD: Violates LSP with type checking
function processPayment(payment: PaymentMethod) {
  if (payment instanceof CreditCard) {
    payment.processCard();
  } else if (payment instanceof Pix) {
    payment.processPix();
  }
}

// ✅ GOOD: Polymorphic interface
export interface PaymentMethod {
  process(): Promise<boolean>;
}

export class CreditCard implements PaymentMethod {
  process(): Promise<boolean> {
    // Card-specific logic
  }
}

export class Pix implements PaymentMethod {
  process(): Promise<boolean> {
    // PIX-specific logic
  }
}

// Usage without type checking
async function processPayment(payment: PaymentMethod): Promise<boolean> {
  return payment.process();
}
```

Interface Segregation in Node.js:
- Create role-based interfaces instead of fat interfaces
- Use multiple interfaces for different clients

```typescript
// Role-based interfaces
export interface OrderReader {
  getOrder(orderId: string): Promise<Order>;
  listOrders(customerId: string): Promise<Order[]>;
}

export interface OrderWriter {
  createOrder(order: Order): Promise<OrderId>;
  updateOrder(orderId: string, order: Partial<Order>): Promise<void>;
  deleteOrder(orderId: string): Promise<void>;
}

// Implementation can satisfy multiple interfaces
export class OrderService implements OrderReader, OrderWriter {
  async getOrder(orderId: string): Promise<Order> { /* ... */ }
  async listOrders(customerId: string): Promise<Order[]> { /* ... */ }
  async createOrder(order: Order): Promise<OrderId> { /* ... */ }
  async updateOrder(orderId: string, order: Partial<Order>): Promise<void> { /* ... */ }
  async deleteOrder(orderId: string): Promise<void> { /* ... */ }
}
```

Dependency Inversion implementation:
- Core owns interfaces, infrastructure implements them
- Explicit dependency injection via constructor
- Composition root in bootstrap module

```typescript
// Domain interface (core owns the abstraction)
export interface PaymentGateway {
  charge(amount: number, customerId: string): Promise<PaymentResult>;
}

// Infrastructure implementation
export class StripePaymentGateway implements PaymentGateway {
  constructor(private readonly stripeApiKey: string) {}
  
  async charge(amount: number, customerId: string): Promise<PaymentResult> {
    // Stripe implementation
  }
}

// High-level module depends on abstraction
export class OrderProcessor {
  constructor(private readonly paymentGateway: PaymentGateway) {}
  
  async processPayment(order: Order): Promise<PaymentResult> {
    return this.paymentGateway.charge(order.total, order.customerId);
  }
}
```

## Testing strategy for SOLID code:
- Test behavior, not implementation
- Contract tests for all polymorphic implementations
- Test components in isolation with fake dependencies
- No more than 2 test doubles per test

```typescript
// Contract test for PaymentGateway implementations
abstract class PaymentGatewayContract {
  abstract createGateway(): PaymentGateway;
  
  async testChargeSuccess() {
    const gateway = this.createGateway();
    const result = await gateway.charge(100.0, 'customer-1');
    expect(result.success).toBe(true);
    expect(result.amount).toBe(100.0);
  }
  
  async testChargeFailure() {
    const gateway = this.createGateway();
    await expect(gateway.charge(-10.0, 'customer-1'))
      .rejects
      .toThrow('Invalid amount');
  }
}

// Concrete test for Stripe implementation
class StripePaymentGatewayTest extends PaymentGatewayContract {
  createGateway(): PaymentGateway {
    return new StripePaymentGateway('test_api_key');
  }
}
```

## Anti-Patterns
❌ Classes with >3 reasons to change
❌ Type checking (`instanceof`) outside composition root
❌ Base classes with empty methods ("I don't need this method" pattern)
❌ Concrete dependencies instantiated inside domain classes
❌ Interfaces with methods that some clients don't use
❌ Type checking for flow control in business logic
❌ "Jack of all trades" utility classes with mixed concerns
❌ Deep inheritance hierarchies instead of composition
❌ Violating interface segregation by creating "fat" interfaces
❌ Dependency on concrete classes instead of abstractions