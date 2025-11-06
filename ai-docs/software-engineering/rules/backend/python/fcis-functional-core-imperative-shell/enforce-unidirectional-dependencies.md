## **Rule: Enforce Unidirectional Dependencies**  
**ID:** `PY-FCIS-BOUNDARIES-001`  
**Scope:** Package and module structure (`src/core/`, `src/shell/`).  
**Quality Criterion:** Architectural integrity and testability.  

### **Action:**  
- **Core must never import from Shell**: Configure static analysis to block imports from `src/shell/` into `src/core/`  
- **Shell may import from Core**: All dependencies flow inward toward business logic  
- **Use explicit interface contracts**: Define abstract interfaces in Core that Shell implements  
- **Enforce with CI checks**: Fail builds that violate dependency direction  
- **Document boundaries visually**: Maintain architecture diagrams showing permitted dependency flows  

### **Violation Example:**  
```python
# ❌ Bad - Core importing from Shell (dependency inversion)
# src/core/order_processing.py
from src.shell.database import get_user_from_db  # VIOLATION: Core depends on Shell

def calculate_discount(user_id: str, amount: float) -> float:
    user = get_user_from_db(user_id)  # Hidden side effect in Core
    return amount * (1 - user.loyalty_level * 0.05)
```

### **Correct Example:**  
```python
# ✅ Good - Clean separation with explicit boundaries
# src/core/order_processing.py (Functional Core)
from dataclasses import dataclass
from typing import Protocol

@dataclass(frozen=True)
class User:
    id: str
    loyalty_level: float

class UserRepository(Protocol):
    """Abstract interface defined in Core, implemented in Shell"""
    def get_by_id(self, user_id: str) -> User:
        ...

def calculate_discount(user_service: UserRepository, user_id: str, amount: float) -> float:
    """Pure function with explicit dependencies"""
    user = user_service.get_by_id(user_id)  # No hidden dependencies
    return amount * (1 - user.loyalty_level * 0.05)

# src/shell/database.py (Imperative Shell)
from src.core.order_processing import UserRepository, User

class DatabaseUserRepository(UserRepository):
    """Shell implementation of Core interface"""
    def get_by_id(self, user_id: str) -> User:
        # Actual database call with error handling
        db_result = connection.query("SELECT * FROM users WHERE id = %s", [user_id])
        if not db_result:
            raise UserNotFoundError(f"User {user_id} not found")
        return User(id=user_id, loyalty_level=float(db_result["loyalty_level"]))
```

### **Verification:**  
```bash
# .pre-commit-config.yaml
repos:
- repo: https://github.com/pre-commit/pre-commit-hooks
  rev: v4.4.0
  hooks:
  - id: check-added-large-files
  - id: detect-private-key
- repo: https://github.com/pre-commit/mirrors-mypy
  rev: v1.4.1
  hooks:
  - id: mypy
    args: [--strict, --namespace-packages]
- repo: local
  hooks:
  - id: validate-core-shell-boundaries
    name: Validate FCIS boundaries
    entry: python scripts/validate_dependencies.py
    language: python
    pass_filenames: false
    always_run: true
```

```python
# scripts/validate_dependencies.py
import sys
from pathlib import Path
import ast

def check_imports():
    violations = []
    core_path = Path("src/core")
    
    for py_file in core_path.rglob("*.py"):
        tree = ast.parse(py_file.read_text(), filename=str(py_file))
        
        for node in ast.walk(tree):
            if isinstance(node, ast.Import) or isinstance(node, ast.ImportFrom):
                if any("shell" in name.name for name in node.names) or \
                   (hasattr(node, "module") and node.module and "shell" in node.module):
                    violations.append(f"VIOLATION in {py_file}: Core module imports from Shell")
    
    return violations

if __name__ == "__main__":
    violations = check_imports()
    if violations:
        print("\n".join(violations))
        sys.exit(1)
```