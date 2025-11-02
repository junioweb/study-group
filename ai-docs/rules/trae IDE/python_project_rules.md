# Python Backend Development Rules for Trae IDE

**Based on:** Clean Architecture, SOLID Principles, Python Best Practices
**Scope:** Python backend project structure, development workflows, and IDE optimization

## Project Structure Template for Trae IDE

### Standard Python Backend Structure
```
backend-project/
├── src/
│   └── project_name/          # Main package (PEP 420 compliant)
│       ├── __init__.py        # Package initialization
│       ├── __main__.py        # CLI entry point
│       ├── domain/            # Pure domain model (no external deps)
│       │   ├── __init__.py
│       │   ├── entities.py    # Business entities
│       │   ├── value_objects.py
│       │   ├── events.py      # Domain events
│       │   └── exceptions.py  # Domain exceptions
│       ├── application/       # Use cases and services
│       │   ├── __init__.py
│       │   ├── use_cases/     # Business use cases
│       │   ├── services.py    # Application services
│       │   └── unit_of_work.py # Unit of Work pattern
│       ├── infrastructure/    # External implementations
│       │   ├── __init__.py
│       │   ├── persistence/   # Database adapters
│       │   ├── messaging/     # Message brokers
│       │   └── web/           # Web framework adapters
│       └── interfaces/        # Entry points
│           ├── __init__.py
│           ├── api/           # REST/GraphQL endpoints
│           └── cli/           # Command line interface
├── tests/                     # Comprehensive test suite
│   ├── __init__.py
│   ├── unit/                  # Unit tests (70-80%)
│   ├── integration/           # Integration tests (15-20%)
│   └── e2e/                   # End-to-end tests (5-10%)
├── requirements/              # Dependency management
│   ├── base.txt               # Core dependencies
│   ├── dev.txt               # Development tools
│   └── prod.txt              # Production dependencies
├── docker/                   # Containerization
├── .github/                  # GitHub workflows
├── pyproject.toml           # Modern Python config
├── README.md
└── .gitignore
```

## Development Workflow Rules

### 1. Dependency Management
- **Rule:** Use `pyproject.toml` for modern Python packaging (PEP 518, 621)
- **Rule:** Separate dependencies into base, dev, and prod requirements
- **Validation:** Trae IDE should validate dependency consistency

**Example pyproject.toml:**
```toml
[build-system]
requires = ["setuptools>=61.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "backend-project"
version = "0.1.0"
description = "Python backend application"

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "black>=23.0.0",
    "mypy>=1.0.0",
    "flake8>=6.0.0"
]
test = ["pytest", "pytest-cov", "pytest-xdist"]

[tool.black]
line-length = 88
target-version = ['py311']

[tool.mypy]
strict = true
```

### 2. Code Organization and Imports
- **Rule:** Follow standard import order: stdlib → third-party → local
- **Rule:** Use absolute imports within the project
- **Rule:** Keep modules under 500 lines with high cohesion

**Example import structure:**
```python
# Standard library
import os
import sys
from typing import Optional, List

# Third-party dependencies
import requests
from pydantic import BaseModel
from sqlalchemy.orm import Session

# Local application imports
from src.project_name.domain.entities import User
from src.project_name.application.services import UserService
from src.project_name.infrastructure.persistence import UserRepository
```

### 3. Type Annotations and Validation
- **Rule:** Use type annotations for all function signatures
- **Rule:** Enable strict mypy checking (`mypy --strict`)
- **Rule:** Use Pydantic for data validation and serialization

**Example typed function:**
```python
def create_user(
    user_data: UserCreateSchema,
    user_repo: UserRepository,
    email_service: EmailService
) -> User:
    """Create a new user with validation and notification."""
    # Type-safe data processing
    user = User(**user_data.dict())
    user_repo.save(user)
    email_service.send_welcome_email(user.email)
    return user
```

## IDE-Specific Optimization Rules

### 1. Trae IDE Configuration
- **Rule:** Configure `.trae/` directory for project-specific settings
- **Rule:** Use workspace-specific linting and formatting rules
- **Rule:** Enable real-time type checking and error highlighting

**Example .trae/config.json:**
```json
{
  "python": {
    "interpreterPath": ".venv/bin/python",
    "linting": {
      "enabled": true,
      "flake8": true,
      "mypy": true,
      "black": true
    },
    "testing": {
      "pytest": true,
      "coverage": true
    }
  },
  "autoFormatOnSave": true,
  "typeChecking": "strict"
}
```

### 2. Live Development Features
- **Rule:** Enable hot-reload for development servers
- **Rule:** Use integrated terminal for dependency management
- **Rule:** Configure debugging profiles for different environments

