## Purpose
Apply package architecture principles for maintainable, scalable Python projects.

### 1. Package Cohesion Principles
- **REP (Reuse/Release Equivalency Principle)**:
  - Granularity of reuse equals granularity of release
  - Package everything needed to reuse a component in a single release unit
- **CCP (Common Closure Principle)**:
  - Classes that change together belong in the same package
  - Minimize number of packages modified per requirement change
- **CRP (Common Reuse Principle)**:
  - Classes that aren't reused together shouldn't be in the same package
  - Avoid forcing clients to depend on unused classes

### 2. Package Dependency Rules
- **Dependency structure must be acyclic** (no circular dependencies)
- **Dependency direction must follow stability**:
  ```
  Stable packages (less likely to change) → Unstable packages (more likely to change)
  ```
- **Package structure must reflect architectural boundaries**:
  ```
  src/
  ├── domain/          # Most stable package
  ├── application/     # Depends on domain
  ├── infrastructure/  # Depends on application and domain
  └── interfaces/      # Depends on all others (least stable)
  ```

### 3. Evolution Strategy
- **Early stage (prototyping)**: Prioritize CCP - group by reasons for change
  ```
  # Early stage package structure (CCP focus)
  src/
  └── features/
      ├── inventory/
      ├── orders/
      └── payments/
  ```
- **Mature stage (production)**: Prioritize REP/CRP - extract reusable components
  ```
  # Mature package structure (REP/CRP focus)
  src/
  ├── core/            # Domain model with high stability
  ├── common/          # Reusable components (validation, dates, etc.)
  ├── features/        # Business features
  └── infrastructure/  # External dependencies
  ```

### 4. Anti-Patterns Explicitly Blocked
- **Utility packages**: No `utils/`, `helpers/`, or `common/` packages with random utilities
- **God packages**: No package with >20 files without sub-packages
- **Circular dependencies**: Detected and blocked at CI time
- **Volatility clusters**: Unstable classes grouped with stable classes in same package

### 5. Package Testing Strategy
- **Package-level tests** validate dependency rules:
  ```python
  def test_package_dependencies():
      # Validate that domain package has no external dependencies
      assert len(get_package_dependencies("src/domain")) == 0
      
      # Validate that infrastructure depends on domain but not vice versa
      infra_deps = get_package_dependencies("src/infrastructure")
      assert "src/domain" in infra_deps
      assert "src/infrastructure" not in get_package_dependencies("src/domain")
  ```
- **Stability metrics** tracked over time:
  - Afferent Coupling (Ca): Number of packages depending on this package
  - Efferent Coupling (Ce): Number of packages this package depends on
  - Instability (I): Ce / (Ca + Ce) - should be low for core packages

### 6. Python-Specific Implementation
- **Namespaces over nested packages** when possible:
  ```python
  # GOOD: Flat namespace structure
  from domain.model import Batch
  from domain.repositories import AbstractRepository
  
  # BAD: Deep nesting
  from src.application.services.order.order_service import OrderService
  ```
- **Type hints for package boundaries**:
  ```python
  # domain/__init__.py
  from typing import TYPE_CHECKING
  
  if TYPE_CHECKING:
      from .repositories import AbstractRepository  # Avoid circular imports in type checking
  ```
- **Lazy imports for optional dependencies**:
  ```python
  def send_email(recipient: str, message: str) -> bool:
      try:
          import smtplib  # Lazy import for optional dependency
          # Implementation
      except ImportError:
          logger.warning("smtplib not available")
          return False
  ```