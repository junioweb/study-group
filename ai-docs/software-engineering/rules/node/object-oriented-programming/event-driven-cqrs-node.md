## Purpose
Implement event-driven architecture and CQRS patterns for scalable, maintainable Node.js systems with eventual consistency.

## Guidelines
Command-Query Separation:
Commands must:
- Use imperative naming (`CreateOrder`, `CancelOrder`)
- Modify only one aggregate
- Return no domain data (only success/failure status or IDs)
- Be processed synchronously

Queries must:
- Use noun-based naming (`GetOrder`, `ListOrders`)
- Be side-effect free
- Return DTOs or primitive data structures only
- Be optimized for read performance

API endpoints must follow separation:
```typescript
// POST endpoint (command)
app.post('/orders', async (req, res) => {
  const command = { type: 'CREATE_ORDER', ...req.body };
  const orderId = await messageBus.handle(command);
  return res.status(201).json({ orderId }); // No domain data
});

// GET endpoint (query)
app.get('/orders/:id', async (req, res) => {
  const query = { type: 'GET_ORDER', orderId: req.params.id };
  const orderDTO = await queryBus.handle(query);
  return res.json(orderDTO); // Read model only
});
```

## Domain events must be immutable:
```typescript
// GOOD: Immutable domain event
export class OrderCreated {
  readonly timestamp: Date = new Date();
  
  constructor(
    readonly orderId: string,
    readonly customerId: string,
    readonly items: readonly OrderItem[],
    readonly createdAt: Date = new Date()
  ) {
    // Validate invariants
    if (items.length === 0) {
      throw new Error('Order must have at least one item');
    }
  }
}
```

## Message bus implementation:
```typescript
export class MessageBus {
  constructor(
    private readonly commandHandlers: Map<string, CommandHandler>,
    private readonly eventHandlers: Map<string, EventHandler[]>,
    private readonly unitOfWork: UnitOfWork
  ) {}

  async handleCommand<T extends Command>(command: T): Promise<any> {
    const handler = this.commandHandlers.get(command.type);
    if (!handler) throw new Error(`No handler for command: ${command.type}`);
    
    return this.unitOfWork.executeInTransaction(async () => {
      const result = await handler.execute(command);
      const events = this.unitOfWork.collectNewEvents();
      await this.unitOfWork.commit();
      
      // Process events after transaction commit for eventual consistency
      await this.processEvents(events);
      return result;
    });
  }

  private async processEvents(events: DomainEvent[]): Promise<void> {
    for (const event of events) {
      const handlers = this.eventHandlers.get(event.type) || [];
      for (const handler of handlers) {
        try {
          await handler.handle(event);
        } catch (error) {
          console.error(`Event handler failed for ${event.type}:`, error);
          // Continue processing other handlers
        }
      }
    }
  }
}
```

## Read model implementation:
- Read models must be denormalized for query performance
- Update via event handlers after transaction commit
- Multiple read models allowed for different query patterns

```typescript
export class OrderReadModelUpdater {
  constructor(private readonly readModelRepository: OrderReadModelRepository) {}

  async handleOrderCreated(event: OrderCreated): Promise<void> {
    const orderView = {
      orderId: event.orderId,
      customerId: event.customerId,
      totalAmount: this.calculateTotal(event.items),
      status: 'CREATED',
      createdAt: event.createdAt
    };
    
    await this.readModelRepository.save(orderView);
  }
}
```

## Transactional boundaries:
```typescript
// Events processed after commit for eventual consistency
await this.unitOfWork.executeInTransaction(async () => {
  await this.orderService.createOrder(command);
  const events = this.unitOfWork.collectNewEvents();
  await this.unitOfWork.commit();
  await this.messageBus.processEvents(events); // After commit
});
```

## Testing requirements:
- Command handlers tested with unit tests and fake UoW
- Event handlers tested in isolation with fake dependencies
- End-to-end tests verify eventual consistency
- Idempotency tests for all event handlers

## Anti-Patterns
❌ Returning domain entities from queries
❌ Commands that return data (violates CQS)
❌ Synchronous HTTP calls between services (use events instead)
❌ Event handlers with side effects on write model
❌ Fat events containing full entity state
❌ Processing events before transaction commit
❌ Using synchronous event processing for cross-service communication
❌ Event handlers that throw exceptions without proper error handling