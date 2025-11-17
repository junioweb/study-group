## Purpose
Establish foundational Domain-Driven Design principles for Node.js backend systems with strict domain model purity and separation of concerns.

## Guidelines
Domain layer must be completely isolated from infrastructure:
```typescript
// ✅ GOOD: Pure domain model with no external dependencies
export class OrderLine {
  constructor(
    readonly orderId: string,
    readonly sku: string,
    readonly quantity: number
  ) {
    if (quantity <= 0) {
      throw new Error('Quantity must be positive');
    }
  }
}

// ❌ BAD: Domain importing infrastructure packages
import { Entity, Column } from 'typeorm'; // Forbidden in domain/
@Entity()
export class OrderLine { /* ... */ }
```

Business rules must reside in domain objects, not services:
```typescript
export class Batch {
  private _allocations: Set<string> = new Set();
  
  constructor(
    readonly reference: string,
    readonly sku: string,
    private _purchasedQuantity: number,
    readonly eta?: Date
  ) {}

  allocate(orderLine: OrderLine): boolean {
    if (this.canAllocate(orderLine)) {
      this._allocations.add(orderLine.orderId);
      return true;
    }
    return false;
  }

  private canAllocate(orderLine: OrderLine): boolean {
    return this.sku === orderLine.sku && 
           this.availableQuantity >= orderLine.quantity;
  }

  get availableQuantity(): number {
    return this._purchasedQuantity - this.allocatedQuantity;
  }

  get allocatedQuantity(): number {
    return this._allocations.size;
  }
}
```

Domain exceptions must be specific and contextual:
```typescript
export class DomainError extends Error {
  constructor(message: string) {
    super(message);
    this.name = 'DomainError';
  }
}

export class OutOfStockError extends DomainError {
  constructor(
    readonly sku: string,
    readonly available: number,
    readonly requested: number
  ) {
    super(`Out of stock for SKU ${sku}. Available: ${available}, Requested: ${requested}`);
    this.name = 'OutOfStockError';
  }
}

export class InvalidOrderStateError extends DomainError {
  constructor(
    readonly orderId: string,
    readonly currentState: string,
    readonly attemptedAction: string
  ) {
    super(`Order ${orderId} in state ${currentState} cannot perform ${attemptedAction}`);
    this.name = 'InvalidOrderStateError';
  }
}
```

## Layered architecture enforcement:
| Layer | Responsibilities | Dependencies Allowed |
|-------|------------------|---------------------|
| `domain/` | Entities, value objects, domain services | None (pure TypeScript) |
| `application/` | Use cases, DTOs, ports (interfaces) | `domain/` only |
| `infrastructure/` | Database, APIs, external services | `domain/`, `application/` |
| `interfaces/` | API endpoints, CLI commands, event handlers | `application/`, `infrastructure` |

## Dependency flow must be strictly unidirectional: `interfaces` → `infrastructure` → `application` → `domain`

## Aggregate root pattern:
- Single entry point for all external interactions
- Enforces consistency boundaries
- Controls lifecycle of child entities
- Publishes domain events

```typescript
export abstract class AggregateRoot {
  private _domainEvents: DomainEvent[] = [];
  
  protected addDomainEvent(event: DomainEvent): void {
    this._domainEvents.push(event);
  }
  
  collectEvents(): DomainEvent[] {
    const events = [...this._domainEvents];
    this._domainEvents = [];
    return events;
  }
}

export class Order extends AggregateRoot {
  private _status: OrderStatus = OrderStatus.PENDING;
  
  constructor(
    readonly id: OrderId,
    readonly customerId: string,
    private _items: OrderItem[] = [],
    readonly createdAt: Date = new Date()
  ) {
    super();
  }
  
  addItem(item: OrderItem): void {
    if (this._status !== OrderStatus.PENDING) {
      throw new InvalidOrderStateError(this.id.value, this._status, 'addItem');
    }
    this._items.push(item);
    this.addDomainEvent(new OrderItemAdded(this.id.value, item));
  }
}
```

## Anti-Patterns
❌ Anemic Domain Model (entities with only getters/setters)
❌ Primitive Obsession (using raw strings/numbers instead of value objects)
❌ Domain objects with infrastructure concerns (`.save()` methods on entities)
❌ Leaky abstractions (domain objects exposing internal state)
❌ God objects (classes with multiple responsibilities violating SRP)
❌ Circular dependencies between layers
❌ Business logic in infrastructure or application layers instead of domain
❌ Using generic exceptions (Error) instead of domain-specific exceptions