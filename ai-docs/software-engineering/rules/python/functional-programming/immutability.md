## **Rule: Immutability in Data Structures**  
**ID:** `PY-FP-IMMUTABLE-002`  
**Scope:** Domain classes and DTOs (`src/models/`).  
**Quality Criterion:** Prevention of accidental mutation bugs.  

### **Action:**  
- Use `@dataclass(frozen=True)` for all domain classes  
- Utilize `pyrsistent` or `functools.partial` for copies with modifications  
- Prohibit methods that alter internal state (`set_xxx`, `add_xxx`, etc)  
- Prefer pure functions for transformations (`transform_user(user) -> User`)  

### **Violation Example:**  
```python
# ❌ Bad - Mutable class with state-altering methods
class Order:
    def __init__(self, items: list):
        self.items = items
    
    def add_item(self, item: Item):
        self.items.append(item)  # Modifies internal state
```

### **Correct Example:**  
```python
# ✅ Good - Immutability with pyrsistent
from pyrsistent import pvector, PVector

@dataclass(frozen=True)
class Order:
    items: PVector[Item] = field(default_factory=pvector)
    
def add_item_to_order(order: Order, item: Item) -> Order:
    """Returns new order with item added, without modifying original"""
    return Order(items=order.items.append(item))
```

### **Verification:**  
```python
# Test ensuring immutability
def test_order_immutability():
    order1 = Order(items=pvector([Item("coffee")]))
    order2 = add_item_to_order(order1, Item("milk"))
    
    assert len(order1.items) == 1  # Original not modified
    assert len(order2.items) == 2  # New instance with change
```