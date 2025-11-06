**Purpose**  
Delay optimization until *after* refactoring — and only where data shows need.

**Rules**
- Never optimize before measuring  
- Use profiling (e.g., `py-spy`, `cProfile`) to identify hotspots (≥90% time in ≤10% code)  
- Only optimize if:  
  - SLA violation confirmed  
  - Hotspot validated in production-like load  
  - Design is already clean (SRP, pure core, etc.)

**Documentation Template**  
```python
# OPTIMIZATION: reduce allocations in hot loop
# Baseline: 12ms/op (10k calls)
# After:     3.2ms/op (73% improvement)
# Trade-off: reduced readability (inlined validation)
# Revert after: Python 3.13 vectorcall improvements
def _hot_path_process(items: list[Item]) -> list[Result]:
    # ... optimized code ...
```

**Anti-Patterns**  
- Manual loop unrolling in non-hot code  
- Caching derived values without evidence of recomputation cost  
- Avoiding function calls “for speed” in non-critical paths