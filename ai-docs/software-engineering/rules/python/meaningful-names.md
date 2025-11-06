**Purpose**  
Ensure names in the *Functional Core* reveal domain intent; in the *Imperative Shell*, clarify technical role.

**Scope**  
Applies to all identifiers: modules, classes, functions, variables, test names.

**Guidelines**
- Core (domain logic):  
  - Use domain nouns/verbs only (e.g., `calculate_shipping_cost`, `Order`, `PaymentPolicy`)  
  - Never leak infrastructure concerns (`db_order`, `http_response`)  
- Shell (I/O, side effects):  
  - Prefix or suffix to clarify role: `fetch_user_from_api`, `persist_order_to_db`, `send_email_via_smtp`  
  - Prefer explicit over implicit: `parse_json_body` > `load_data`

**Anti-Patterns**  
- `data`, `handler`, `process`, `manager` without domain context  
- `obj1`, `tmp`, `res` — non-searchable, non-pronounceable  
- Mixing abstraction levels: `validate_and_save_user()` (violates *Do One Thing*)

**Examples**  
✅ Core:  
```python
def apply_discount(order: Order, policy: DiscountPolicy) -> Order:
    return policy.apply(order)
```

✅ Shell:  
```python
def fetch_user_from_api(user_id: UUID) -> User | None:
    response = http_client.get(f"/users/{user_id}")
    return User.parse(response.json()) if response.ok else None
```