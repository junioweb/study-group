## **Rule: Pure Functions for Business Logic**  
**ID:** `PY-FP-PURE-001`  
**Scope:** Business logic modules (`src/domain/`).  
**Quality Criterion:** Determinism and testability.  

### **Action:**  
- Functions must depend **only** on input parameters  
- Prohibit access to global variables, external state, or I/O within the function  
- Return new objects instead of modifying input parameters  
- Explicitly document external dependencies as parameters  

### **Violation Example:**  
```python
# ❌ Bad - Access to global state and side effects
user_cache = {}

def calculate_discount(user_id: str, amount: float) -> float:
    if user_id not in user_cache:
        user_cache[user_id] = fetch_user_from_db(user_id)  # Side effect
    user = user_cache[user_id]
    return amount * (1 - user.loyalty_level * 0.05)
```

### **Correct Example:**  
```python
# ✅ Good - Pure function with all dependencies explicit
def calculate_discount(user: User, amount: float) -> float:
    """Calculate discount based on user loyalty level.
    
    Args:
        user: User object with loyalty_level attribute (0.0 to 1.0)
        amount: Total amount before discount
    """
    discount_rate = user.loyalty_level * 0.05
    return amount * (1 - discount_rate)
```

### **Verification:**  
```python
# Simple unit test for pure function
def test_calculate_discount():
    user = User(loyalty_level=0.8)
    assert calculate_discount(user, 100.0) == 60.0
    # No need to mock database or cache
```