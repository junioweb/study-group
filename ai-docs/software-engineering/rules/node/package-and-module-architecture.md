## Purpose
Apply package architecture principles for maintainable, scalable Node.js/TypeScript projects with clear dependency boundaries and stability metrics.

## Guidelines
Package dependency rules:
- Dependencies must flow inward: interfaces → application → domain
- No circular dependencies between packages
- Stable packages (less likely to change) must not depend on unstable packages
- Use dependency injection to invert dependencies

## Recommended package structure:
```
src/
├── domain/          # Most stable package
│   ├── models/      # Entities, value objects (immutable)
│   ├── exceptions/  # Domain-specific exceptions
│   └── services/    # Pure domain services (functions preferred)
├── application/     # Use cases, DTOs, ports (interfaces)
│   ├── use-cases/
│   ├── dto/
│   └── ports/
├── infrastructure/  # External integrations (adapters)
│   ├── database/
│   ├── external-services/
│   └── messaging/
└── interfaces/      # API endpoints, CLI commands, event handlers
    ├── http/        # Express controllers, routes
    ├── cli/
    └── events/
```

## Evolution strategy:
- Early stage (prototyping): Prioritize CCP (Common Closure Principle) - group related functionality
- Mature stage (production): Prioritize REP/CRP (Reuse/Release and Common Reuse) - enforce strict boundaries

## TypeScript-specific implementation:
- Use path aliases in tsconfig.json for clean imports:
```json
{
  "compilerOptions": {
    "paths": {
      "@domain/*": ["src/domain/*"],
      "@application/*": ["src/application/*"],
      "@infrastructure/*": ["src/infrastructure/*"],
      "@interfaces/*": ["src/interfaces/*"]
    }
  }
}
```

- Type-only imports for boundary validation:
```typescript
// src/application/use-cases/CreateOrder.ts
import type { OrderRepositoryPort } from '@/application/ports/OrderRepositoryPort';
import type { Order } from '@/domain/models/Order';
```

- Dynamic imports for optional dependencies:
```typescript
// src/infrastructure/external-services/EmailService.ts
export class EmailService {
  async sendEmail(recipient: string, message: string): Promise<boolean> {
    try {
      // Lazy import for optional dependency
      const sgMail = await import('@sendgrid/mail');
      await sgMail.send({ to: recipient, text: message });
      return true;
    } catch (error) {
      console.warn('Failed to send email:', error);
      return false;
    }
  }
}
```

## Dependency validation:
- Implement package dependency tests using dependency-cruiser:
```bash
npm install -D dependency-cruiser
```

```json
// .dependency-cruiser.js
module.exports = {
  forbidden: [
    {
      name: 'no-circular',
      severity: 'error',
      from: {},
      to: { circular: true }
    },
    {
      name: 'no-domain-depends-on-outer',
      comment: 'Domain must not depend on application/infrastructure',
      severity: 'error',
      from: { path: '^src/domain' },
      to: { path: '^(src/application|src/infrastructure|src/interfaces)' }
    }
  ]
};
```

- Track stability metrics over time:
  - Afferent Coupling (Ca): Number of packages depending on this package
  - Efferent Coupling (Ce): Number of packages this package depends on
  - Instability (I): Ce / (Ca + Ce) - should be low for core packages (domain: <0.2, application: <0.5)

## Anti-Patterns
❌ Utility packages: No `utils/`, `helpers/`, or `common/` packages with random utilities
❌ God packages: No package with >20 files without sub-packages
❌ Circular dependencies between packages (detected by dependency-cruiser)
❌ Volatility clusters: Unstable classes grouped with stable classes
❌ Deep nesting that makes imports verbose and complex
❌ Concrete implementations in domain layer
❌ Infrastructure concerns leaking into application or domain layers
❌ Relative imports across package boundaries (`../../domain/models/Order`)