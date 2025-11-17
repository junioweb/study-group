## Purpose
Implement proper dependency injection patterns in Node.js to maintain clean architecture boundaries and testability.

## Guidelines
Domain layer owns all interfaces:
```typescript
// domain/ports/notification.port.ts
export interface Notification {
  orderId: string;
  message: string;
}

export interface NotificationService {
  send(notification: Notification): Promise<boolean>;
}
```

Infrastructure implements domain interfaces:
```typescript
// infrastructure/services/email-notification.service.ts
import { NotificationService, Notification } from '@/domain/ports/notification.port';

export class EmailNotificationService implements NotificationService {
  constructor(
    private readonly smtpServer: string,
    private readonly port: number = 587
  ) {}

  async send(notification: Notification): Promise<boolean> {
    // Implementation details using SMTP library
    console.log(`Sending email to order ${notification.orderId}: ${notification.message}`);
    return true;
  }
}
```

Composition root must be single entrypoint:
```typescript
// infrastructure/composition-root.ts
import { Container } from 'inversify';
import { OrderService } from '@/application/services/order.service';
import { PostgresOrderRepository } from '@/infrastructure/database/postgres-order.repository';
import { EmailNotificationService } from '@/infrastructure/services/email-notification.service';
import { RedisEventPublisher } from '@/infrastructure/messaging/redis-event.publisher';
import { TYPES } from '@/application/types';

export function bootstrap(container: Container): void {
  // Create infrastructure dependencies
  const databaseUrl = process.env.DATABASE_URL || 'postgres://localhost:5432/orders';
  const smtpServer = process.env.SMTP_SERVER || 'smtp.example.com';
  const redisUrl = process.env.REDIS_URL || 'redis://localhost:6379';

  // Register infrastructure implementations
  container.bind<string>('DatabaseUrl').toConstantValue(databaseUrl);
  container.bind<string>('SmtpServer').toConstantValue(smtpServer);
  container.bind<string>('RedisUrl').toConstantValue(redisUrl);
  
  // Register adapters for domain ports
  container.bind<OrderRepositoryPort>(TYPES.OrderRepository)
    .to(PostgresOrderRepository)
    .inSingletonScope();
    
  container.bind<NotificationService>(TYPES.NotificationService)
    .to(EmailNotificationService)
    .inSingletonScope();
    
  container.bind<EventPublisher>(TYPES.EventPublisher)
    .to(RedisEventPublisher)
    .inSingletonScope();
  
  // Register application services
  container.bind<OrderService>(TYPES.OrderService).to(OrderService);
}
```

Adapter pattern implementation:
```typescript
// infrastructure/messaging/redis-event.publisher.ts
import { EventPublisher } from '@/domain/ports/event.publisher.port';
import { DomainEvent } from '@/domain/events/domain-event';
import Redis from 'ioredis';

export class RedisEventPublisher implements EventPublisher {
  private readonly redis: Redis;
  
  constructor(redisUrl: string) {
    this.redis = new Redis(redisUrl);
  }
  
  async publish(channel: string, event: DomainEvent): Promise<void> {
    const eventPayload = {
      type: event.type,
       event,
      timestamp: new Date().toISOString()
    };
    
    await this.redis.publish(channel, JSON.stringify(eventPayload));
  }
}
```

Fake adapters required for testing:
```typescript
// tests/fakes/fake-notification.service.ts
import { NotificationService, Notification } from '@/domain/ports/notification.port';

export class FakeNotificationService implements NotificationService {
  sent: Notification[] = [];
  
  async send(notification: Notification): Promise<boolean> {
    this.sent.push(notification);
    return true;
  }
  
  getSentCount(): number {
    return this.sent.length;
  }
  
  getLastNotification(): Notification | undefined {
    return this.sent[this.sent.length - 1];
  }
}
```

## DI techniques in Node.js:
- Use constructor injection for required dependencies
- Use property injection for optional dependencies
- Use factory functions for complex creation logic
- Never use service locator or global state

```typescript
// GOOD: Constructor injection for explicit dependencies
export class AllocateHandler {
  constructor(
    private readonly unitOfWork: UnitOfWork,
    private readonly notificationService: NotificationService
  ) {}
  
  async execute(command: AllocateCommand): Promise<string> {
    await this.unitOfWork.executeInTransaction(async () => {
      const batchRef = await allocate(command.orderId, command.sku, command.qty, this.unitOfWork);
      await this.unitOfWork.commit();
      await this.notificationService.send({
        orderId: command.orderId,
        message: `Allocated to ${batchRef}`
      });
    });
    return batchRef;
  }
}
```

## Testing strategy:
- Unit tests use fake dependencies only
- Integration tests use real dependencies with test containers
- Bootstrap must be tested with different configurations
- Verify dependency wiring in integration tests

```typescript
describe('OrderService', () => {
  let orderService: OrderService;
  let fakeOrderRepository: FakeOrderRepository;
  let fakeNotificationService: FakeNotificationService;
  
  beforeEach(() => {
    fakeOrderRepository = new FakeOrderRepository();
    fakeNotificationService = new FakeNotificationService();
    
    orderService = new OrderService(
      fakeOrderRepository,
      fakeNotificationService
    );
  });
  
  test('sends notification when order is created', async () => {
    const order = await orderService.createOrder(validOrderRequest);
    
    expect(fakeNotificationService.getSentCount()).toBe(1);
    expect(fakeNotificationService.getLastNotification()?.orderId).toBe(order.id);
  });
});
```

## Anti-Patterns
❌ Service locator pattern (hidden dependencies via container.resolve())
❌ Global state (singletons, module-level variables)
❌ Concrete implementations in domain layer
❌ Hidden dependencies via mocking frameworks as primary testing strategy
❌ Direct instantiation of infrastructure objects outside composition root
❌ Using dependency injection frameworks that obscure dependency flow
❌ Circular dependencies between services
❌ Constructor over-injection (more than 5 dependencies)
❌ Property injection for required dependencies