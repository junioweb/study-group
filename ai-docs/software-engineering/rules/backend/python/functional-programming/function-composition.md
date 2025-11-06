## **Rule: Function Composition in Pipelines**  
**ID:** `PY-FP-COMPOSE-003`  
**Scope:** Data processing services (`src/services/`).  
**Quality Criterion:** Readability and reusability.  

### **Action:**  
- Use `toolz.compose` or `toolz.pipe` to chain transformations  
- Create transformation functions with signature `T -> U`  
- Avoid temporary variables for intermediate results  
- Name composed functions as data flows (`process_user_input`)  

### **Violation Example:**  
```python
# ❌ Bad - Intermediate variables and imperative logic
def process_data(raw_data):
    cleaned = clean_data(raw_data)
    validated = validate_data(cleaned)
    normalized = normalize_data(validated)
    return filter_active(normalized)
```

### **Correct Example:**  
```python
# ✅ Good - Declarative pipeline with toolz
from toolz import compose, pipe

# Pure transformation definitions
clean_data = lambda x: ... 
validate_data = lambda x: ...
normalize_data = lambda x: ...
filter_active = lambda x: ...

# Explicit composition
process_data = compose(
    filter_active,
    normalize_data,
    validate_data,
    clean_data
)

# OR using pipe for readability in long pipelines
def process_user_data(user_input):
    return pipe(
        user_input,
        parse_input,
        enrich_with_profile,
        apply_business_rules,
        format_response
    )
```

### **Verification:**  
```python
# Test of complete pipeline with sample data
def test_data_processing():
    raw = {"name": " John ", "active": "yes"}
    result = process_data(raw)
    assert result["name"] == "john"  # Normalized
    assert result["status"] == "active"  # Validated and filtered
```