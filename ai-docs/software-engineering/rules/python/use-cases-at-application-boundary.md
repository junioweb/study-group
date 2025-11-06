**Purpose**  
Write use cases and acceptance tests against *primary ports* — not UI or DB.

**Guidelines**
- Use case description format:  
  > *Given* a valid [context],  
  > *When* [primary actor] requests [action],  
  > *Then* [outcome] is produced, and [side effects] occur.

- Automated acceptance tests call *primary ports directly* (bypassing HTTP/CLI):  
  ```python
  def test_create_order_with_invalid_email_fails():
      port = InMemoryOrderCommandPort()
      req = CreateOrderRequest(email="invalid", items=[...])
      with pytest.raises(UserValidationError):
          port.create_order(req)
  ```

- **Never** mention:  
  ❌ “click the Submit button”  
  ❌ “INSERT INTO orders…”  
  ✅ “user submits order request”  
  ✅ “order is persisted and confirmation sent”

**Anti-Patterns**  
- Acceptance tests that start/stop HTTP server  
- Use cases tied to endpoint paths (`POST /v1/orders`)  
- “Happy path only” test coverage