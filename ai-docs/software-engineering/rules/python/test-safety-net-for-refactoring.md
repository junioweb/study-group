**Purpose**  
Treat test suite as *non-negotiable safety net* — no refactoring without coverage.

**Rules**
- **Unit tests (≥70% of suite)**:  
  - Target *Functional Core only*  
  - Pure functions → no mocks, no setup  
  - Must pass in <100ms each  
  - Name: `test_<function>_<scenario>_<expectation>`  
- **Integration tests (≤20%)**:  
  - Test *Core + Adapter* (e.g., core + SQLite repo)  
  - Use real/fake adapters — no mocks of domain logic  
- **No refactoring allowed** if:  
  - Critical path has <80% branch coverage  
  - No characterization test for legacy behavior  

**Anti-Patterns**  
- Mocking core logic (e.g., `mock.patch('core.calculate_total')`)  
- Tests with `time.sleep()` or global state  
- `"# TODO: write test"` in merged code  

**Example**  
```python
def test_calculate_total_with_discount_applied_returns_correct_amount():
    # Given
    order = Order(items=[Item("A", Money(10, "USD"), qty=2)])
    policy = FixedDiscountPolicy(rate=Decimal("0.1"))

    # When
    total = calculate_total(order, tax_table=NO_TAX)
    discounted = policy.apply_to(total)

    # Then
    assert discounted == Money(18, "USD")
```