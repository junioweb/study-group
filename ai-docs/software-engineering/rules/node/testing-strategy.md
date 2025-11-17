## Purpose
Establish comprehensive testing strategy for Node.js/TypeScript projects covering unit, integration and end-to-end tests with emphasis on TDD practice and refactoring safety net.

## Guidelines
Test-Driven Development cycle is mandatory:
1. **Red**: Write failing acceptance test against primary port
2. **Green**: Implement minimal code to pass test (no refactoring yet)
3. **Refactor**: Improve structure while keeping tests green

```typescript
// Example TDD cycle:

// 1. RED: Write failing test
test('create order with invalid email fails', async () => {
  const port = new InMemoryOrderCommandPort();
  const request = { email: 'invalid', items: [...] };
  
  await expect(port.createOrder(request))
    .rejects
    .toThrow(InvalidEmailError);
});

// 2. GREEN: Minimal implementation to pass
class OrderCommandPort {
  async createOrder(request: CreateOrderRequest): Promise<OrderId> {
    if (!request.email.includes('@')) {
      throw new InvalidEmailError();
    }
    return new OrderId('temp-id');
  }
}

// 3. REFACTOR: Improve structure with tests still green
class EmailValidator {
  static isValid(email: string): boolean {
    return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
  }
}

class OrderCommandPort {
  async createOrder(request: CreateOrderRequest): Promise<OrderId> {
    if (!EmailValidator.isValid(request.email)) {
      throw new InvalidEmailError();
    }
    // ... proper implementation
  }
}
```

## Test distribution requirements:
- **Unit tests (≥70% of suite)**: Target Functional Core only
  - Pure functions → no mocks, no setup
  - Must pass in <100ms each
  - Name pattern: `functionName_scenario_expectation.test.ts`
  - Example: `calculateTotal_emptyCart_returnsZero.test.ts`

- **Integration tests (≤25%)**: Test Core + Adapter combinations
  - Use real/fake adapters — no mocks of domain logic
  - Test boundaries between components
  - May require test containers or in-memory databases
  - Example: `postgres-order-repository.integration.test.ts`

- **End-to-end tests (≤5%)**: Full workflow validation
  - Test critical paths only (happy path + 1-2 error cases)
  - Execute against production-like environment
  - May use testcontainers for external dependencies
  - Example: `create-order-e2e.test.ts`

## Acceptance tests must call primary ports directly:
```typescript
// ✅ GOOD: Test against port interface, not HTTP endpoints
test('create order with invalid email fails', async () => {
  const port = new InMemoryOrderCommandPort();
  const request = { email: 'invalid', items: [...] };
  
  await expect(port.createOrder(request))
    .rejects
    .toThrow(InvalidEmailError);
});

// ❌ BAD: Test starting HTTP server for unit test
test('create order endpoint returns 400 for invalid email', async () => {
  const response = await request(app)
    .post('/orders')
    .send({ email: 'invalid', items: [...] });
  
  expect(response.status).toBe(400);
});
```

## Boy Scout Refactoring Checklist (at least one item per PR):
- [ ] Reduced function length (e.g., 25 → 18 lines)
- [ ] Improved naming (e.g., `data` → `customerProfile`)
- [ ] Extracted pure function from shell
- [ ] Replaced `if/else` chain with strategy/polymorphism
- [ ] Encapsulated data clump into value object
- [ ] Added missing test for edge case
- [ ] Lowered cyclomatic complexity by ≥1

## No refactoring allowed if:
- Critical path has <80% branch coverage
- No characterization test for legacy behavior
- Build pipeline is failing
- No reviewer approved the architectural changes

## Testing tools and setup:
```typescript
// tests/setup.ts - Vitest setup file
import { vi } from 'vitest';
import { container } from '@/infrastructure/di-container';

// Global mocks for infrastructure
vi.mock('@/infrastructure/database/DatabaseConnection', () => ({
  getDatabaseConnection: vi.fn().mockReturnValue({
    query: vi.fn(),
    transaction: vi.fn()
  })
}));

// Custom matchers for domain objects
expect.extend({
  toBeMoney(received: Money, expected: { amount: number, currency: string }) {
    return {
      pass: received.amount === expected.amount && received.currency === expected.currency,
      message: () => `expected ${received} to be money ${expected.amount} ${expected.currency}`
    };
  }
});
```

## Anti-Patterns
❌ Mocking core logic (e.g., `vi.mock('@/domain/services/calculateTotal')`)
❌ Tests with `setTimeout()` or global state dependencies
❌ "# TODO: write test" in merged code
❌ Writing implementation before tests "to explore the problem"
❌ Tests that check implementation details instead of behavior
❌ Acceptance tests that start/stop HTTP servers unnecessarily
❌ "Happy path only" test coverage with no edge cases
❌ Tests that depend on external services without proper mocking/faking
❌ Slow tests (>500ms) in the unit test suite
❌ Using production databases in test environments