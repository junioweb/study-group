## **Rule: Functional Type System Patterns**  
**ID:** `PY-FP-TYPING-001`  
**Scope:** All Python modules with business logic.  
**Quality Criterion:** Safe data transformation and referential transparency.  

### **Core Principle**  
> "Model domain data with immutable structures, isolate effects at boundaries, and let the type system document behavior."  
> *(Inspired by 04-type-system-and-polymorphism.md and 07-effects-management.md)*

### **Action:**  
- **For domain models**: Use `@dataclass(frozen=True)` for entities and value objects  
- **For external data**: Use Pydantic models **only** at system boundaries (APIs, database adapters)  
- **For configuration/constants**: Use `NamedTuple` for simple immutable structures  
- **Never use**: `TypedDict` for domain modeling or mutable dataclasses in core logic  
- **Validate invariants**: Use `__post_init__` in dataclasses for domain validation  

### **Violation Example:**  
```python
# ❌ Bad - Mixed concerns and mutable structures
from typing import TypedDict

class User(TypedDict):
    id: str
    name: str
    is_active: bool

def update_user(user: User, **kwargs) -> User:
    """Modifies user in-place - breaks referential transparency"""
    user.update(kwargs)  # Mutation!
    return user

# Using in business logic
def send_welcome_email(user_data):
    user = update_user(user_data, welcome_sent=True)  # Side effect hidden
    email_service.send(user['email'], "Welcome!")  # IO mixed with logic
```

### **Correct Example:**  
```python
# ✅ Good - Separation of concerns with proper typing
from dataclasses import dataclass, field
from typing import NamedTuple, Optional
from pydantic import BaseModel  # Only for boundary models

# 1. Domain model (immutable core)
@dataclass(frozen=True)
class User:
    id: str
    name: str
    email: str
    is_active: bool = True
    welcome_sent: bool = False
    
    def with_welcome_sent(self) -> "User":
        """Pure transformation - preserves immutability"""
        return User(
            id=self.id,
            name=self.name,
            email=self.email,
            is_active=self.is_active,
            welcome_sent=True
        )
    
    def __post_init__(self):
        if "@" not in self.email:
            raise ValueError("Invalid email format")

# 2. Boundary model (external interface only)
class UserCreateRequest(BaseModel):
    name: str
    email: str

# 3. Configuration (simple immutable structure)
class EmailConfig(NamedTuple):
    smtp_host: str
    smtp_port: int = 587
    timeout: float = 30.0

# 4. Pure business logic with explicit dependencies
def prepare_welcome_message(user: User) -> str:
    """Pure function - no side effects, easy to test"""
    return f"Hello {user.name}! Welcome to our platform."

# 5. Effect isolation at boundaries
def handle_user_registration(
    request: UserCreateRequest,
    email_config: EmailConfig
) -> User:
    """Imperative shell - handles effects at boundary"""
    # Convert boundary model to domain model
    user = User(
        id=generate_id(),
        name=request.name,
        email=request.email
    )
    
    # Pure transformation
    updated_user = user.with_welcome_sent()
    
    # Effect handling (isolated)
    message = prepare_welcome_message(updated_user)
    send_email(email_config, updated_user.email, message)
    
    return updated_user

# Helper with explicit effect type
def send_email(config: EmailConfig, to: str, message: str) -> bool:
    """Clearly marked effect function - no hidden I/O in core logic"""
    # Implementation with SMTP client using config
    pass
```

### **Verification Strategy:**  
```python
# 1. mypy configuration (pyproject.toml)
[tool.mypy]
disallow_untyped_defs = true
warn_return_any = true
no_implicit_optional = true
strict_equality = true
check_untyped_defs = true

# 2. Test immutability and purity
def test_user_immutability():
    user = User(id="1", name="Alice", email="alice@example.com")
    
    # Transformation creates new instance
    updated = user.with_welcome_sent()
    assert updated.welcome_sent is True
    assert user.welcome_sent is False  # Original unchanged
    
    # Cannot modify directly
    with pytest.raises(FrozenInstanceError):
        user.name = "Bob"

# 3. Test boundary conversion
def test_boundary_to_domain():
    request = UserCreateRequest(name="Bob", email="bob@example.com")
    user = User(id="2", **request.model_dump(mode="python"))
    assert user.name == "Bob"
```

### **When to Break This Rule:**  
While this pattern covers 90% of cases, practical exceptions include:
- **Performance-critical paths**: Use mutable structures with explicit documentation when profiling shows bottlenecks
- **Legacy system integration**: Create adapter layers that convert between functional and imperative styles
- **Simple scripts**: For throwaway code or simple CLI tools, lighter typing may be appropriate