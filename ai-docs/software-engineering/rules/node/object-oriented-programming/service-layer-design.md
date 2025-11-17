## Purpose
Design application service layer with proper separation of concerns, dependency injection, and transaction management in Node.js applications.

## Guidelines
Application services must:
- Be stateless and focus on use cases
- Orchestrate domain objects without containing business logic
- Handle transactions and unit of work boundaries
- Translate between DTOs and domain objects

Service layer structure:
```typescript
// src/application/services/order.service.ts
export class OrderService {
  constructor(
    private readonly orderRepository: OrderRepositoryPort,
    private readonly paymentService: PaymentServicePort,
    private readonly notificationService: NotificationServicePort
  ) {}

  async createOrder(request: CreateOrderRequest): Promise<OrderId> {
    // Convert DTO to domain objects
    const items = request.items.map(item => 
      new OrderItem(item.productId, item.quantity, item.price)
    );
    
    // Create domain entity
    const order = Order.create({
      customerId: request.customerId,
      items: items
    });
    
    // Execute domain logic
    if (!order.isValid()) {
      throw new InvalidOrderError('Order validation failed');
    }
    
    // Handle side effects and infrastructure concerns
    await this.orderRepository.save(order);
    await this.notificationService.notifyOrderCreated(order);
    
    return order.id;
  }
}
```

## Transaction management:
- Unit of Work pattern for transaction boundaries
- Explicit transaction demarcation in service layer
- All domain modifications within a single transaction

```typescript
export class OrderService {
  constructor(
    private readonly unitOfWork: UnitOfWork,
    private readonly orderRepository: OrderRepositoryPort
  ) {}

  async updateOrderStatus(orderId: string, newStatus: OrderStatus): Promise<void> {
    await this.unitOfWork.executeInTransaction(async () => {
      const order = await this.orderRepository.findById(orderId);
      if (!order) throw new OrderNotFoundError(orderId);
      
      order.updateStatus(newStatus); // Domain logic
      await this.orderRepository.save(order);
      
      // Events will be collected and processed after commit
    });
  }
}
```

## DTO patterns:
- Request DTOs for incoming data validation
- Response DTOs for API contracts
- Domain object mapping in service layer only

```typescript
// Request DTO (validated at boundary)
export class CreateOrderRequest {
  constructor(
    readonly customerId: string,
    readonly items: OrderItemRequest[]
  ) {}
}

// Response DTO (API contract)
export class OrderResponse {
  constructor(
    readonly id: string,
    readonly status: OrderStatus,
    readonly total: number,
    readonly createdAt: Date
  ) {}
}

// Mapping in service layer
private toOrderResponse(order: Order): OrderResponse {
  return new OrderResponse(
    order.id.value,
    order.status,
    order.calculateTotal(),
    order.createdAt
  );
}
```

## Error handling strategy:
- Domain exceptions for business rule violations
- Application exceptions for infrastructure failures
- Error translation at interface layer

```typescript
// Domain exception
export class InsufficientStockError extends DomainError {
  constructor(sku: string, available: number, requested: number) {
    super(`Insufficient stock for SKU ${sku}. Available: ${available}, Requested: ${requested}`);
  }
}

// Service layer throws domain exceptions
async allocateInventory(order: Order): Promise<void> {
  for (const item of order.items) {
    const stock = await this.inventoryRepository.getStock(item.sku);
    if (stock < item.quantity) {
      throw new InsufficientStockError(item.sku, stock, item.quantity);
    }
  }
}

// Interface layer translates to HTTP errors
app.post('/orders', async (req, res) => {
  try {
    const result = await orderService.createOrder(req.body);
    res.status(201).json(result);
  } catch (error) {
    if (error instanceof InsufficientStockError) {
      res.status(400).json({ error: error.message });
    } else if (error instanceof DomainError) {
      res.status(400).json({ error: `Invalid order: ${error.message}` });
    } else {
      res.status(500).json({ error: 'Internal server error' });
    }
  }
});
```

## Testing strategy:
- Unit tests with mocked dependencies
- Test service behavior, not implementation details
- Verify transaction boundaries and error handling
- Test boundary conditions and edge cases

```typescript
test('createOrder throws error when customer not found', async () => {
  const customerRepository = mock<CustomerRepositoryPort>();
  when(customerRepository.findById('invalid-id')).thenReturn(null);
  
  const orderService = new OrderService(
    mock<OrderRepositoryPort>(),
    customerRepository,
    mock<NotificationServicePort>()
  );
  
  await expect(orderService.createOrder({
    customerId: 'invalid-id',
    items: [validItem]
  })).rejects.toThrow(CustomerNotFoundError);
});
```

## Anti-Patterns
❌ Business logic in service layer instead of domain objects
❌ Services with multiple responsibilities (violating SRP)
❌ Transaction management scattered across multiple layers
❌ Direct database calls in service methods without repository abstraction
❌ Services that return domain entities directly to API layer
❌ Mixing synchronous and asynchronous operations in the same service method
❌ Services that handle HTTP requests/responses directly
❌ Hard-coded dependencies instead of dependency injection