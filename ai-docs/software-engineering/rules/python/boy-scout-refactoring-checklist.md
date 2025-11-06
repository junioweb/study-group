**Purpose**  
Enforce incremental improvement on every change — no net-negative PRs.

**Mandatory PR Checklist** *(at least one item must be checked)*  
- [ ] Reduced function length (e.g., 25 → 18 lines)  
- [ ] Improved naming (e.g., `data` → `customer_profile`)  
- [ ] Extracted pure function from shell  
- [ ] Replaced `if/else` chain with strategy/polymorphism  
- [ ] Encapsulated data clump into value object  
- [ ] Removed obsolete comment  
- [ ] Added missing test for edge case  
- [ ] Lowered cyclomatic complexity by ≥1

**Enforcement**  
- PR description must list *what smell was fixed*  
- CI blocks merge if checklist not addressed  
- Reviewers verify *net improvement*, not just correctness