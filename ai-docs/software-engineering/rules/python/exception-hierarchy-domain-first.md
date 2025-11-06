**Purpose**  
Design exception hierarchy aligned with domain semantics — not technical layers.

**Guidelines**
- Define exceptions in `src/core/exceptions.py`  
- Group by domain concept, not cause:  
  ```python
  # core/exceptions.py
  class OrderException(Exception): pass
  class OrderValidationError(OrderException): pass
  class OrderAlreadyPaid(OrderException): pass

  class PaymentException(Exception): pass
  class PaymentDeclined(PaymentException): pass
  ```
- **Never** use generic exceptions (`Exception`, `RuntimeError`) in core  
- Shell catches domain exceptions and translates to transport-level errors (e.g., HTTP 400/500)  
- Include **correlation ID** and **structured context** in all error logs  

**Anti-Patterns**  
- `raise Exception("Invalid email")`  
- `except Exception as e:` without re-raising or logging context  
- Returning `None` or error codes instead of raising

**Example**  
```python
# Core
def validate_email(email: str) -> Email:
    if "@" not in email:
        raise UserValidationError(
            code="INVALID_EMAIL",
            message="Email must contain '@'",
            context={"email": email}
        )
    return Email(email)

# Shell
@app.post("/users")
def create_user(req: CreateUserRequest):
    try:
        user = create_user_core(req.email, req.name)
        return user.to_dto()
    except UserValidationError as e:
        logger.warning("User creation failed", exc_info=True, extra=e.context)
        raise HTTPException(400, detail=e.message)
```