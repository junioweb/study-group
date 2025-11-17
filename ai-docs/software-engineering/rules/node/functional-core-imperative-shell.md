## Purpose
Enforce strict separation between pure functional core logic and imperative shell that handles side effects in Node.js/TypeScript applications.

## Guidelines
Core functions must be pure:
- No I/O operations (database calls, HTTP requests, file system)
- No mutation of input parameters
- No reliance on external state (current time, random numbers, environment variables)
- No side effects (logging, sending messages, modifying global state)
- Deterministic output for given inputs

```typescript
// ✅ GOOD: Pure core function
export function calculateOrderTotal(items: OrderItem[], taxRate: number): number {
  const subtotal = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
  return subtotal * (1 + taxRate);
}

// ❌ BAD: Impure core function with side effects
export function calculateOrderTotal(items: OrderItem[]): number {
  const subtotal = items.reduce((sum, item) => sum + item.price * item.quantity, 0);
  const taxRate = getTaxRateFromDatabase(); // I/O operation
  console.log(`Calculated total: ${subtotal}`); // Side effect
  return subtotal * (1 + taxRate);
}
```

## Core functions constraints:
- Small size: ≤15 lines of code
- Limited parameters: ≤3 parameters
- Clear naming: verb + domain object (calculateTotal, validateEmail)
- Command-Query Separation: 
  - Queries (getOrderTotal) return values, no side effects
  - Commands (updateOrderStatus) perform actions, return void or status

## Shell functions handle side effects:
- Thin wrappers (≤25 lines) around core logic
- Responsible for: I/O, error handling, logging, retries, timeouts
- Contain try/catch blocks only — extracted into helpers like `executeWithRetry()`
- Translate between external formats and core domain types

```typescript
// ✅ GOOD: Shell function handling side effects
export async function createOrderHandler(request: CreateOrderRequest): Promise<OrderResponse> {
  try {
    // Convert from external format to domain types
    const orderItems = request.items.map(item => new OrderItem(item.productId, item.quantity, item.price));
    
    // Call pure core logic
    const order = Order.create(orderItems);
    const total = calculateOrderTotal(order.items, globalTaxRate);
    
    // Handle I/O operations
    const persistedOrder = await orderRepository.save(order.withTotal(total));
    
    // Convert back to external format
    return OrderResponse.fromOrder(persistedOrder);
  } catch (error) {
    // Translate domain exceptions to appropriate responses
    if (error instanceof DomainError) {
      throw new HttpError(400, `Invalid order: ${error.message}`);
    }
    throw new HttpError(500, 'Internal server error');
  }
}
```

## Exception handling strategy:
- Domain exceptions defined in core layer (e.g., `OutOfStockError`, `InvalidEmailError`)
- Core functions let exceptions propagate (no try/catch in core)
- Shell functions translate domain exceptions to appropriate formats (HTTP errors, CLI messages)
- Infrastructure exceptions handled at adapter level with proper logging

```typescript
// src/domain/exceptions/DomainError.ts
export class DomainError extends Error {
  constructor(message: string) {
    super(message);
    this.name = 'DomainError';
  }
}

export class InvalidOrderStateError extends DomainError {
  constructor(orderId: string, currentState: string, attemptedAction: string) {
    super(`Order ${orderId} in state ${currentState} cannot perform ${attemptedAction}`);
    this.name = 'InvalidOrderStateError';
  }
}
```

## Composition root setup:
- Single entry point for dependency composition
- Located at application bootstrap
- Injects real implementations for production, fakes for testing
- Never uses service locator or global state

```typescript
// src/infrastructure/composition-root.ts
import { CreateOrderUseCase } from '@/application/use-cases/CreateOrderUseCase';
import { PostgresOrderRepository } from '@/infrastructure/database/PostgresOrderRepository';
import { ExpressOrderController } from '@/interfaces/http/controllers/OrderController';
import { Container } from 'inversify';

export function bootstrap(container: Container): void {
  // Register infrastructure dependencies
  container.bind('DatabaseConnection').toConstantValue(createDatabaseConnection());
  
  // Register adapters for core ports
  container.bind<OrderRepositoryPort>('OrderRepositoryPort')
    .to(PostgresOrderRepository)
    .inSingletonScope();
  
  // Register application layer
  container.bind<CreateOrderUseCase>(CreateOrderUseCase).toSelf();
  
  // Register interface layer
  container.bind<ExpressOrderController>(ExpressOrderController).toSelf();
}
```

## Anti-Patterns
❌ Core function calling `fetch()`, `fs.readFile()`, or database operations
❌ Shell function containing business rules (e.g., "if user is premium, apply 10% off")
❌ Command function returning computed values (violates CQS principle)
❌ Direct mutation of input parameters in core functions
❌ Using global state or singletons in core logic
❌ Implementing retries or circuit breakers in core functions
❌ Mixing core logic and infrastructure concerns in the same function
❌ Using environment variables directly in core functions