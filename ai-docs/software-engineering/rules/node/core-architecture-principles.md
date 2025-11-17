## Purpose
Structure Node.js/TypeScript applications using Hexagonal Architecture with strict separation between core domain logic and infrastructure concerns.

## Guidelines
Core must be completely isolated from infrastructure:
```typescript
// GOOD: Domain model completely isolated
export class OrderLine {
  constructor(
    readonly orderId: string,
    readonly sku: string,
    readonly qty: number
  ) {
    if (qty <= 0) throw new Error('Quantity must be positive');
  }
}

// BAD: Domain importing infrastructure packages
import { Column, Entity } from 'typeorm'; // ❌ Forbidden in core/
```

Primary (driving) ports — app is driven by external actors:
- Naming convention: `*CommandPort`, `*QueryPort`
- Testing strategy: Direct calls to ports (no HTTP servers)
- Examples: `OrderCommandPort`, `UserQueryPort`
```typescript
// src/core/ports/OrderCommandPort.ts
export interface OrderCommandPort {
  createOrder(order: CreateOrderDTO): Promise<OrderId>;
  cancelOrder(orderId: string): Promise<void>;
}
```

Secondary (driven) ports — app drives external systems:
- Naming convention: `*RepositoryPort`, `*GatewayPort`, `*PublisherPort`
- Testing strategy: In-memory implementations for tests
- Examples: `PaymentGatewayPort`, `OrderRepositoryPort`
```typescript
// src/core/ports/OrderRepositoryPort.ts
export interface OrderRepositoryPort {
  save(order: Order): Promise<void>;
  findById(id: string): Promise<Order | null>;
}
```

## Directory structure enforcing boundaries:
```
src/
├── core/                 # Pure domain logic
│   ├── models/           # Entities, value objects
│   ├── exceptions/       # Domain exceptions
│   ├── use-cases/        # Business logic functions
│   └── ports/            # Interfaces only
│       ├── order-command.port.ts
│       └── order-repository.port.ts
│
├── adapters/            # Infrastructure implementations
│   ├── database/        # Database adapters
│   ├── http/            # API controllers, Express routes
│   └── external/        # Third-party service integrations
│
└── app.ts               # Composition root for dependency injection
```

## Implementation example:
```typescript
// src/adapters/database/PostgresOrderRepository.ts
import { OrderRepositoryPort } from '@/core/ports/OrderRepositoryPort';
import { Order } from '@/core/models/Order';
import { Pool } from 'pg';

export class PostgresOrderRepository implements OrderRepositoryPort {
  constructor(private readonly pool: Pool) {}

  async save(order: Order): Promise<void> {
    await this.pool.query(
      'INSERT INTO orders (id, customer_id, status) VALUES ($1, $2, $3)',
      [order.id, order.customerId, order.status]
    );
  }
}
```

## Anti-Patterns
❌ Domain/core objects importing infrastructure packages (Express, TypeORM, etc.)
❌ Naming ports `IService`, `IHandler` — too vague and non-specific
❌ Using same port for reading and writing (violates CQS principle)
❌ Adapter calling another adapter directly (bypassing core ports)
❌ Hidden dependencies via service locator pattern or global state
❌ Exposing infrastructure types (like `Request`, `Response`) in core interfaces