## Purpose
Implement domain entities, value objects, and aggregates following TypeScript-specific DDD practices with immutability and behavior-rich design.

## Guidelines
Entities must:
- Have explicit identity (UUID preferred)
- Encapsulate state-changing behavior
- Implement proper equality based on identity
- Use private attributes with public methods for state changes

## Value objects must:
- Be immutable with `readonly` properties
- Validate invariants in constructor
- Implement structural equality
- Provide transformation methods returning new instances

```typescript
// GOOD: Immutable value object with validation
export class Money {
  constructor(
    readonly amount: number,
    readonly currency: string
  ) {
    // Validate invariants in constructor
    if (amount < 0) throw new Error('Money cannot be negative');
    if (currency.length !== 3) throw new Error('Currency must be ISO 3-letter code');
    
    // Ensure precision
    this.amount = Number(amount.toFixed(2));
  }

  // Transformation methods return new instances
  add(other: Money): Money {
    if (this.currency !== other.currency) {
      throw new Error('Cannot add different currencies');
    }
    return new Money(this.amount + other.amount, this.currency);
  }

  // Structural equality
  equals(other: Money): boolean {
    return this.amount === other.amount && 
           this.currency === other.currency;
  }
}
```

## Aggregate boundaries must:
- Have exactly one aggregate root
- Enforce all invariants within the boundary
- Allow external references only to the root
- Return only DTOs or primitive types from root methods

```typescript
export class Order extends AggregateRoot {
  private _status: OrderStatus = OrderStatus.PENDING;
  private _items: OrderItem[] = [];
  private _events: DomainEvent[] = [];

  constructor(
    readonly id: OrderId = new OrderId(uuidv4()),
    readonly customerId: string,
    readonly createdAt: Date = new Date()
  ) {
    super(id);
  }

  addItem(item: OrderItem): void {
    if (this._status !== OrderStatus.PENDING) {
      throw new InvalidOrderStateError(this.id, this._status, 'addItem');
    }
    this._items.push(item);
    this._recordEvent(new OrderItemAdded(this.id, item));
  }

  collectEvents(): DomainEvent[] {
    const events = [...this._events];
    this._events = [];
    return events;
  }

  private _recordEvent(event: DomainEvent): void {
    this._events.push(event);
  }
}
```

## Collection handling:
- Never expose mutable collections directly
- Return defensive copies or immutable collections (Object.freeze, [...array])
- Use private collections with controlled access methods

```typescript
// GOOD: Defensive collection exposure
get items(): readonly OrderItem[] {
  return Object.freeze([...this._items]);
}

// BAD: Exposing internal mutable collection
get items(): OrderItem[] {
  return this._items; // Allows external mutation
}
```

## Testing requirements:
- Test domain behavior, not implementation details
- 100% testable without infrastructure
- Express tests in business language using domain terminology

```typescript
test('order cannot be modified after shipment', () => {
  const order = OrderFactory.createWithItems(3);
  order.ship();
  
  expect(() => order.addItem(new OrderItem('product-1', 1, 10.0)))
    .toThrow(InvalidOrderStateError);
});
```

## Anti-Patterns
❌ Public setters on entities (`order.status = 'shipped'`)
❌ Mutable value objects without validation
❌ Exposing internal collections (`order.items.push(newItem)`)
❌ Aggregate roots with public attributes
❌ Domain objects importing infrastructure packages (`import { Entity } from 'typeorm'`)
❌ Using getters/setters for complex business logic instead of behavior methods
❌ Breaking encapsulation by exposing internal state for external manipulation