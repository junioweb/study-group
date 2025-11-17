## Purpose
Implement immutable value objects in TypeScript/Node.js with proper validation, structural equality, and functional transformation methods.

## Guidelines
Value objects must be immutable:
Use `readonly` properties for all fields
Prevent mutation via private constructors and factory methods
Use frozen objects or immutable.js for complex structures in legacy code
@dataclass(frozen=True) equivalent:
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
}
```

Structural equality implementation:
```typescript
// Implement proper equality checking
equals(other: Money): boolean {
  return this.amount === other.amount && 
         this.currency === other.currency;
}

// Or use deep equality for complex objects
static equals(a: Address, b: Address): boolean {
  return a.street === b.street && 
         a.city === b.city && 
         a.zipCode === b.zipCode;
}
```

Factory methods for validation:
```typescript
// Prefer factory methods over direct constructors
static fromUSD(amount: number): Money {
  return new Money(amount, 'USD');
}

static fromString(value: string): Money {
  const [amountStr, currency] = value.split(' ');
  const amount = parseFloat(amountStr);
  if (isNaN(amount)) throw new Error(`Invalid money format: ${value}`);
  return new Money(amount, currency);
}
```

## Testing requirements:
Value objects must be 100% testable without infrastructure
Test all validation rules and edge cases
Verify immutability (no mutation after creation)
Test transformation methods return new instances
```typescript
test('Money cannot be negative', () => {
  expect(() => new Money(-1, 'USD')).toThrow('Money cannot be negative');
});

test('Money.add returns new instance with correct amount', () => {
  const money1 = new Money(10, 'USD');
  const money2 = new Money(20, 'USD');
  const result = money1.add(money2);
  
  expect(result).not.toBe(money1);
  expect(result.amount).toBe(30);
});
```

## Anti-Patterns
❌ Public setters or mutable properties on value objects
❌ Validation logic outside of constructor/factory methods
❌ Using plain objects `{ amount: 10, currency: 'USD' }` without validation
❌ Exposing internal mutable state through getters
❌ Reference equality checks (`a === b`) instead of structural equality
❌ Mutating value objects in place instead of returning new instances