## Rule: Lazy Evaluation for Resource Efficiency
ID: `PY-FP-LAZY-001`
Scope: Data processing pipelines and large dataset handling (`src/services/`, `src/batch_processing/`)
Quality Criterion: Memory efficiency and on-demand computation.

### Core Principle
"Process data streams incrementally to minimize memory footprint and enable early termination."
(Complements testing-strategy.md for performance validation)

### Action:
Use generators for streaming data processing:
- Prefer `yield` over returning lists for sequential data
- Use `Iterator`, `Generator`, or `Iterable` type hints for stream boundaries
- Implement processing pipelines with `toolz.pipe` or custom composition
- Materialize collections only at endpoints (e.g., before returning to caller)

### Optimize with standard library tools:
- Use `itertools` for transformations on large collections
- Leverage `more_itertools` for advanced generator operations when appropriate
- Employ `heapq` and other memory-efficient algorithms for sorted data

### Avoid these performance pitfalls:
- Calling `list()` on generators in intermediate steps
- Loading entire datasets into memory before processing
- Creating multiple intermediate collections in transformation chains

### Implementation patterns:
```python
from typing import Generator, Iterator, TypeVar
from toolz import pipe

T = TypeVar('T')

def read_lines(file_path: str) -> Generator[str, None, None]:
    """Memory-efficient file reading"""
    with open(file_path) as f:
        for line in f:
            yield line.rstrip('\n')

def transform_data(lines: Iterator[str]) -> Iterator[ProcessedRecord]:
    """Chain transformations without intermediate collections"""
    return (
        parse_record(clean_line(line))
        for line in lines
        if is_valid_line(line)
    )

def process_large_dataset(file_path: str) -> List[ProcessedRecord]:
    """Materialize only at endpoint"""
    return pipe(
        file_path,
        read_lines,
        transform_data,
        list  # Only materialization point
    )
```

### Verification:
```python
# Memory usage test
def test_memory_efficiency():
    # Simulate large dataset processing
    import tracemalloc
    tracemalloc.start()
    
    # Process 1M records without materializing intermediates
    result = process_large_dataset("test_data.csv")
    
    current, peak = tracemalloc.get_traced_memory()
    assert peak < 50 * 1024 * 1024  # Less than 50MB peak memory
    assert len(result) == 1000  # Expected output size
    tracemalloc.stop()

# Early termination test
def test_early_termination():
    # Verify pipeline stops after finding first match
    items = (x for x in range(1000000))  # Large generator
    result = next((x for x in items if x > 10), None)
    # Should not process all million items
    assert result == 11
```

### Anti-Patterns
❌ `data = [process(x) for x in load_all_data()]` - Processing after full load
❌ Converting generators to lists before filtering: `list(filter(pred, gen))`
❌ Using pandas/DataFrames for simple transformations that could be streams
❌ Loading entire JSON/XML files into memory instead of streaming parsers
❌ Processing files line-by-line but storing all results before returning