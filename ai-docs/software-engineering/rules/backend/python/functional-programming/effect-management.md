## **Rule: Effect Management with Explicit Types**  
**ID:** `PY-FP-EFFECTS-004`  
**Scope:** I/O and external integration modules (`src/adapters/`).  
**Quality Criterion:** Clear separation between pure logic and side effects.  

### **Action:**  
- Use `returns.Result` for operations that may fail  
- Isolate I/O in functions with `_io` or `_external` suffix  
- Core business logic must receive ready data, not I/O dependencies  
- Use `dependency_injector` for external service dependency injection  

### **Violation Example:**  
```python
# ❌ Bad - Business logic mixed with I/O
def process_payment(user_id, amount):
    user = database.get_user(user_id)  # Side effect
    if user.balance < amount:
        raise InsufficientFunds()
    payment_gateway.charge(user.card, amount)  # External effect
    user.balance -= amount  # Mutation
    database.save_user(user)  # Side effect
    return Transaction(id=generate_id(), amount=amount)
```

### **Correct Example:**  
```python
# ✅ Good - Clear separation with Result and explicit dependencies
from returns.result import Result, Success, Failure

# Pure core - no external dependencies
def validate_payment(user: User, amount: float) -> Result[Transaction, str]:
    if user.balance < amount:
        return Failure("Insufficient funds")
    return Success(Transaction(
        id=generate_id(), 
        amount=amount,
        new_balance=user.balance - amount
    ))

# Adapter layer with effects
def process_payment_io(
    user_id: str, 
    amount: float,
    user_repo,  # Abstraction for persistence
    payment_client  # Abstraction for gateway
) -> Result[dict, str]:
    user_result = user_repo.get_by_id(user_id)
    if isinstance(user_result, Failure):
        return user_result
    
    validation = validate_payment(user_result.unwrap(), amount)
    if isinstance(validation, Failure):
        return validation
    
    transaction = validation.unwrap()
    charge_result = payment_client.charge(transaction.amount)
    
    if isinstance(charge_result, Success):
        updated_user = user_result.unwrap().with_balance(transaction.new_balance)
        user_repo.save(updated_user)
        return Success(transaction.to_dict())
    
    return Failure(f"Payment failed: {charge_result.failure()}")
```

### **Verification:**  
```python
# Test of pure core without complex mocks
def test_validate_payment():
    user = User(balance=100.0)
    result1 = validate_payment(user, 50.0)
    assert isinstance(result1, Success)
    
    result2 = validate_payment(user, 150.0)
    assert isinstance(result2, Failure)
    assert "Insufficient funds" in result2.failure()
```