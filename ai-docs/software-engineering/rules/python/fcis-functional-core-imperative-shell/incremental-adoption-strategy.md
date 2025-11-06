## **Rule: Incremental Adoption Strategy**  
**ID:** `PY-FCIS-ADOPTION-004`  
**Scope:** Project planning and team onboarding.  
**Quality Criterion:** Sustainable migration and team buy-in.  

### **Action:**  
- **Identify candidate modules**: Start with high-business-value, low-I/O modules for initial refactoring  
- **Measure before and after**: Track bug rates, test coverage, and onboarding time as success metrics  
- **Three-phase adoption**: 
  1. **Island creation**: Create new features using FCIS pattern
  2. **Strangler pattern**: Gradually extract Core from existing modules
  3. **Systematic migration**: Refactor remaining modules following dependency rules
- **Team enablement**: Pair programming sessions focused on boundary identification and effect isolation  
- **Architectural enforcement**: Progressive static analysis rules (warning → error) to prevent boundary violations  

### **Violation Example:**  
```python
# ❌ Bad - Big bang rewrite with no metrics or incremental approach
"""
Team decision log:
- We're rewriting the entire monolith to FCIS in 3 months
- No measurements of current system to compare against
- All developers must learn functional programming immediately
- No gradual migration path for existing features
- No enforcement mechanisms to prevent boundary violations
"""

# src/legacy/
# ... thousands of lines of mixed-concern code with no clear boundaries

# src/new_fcis/
# ... incomplete rewrite with the same problems in new structure
```

### **Correct Example:**  
```python
# ✅ Good - Incremental adoption with clear metrics and progression
"""
Adoption roadmap:
1. Month 1: Create FCIS structure for new Payment Service
   - Metrics baseline: Current payment bug rate = 15%, test coverage = 40%
   - Success criteria: Bug rate < 5%, test coverage > 85% after 3 months

2. Month 2-3: Extract User Profile Core from legacy module
   - Strategy: Strangler pattern - route new features through FCIS structure
   - Legacy code remains but no new features added to it

3. Month 4-6: Systematic migration of high-value modules
   - Priority: Order processing > Inventory > Reporting
   - Each module follows the 3-step extraction process:
     a) Identify pure functions in legacy code
     b) Move to Core with tests
     c) Create Shell adapters for remaining impure code

4. Ongoing: Architectural governance
   - Pre-commit hooks enforce boundaries
   - Monthly architecture reviews
   - Developer pairing sessions for knowledge transfer
"""

# Adoption metrics dashboard
PAYMENT_SERVICE_METRICS = {
    "baseline": {
        "bug_rate": 0.15,  # 15% of deployments have payment-related bugs
        "test_coverage": 0.40,
        "mean_time_to_fix": "4 hours"
    },
    "current": {
        "bug_rate": 0.03,  # 3% after FCIS adoption
        "test_coverage": 0.92,
        "mean_time_to_fix": "30 minutes"
    },
    "target": {
        "bug_rate": 0.02,
        "test_coverage": 0.95,
        "mean_time_to_fix": "15 minutes"
    }
}

# 3-step extraction process for legacy modules
def extract_user_profile_core(legacy_module_path: str):
    """
    Step 1: Identify pure functions in legacy code
    - Use static analysis to find functions with no external dependencies
    - Mark candidate functions with @core_candidate decorator
    """
    core_candidates = identify_pure_functions(legacy_module_path)
    
    """
    Step 2: Move to Core with comprehensive tests
    - Create src/core/user_profile.py
    - Write property-based tests for all edge cases
    - Ensure 100% test coverage before proceeding
    """
    create_core_module(core_candidates, tests=True)
    
    """
    Step 3: Create Shell adapters for remaining impure code
    - Implement UserRepository interface in src/shell/adapters/
    - Update legacy code to use new Core module through adapters
    - Gradually route new features through FCIS structure
    """
    create_shell_adapters(core_candidates)
    update_legacy_integration_points()

# Developer onboarding checklist
FCIS_ONBOARDING = {
    "week_1": [
        "Complete functional programming fundamentals workshop",
        "Pair with senior developer on simple Core module",
        "Write first pure function with property-based tests"
    ],
    "week_2": [
        "Implement Shell adapter for existing Core function",
        "Review boundary enforcement rules in code review",
        "Fix boundary violation in legacy code"
    ],
    "week_3": [
        "Lead extraction of small module using 3-step process",
        "Present architectural decision to team",
        "Mentor another developer on FCIS concepts"
    ]
}
```

### **Verification:**  
```python
# scripts/validate_adoption_progress.py
import json
from pathlib import Path

def calculate_adoption_metrics():
    """Measure FCIS adoption progress across the codebase"""
    metrics = {
        "modules_converted": 0,
        "total_modules": 0,
        "boundary_violations": 0,
        "test_coverage_core": 0.0,
        "bug_rate_improvement": 0.0
    }
    
    # Count modules following FCIS structure
    core_modules = list(Path("src/core").rglob("*.py"))
    shell_modules = list(Path("src/shell").rglob("*.py"))
    legacy_modules = list(Path("src/legacy").rglob("*.py"))
    
    metrics["modules_converted"] = len(core_modules) + len(shell_modules)
    metrics["total_modules"] = metrics["modules_converted"] + len(legacy_modules)
    
    # Check for boundary violations
    with open("boundary_violations.log") as f:
        violations = f.read().count("VIOLATION")
        metrics["boundary_violations"] = violations
    
    # Get test coverage for Core modules only
    coverage_report = json.load(open("coverage-core.json"))
    metrics["test_coverage_core"] = coverage_report["totals"]["percent_covered"]
    
    # Calculate bug rate improvement
    current_bugs = get_bug_count("last_30_days")
    previous_bugs = get_bug_count("previous_30_days")
    metrics["bug_rate_improvement"] = (previous_bugs - current_bugs) / previous_bugs
    
    return metrics

def generate_adoption_report():
    """Generate executive summary of adoption progress"""
    metrics = calculate_adoption_metrics()
    progress = metrics["modules_converted"] / metrics["total_modules"]
    
    report = f"""
    FCIS Adoption Progress Report
    =============================
    Overall Progress: {progress:.1%} ({metrics['modules_converted']}/{metrics['total_modules']} modules)
    
    Quality Metrics:
    - Core Test Coverage: {metrics['test_coverage_core']:.1f}%
    - Boundary Violations: {metrics['boundary_violations']} (target: 0)
    - Bug Rate Improvement: {metrics['bug_rate_improvement']:.1%}
    
    Next Actions:
    1. Focus on extracting Order Processing module (highest business value)
    2. Address 3 critical boundary violations in src/core/auth.py
    3. Schedule pairing session for team members struggling with effect isolation
    """
    
    Path("reports/adoption_progress.md").write_text(report)
    return report
```