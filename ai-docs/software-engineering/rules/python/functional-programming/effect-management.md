## Rule: Effect Isolation at System Boundaries
ID: `PY-FP-EFFECTS-001`
Scope: All adapter modules and shell functions (`src/adapters/`, `src/app/`)
Quality Criterion: Side effects are explicit, testable, and contained.

### Core Principle
"Push all side effects to the edges of the system. The functional core should remain pure and deterministic."
(Complements functional-code-imperative-shell.md)

### Action:
Explicitly identify and isolate effectful operations:
- Mark effectful functions with suffix `_effect`, `_with_io`, or `_cmd`
- Use type hints to indicate effects: `Callable[..., IO[A]]`, `Callable[..., Task[A]]`
- Create dedicated effect handlers for common operations (database, http, filesystem)
- Separate effect execution from effect definition

### Never allow in functional core:
- Direct database calls, HTTP requests, or file operations
- Random number generation without explicit seed parameter
- Current time usage without explicit time provider parameter
- Global state modification or access

Implementation patterns:
```python
# 1. Effect definition (pure)
def create_user_effect(user_data: UserData) -> IO[User]:
    """Defines the effect without executing it"""
    return IO(lambda: database.insert("users", user_data))

# 2. Effect handler (imperative shell)
def execute_io(io_effect: IO[A], config: AppConfig) -> A:
    """Executes effects with proper error handling and logging"""
    try:
        return io_effect.run()
    except DatabaseError as e:
        logger.error(f"Database operation failed: {e}")
        raise UserCreationError(f"Failed to create user: {str(e)}")

# 3. Time dependency injection
def generate_report_effect(users: List[User], now: Callable[[], datetime]) -> str:
    """Time dependency explicitly injected"""
    timestamp = now().isoformat()
    return f"Report generated at {timestamp} for {len(users)} users"
```

Verification:
```python
# Test effect definition without execution
def test_user_creation_effect_definition():
    user_data = UserData(name="Alice", email="alice@example.com")
    effect = create_user_effect(user_data)
    assert isinstance(effect, IO)  # Verify effect type without execution

# Test effect handler with mocked dependencies
def test_execute_io_with_failure():
    failing_effect = IO(lambda: raise DatabaseError("connection failed"))
    config = AppConfig(db_url="test")
    
    with pytest.raises(UserCreationError):
        execute_io(failing_effect, config)
```

### Anti-Patterns
❌ Calling database directly from business logic functions
❌ Using datetime.now() directly in core calculations
❌ "Hidden" effects where function name doesn't indicate I/O
❌ Effect handling scattered throughout code instead of dedicated modules
❌ Testing core logic by mocking database calls (should test against pure functions)