**Example launch.json for debugging:**
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Python: FastAPI Dev",
      "type": "python",
      "request": "launch",
      "module": "uvicorn",
      "args": ["src.project_name.interfaces.api:app", "--reload"],
      "env": {"PYTHONPATH": "${workspaceFolder}/src"}
    }
  ]
}
```

## Testing and Quality Rules

### 1. Test Structure
- **Rule:** Follow FIRST principles (Fast, Isolated, Repeatable, Self-Validating, Timely)
- **Rule:** Use Arrange-Act-Assert pattern for test organization
- **Rule:** Maintain 80%+ test coverage for critical paths

**Example test structure:**
```python
def test_user_creation_success():
    # Arrange
    user_data = UserCreateSchema(name="John", email="john@example.com")
    mock_repo = Mock(spec=UserRepository)
    mock_email = Mock(spec=EmailService)
    
    # Act
    user_service = UserService(mock_repo, mock_email)
    result = user_service.create_user(user_data)
    
    # Assert
    assert result.name == "John"
    mock_repo.save.assert_called_once()
    mock_email.send_welcome_email.assert_called_once()
```

### 2. Integration Testing
- **Rule:** Use test containers for real integration testing
- **Rule:** Test database operations with real database instances
- **Rule:** Mock external services but test internal integrations

**Example integration test:**
```python
@pytest.mark.integration
def test_user_repository_integration():
    # Use test database container
    with PostgresTestContainer() as postgres:
        engine = create_engine(postgres.get_connection_url())
        SessionLocal = sessionmaker(bind=engine)
        
        # Test real database operations
        repo = UserRepository(SessionLocal)
        user = repo.save(User(name="Test User"))
        
        assert user.id is not None
        assert repo.get_by_id(user.id) == user
```

## Performance and Optimization Rules

### 1. Database Optimization
- **Rule:** Use connection pooling for database operations
- **Rule:** Implement proper indexing for frequent queries
- **Rule:** Use async/await for I/O-bound operations

**Example async database operations:**
```python
async def get_user_by_email(email: str) -> Optional[User]:
    async with AsyncSession() as session:
        result = await session.execute(
            select(User).where(User.email == email)
        )
        return result.scalar_one_or_none()
```

### 2. API Performance
- **Rule:** Implement response compression (gzip)
- **Rule:** Use caching for frequently accessed data
- **Rule:** Implement rate limiting and request throttling

**Example FastAPI with caching:**
```python
@app.get("/users/{user_id}", response_model=UserResponse)
@cache(expire=300)  # Cache for 5 minutes
async def get_user(user_id: int, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user
```

## Security Rules

### 1. Authentication and Authorization
- **Rule:** Use JWT tokens for stateless authentication
- **Rule:** Implement role-based access control (RBAC)
- **Rule:** Validate all input data against schemas

**Example JWT authentication:**
```python
@app.post("/login")
async def login(credentials: LoginSchema):
    user = authenticate_user(credentials.username, credentials.password)
    if not user:
        raise HTTPException(status_code=401, detail="Invalid credentials")
    
    access_token = create_access_token(data={"sub": user.username})
    return {"access_token": access_token, "token_type": "bearer"}
```

### 2. Security Headers and HTTPS
- **Rule:** Always use HTTPS in production
- **Rule:** Set security headers (CSP, HSTS, XSS protection)
- **Rule:** Validate and sanitize all user input

**Example security middleware:**
```python
app.add_middleware(
    SecurityHeadersMiddleware,
    content_security_policy={"default-src": "'self'"},
    strict_transport_security="max-age=31536000; includeSubDomains"
)
```

## Deployment and Operations Rules

### 1. Containerization
- **Rule:** Use multi-stage Docker builds
- **Rule:** Optimize Docker image layers for caching
- **Rule:** Use .dockerignore to exclude unnecessary files

**Example Dockerfile:**
```dockerfile
FROM python:3.11-slim as builder

WORKDIR /app
COPY requirements/base.txt .
RUN pip install --no-cache-dir -r base.txt

FROM python:3.11-slim as runtime

WORKDIR /app
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages
COPY src/ ./src/

CMD ["uvicorn", "src.project_name.interfaces.api:app", "--host", "0.0.0.0", "--port", "8000"]
```

### 2. Monitoring and Logging
- **Rule:** Implement structured logging (JSON format)
- **Rule:** Use metrics and health checks
- **Rule:** Set up error tracking and alerting

**Example structured logging:**
```python
import structlog

logger = structlog.get_logger()

def process_order(order: Order):
    try:
        # Business logic
        logger.info("order_processing_started", order_id=order.id, amount=order.amount)
        # ... processing ...
        logger.info("order_processing_completed", order_id=order.id)
    except Exception as e:
        logger.error("order_processing_failed", order_id=order.id, error=str(e))
        raise
```

## References and Further Reading

- **Clean Architecture:** Maintainable and testable code structure
- **SOLID Principles:** Object-oriented design best practices
- **Python Best Practices:** PEP 8, type hints, modern tooling
- **Study Files:** `/ai-docs/rules/architecture-solid-principles.md`
- **Study Files:** `/ai-docs/rules/project-structure.md`
- **Study Files:** `/ai-docs/rules/testing-quality.md`

## Validation Rules for Trae IDE

1. **Architecture Compliance:** Verify dependency direction (Domain → Application → Infrastructure)
2. **Type Safety:** Enforce type annotations and mypy strict mode
3. **Test Coverage:** Monitor and enforce minimum test coverage
4. **Security Scanning:** Integrate security vulnerability scanning
5. **Performance Metrics:** Track response times and resource usage
6. **Code Quality:** Enforce formatting, linting, and complexity metrics