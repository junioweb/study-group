**Purpose**  
Structure code to support Hexagonal Architecture: application core depends *only* on abstract ports; adapters implement them.

**Scope**  
Project layout, dependency direction, module boundaries.

**Directory Structure**  
```
src/
├── core/                 # Pure domain: functions, value objects, exceptions
│   ├── orders/
│   │   ├── models.py     # Order, Money, TaxTable (immutable, no methods)
│   │   ├── policies.py   # calculate_total(), validate_order()
│   │   └── exceptions.py # OrderValidationError (domain-specific)
│   └── ports/            # Interfaces only — no implementations
│       ├── order_commands.py  # class OrderCommandPort(Protocol):
│       └── payment_gateway.py # class PaymentGatewayPort(Protocol):
│
├── shell/                # Imperative Shell: I/O, side effects
│   ├── adapters/
│   │   ├── db/           # PostgreSQLOrderRepository implements OrderCommandPort
│   │   ├── api/          # FastAPIOrderHandler uses OrderCommandPort
│   │   └── payment/      # StripePaymentAdapter implements PaymentGatewayPort
│   └── app.py            # DI wiring: core + adapters → runnable app
│
tests/
├── unit/      # Core only — no mocks (pure functions)
├── integration/ # Core + real/fake adapters (e.g., SQLite, Stripe sandbox)
└── e2e/       # Full stack (HTTP-in, DB-out)
```

**Rules**
- Core **must not import** `shell/`, `fastapi`, `sqlalchemy`, `requests`  
- Ports are **Protocols** (PEP 544) or abstract base classes  
- Adapters **never expose internals** — only via port interfaces  
- DI is explicit: `app = build_app(order_repo=PostgresRepo(...))`

**Anti-Patterns**  
- `core/models.py` importing `sqlalchemy.orm`  
- Adapter calling core logic *and* another adapter directly (bypassing port)  
- Global singletons for `db` or `http_client` in core