## **Rule: Strict Side Effect Isolation**  
**ID:** `PY-FCIS-EFFECTS-002`  
**Scope:** Function signatures and implementation patterns.  
**Quality Criterion:** Referential transparency and testability.  

### **Action:**  
- **Core functions must be pure**: No I/O, no mutation, no external state access  
- **Shell handles all effects**: Database calls, network requests, file operations, logging  
- **Dependency rejection pattern**: Core accepts data, not services or callbacks  
- **Immutable data crossing boundaries**: Convert mutable external data to frozen dataclasses at Shell boundaries  
- **Explicit effect types**: Use Result types (Success/Failure) to model potential failures without exceptions  

### **Violation Example:**  
```python
# ❌ Bad - Mixed concerns with hidden side effects
# src/core/payment.py
import requests  # VIOLATION: External dependency in Core
from datetime import datetime  # VIOLATION: Non-deterministic dependency

def process_payment(user_id, amount):
    """Hidden side effects make this untestable without mocks"""
    user = get_user_from_db(user_id)  # VIOLATION: Database call in Core
    currency_rate = requests.get(f"https://api.exchangerate.host/latest?base=USD").json()  # VIOLATION: Network call
    converted_amount = amount * currency_rate["rates"]["BRL"]
    
    if user.balance < converted_amount:
        log_transaction(user_id, "INSUFFICIENT_FUNDS")  # VIOLATION: Logging side effect
        raise ValueError("Insufficient funds")
    
    user.balance -= converted_amount  # VIOLATION: Mutation
    return {
        "transaction_id": generate_id(),  # VIOLATION: Non-deterministic function
        "timestamp": datetime.now(),  # VIOLATION: Non-deterministic value
        "amount": converted_amount
    }
```

### **Correct Example:**  
```python
# ✅ Good - Strict separation of concerns
# src/core/payment.py (Functional Core)
from dataclasses import dataclass
from typing import Union
from datetime import datetime

@dataclass(frozen=True)
class User:
    id: str
    balance: float
    currency: str

@dataclass(frozen=True)
class TransactionResult:
    transaction_id: str
    timestamp: datetime
    amount: float
    converted_amount: float

class InsufficientFundsError(Exception):
    pass

def process_payment(
    user: User,
    amount: float,
    exchange_rates: dict[str, float],
    transaction_id: str,
    timestamp: datetime
) -> TransactionResult:
    """Pure function with all dependencies explicit"""
    if user.currency not in exchange_rates:
        raise ValueError(f"Unsupported currency: {user.currency}")
    
    converted_amount = amount * exchange_rates[user.currency]
    
    if user.balance < converted_amount:
        raise InsufficientFundsError(f"User {user.id} has insufficient funds")
    
    return TransactionResult(
        transaction_id=transaction_id,
        timestamp=timestamp,
        amount=amount,
        converted_amount=converted_amount
    )

# src/shell/payment_service.py (Imperative Shell)
from src.core.payment import User, process_payment, InsufficientFundsError
import requests
from uuid import uuid4
from datetime import datetime

def handle_payment(user_id: str, amount: float) -> dict:
    """Shell handles all effects and converts to/from Core types"""
    try:
        # Get data from external sources
        user_data = database.get_user(user_id)
        exchange_rates = requests.get("https://api.exchangerate.host/latest?base=USD").json()["rates"]
        
        # Convert to Core types
        user = User(
            id=user_data["id"],
            balance=float(user_data["balance"]),
            currency=user_data["currency"]
        )
        
        # Call pure Core function
        result = process_payment(
            user=user,
            amount=amount,
            exchange_rates=exchange_rates,
            transaction_id=str(uuid4()),
            timestamp=datetime.now()
        )
        
        # Handle effects
        database.update_user_balance(user_id, result.converted_amount)
        logger.info(f"Payment processed: {result.transaction_id}")
        audit_log.record_transaction(result)
        
        # Convert back to response format
        return {
            "success": True,
            "transaction_id": result.transaction_id,
            "amount": result.converted_amount,
            "currency": user.currency
        }
        
    except InsufficientFundsError as e:
        logger.warning(f"Payment failed: {str(e)}")
        return {"success": False, "error": "INSUFFICIENT_FUNDS"}
    except Exception as e:
        logger.error(f"Unexpected payment error: {str(e)}", exc_info=True)
        return {"success": False, "error": "SYSTEM_ERROR"}
```

### **Verification:**  
```python
# tests/unit/core/test_payment.py
def test_process_payment_successful():
    user = User(id="user1", balance=100.0, currency="BRL")
    rates = {"BRL": 5.0}  # 1 USD = 5 BRL
    result = process_payment(
        user=user,
        amount=10.0,  # $10 USD
        exchange_rates=rates,
        transaction_id="tx123",
        timestamp=datetime(2023, 1, 1)
    )
    assert result.converted_amount == 50.0  # 10 * 5 = 50 BRL
    assert result.transaction_id == "tx123"

def test_process_payment_insufficient_funds():
    user = User(id="user1", balance=40.0, currency="BRL")
    rates = {"BRL": 5.0}
    
    with pytest.raises(InsufficientFundsError):
        process_payment(
            user=user,
            amount=10.0,
            exchange_rates=rates,
            transaction_id="tx123",
            timestamp=datetime(2023, 1, 1)
        )

# tests/integration/shell/test_payment_service.py
def test_handle_payment_integration(mock_database, mock_requests):
    """Integration test with mocked external services"""
    mock_database.get_user.return_value = {
        "id": "user1",
        "balance": "100.0",
        "currency": "BRL"
    }
    mock_requests.get.return_value.json.return_value = {
        "rates": {"BRL": 5.0}
    }
    
    result = handle_payment("user1", 10.0)
    assert result["success"] is True
    assert result["amount"] == 50.0  # Converted amount
    mock_database.update_user_balance.assert_called_once_with("user1", 50.0)
```