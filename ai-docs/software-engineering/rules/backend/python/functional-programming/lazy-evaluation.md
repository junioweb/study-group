## **Rule: Lazy Evaluation for Large Datasets**  
**ID:** `PY-FP-LAZY-006`  
**Scope:** Batch data processing (`src/batch_processing/`).  
**Quality Criterion:** Memory efficiency and on-demand processing.  

### **Action:**  
- Use generators (`yield`) for data stream processing  
- Implement `itertools` for operations on large collections  
- Avoid `list()` in intermediate transformations  
- Utilize `more_itertools` for advanced generator operations  

### **Violation Example:**  
```python
# ❌ Bad - Loads everything into memory
def process_logs(log_file_path: str) -> List[LogEntry]:
    with open(log_file_path) as f:
        lines = f.readlines()  # Loads entire file into memory
        cleaned = [clean_line(line) for line in lines]  # Intermediate list
        parsed = [parse_log(line) for line in cleaned]  # Another intermediate list
        filtered = [log for log in parsed if log.level == "ERROR"]  # Yet another list
    return filtered
```

### **Correct Example:**  
```python
# ✅ Good - Lazy processing with generators
from typing import Generator, Iterator

def read_logs(file_path: str) -> Generator[str, None, None]:
    """Reads file line by line without loading everything into memory"""
    with open(file_path) as f:
        for line in f:
            yield line

def clean_logs(lines: Iterator[str]) -> Generator[str, None, None]:
    for line in lines:
        yield clean_line(line)

def parse_logs(lines: Iterator[str]) -> Generator[LogEntry, None, None]:
    for line in lines:
        yield parse_log(line)

def filter_errors(entries: Iterator[LogEntry]) -> Iterator[LogEntry]:
    return (entry for entry in entries if entry.level == "ERROR")

def process_logs_lazy(log_file_path: str) -> List[LogEntry]:
    """Lazy pipeline - only processes what's needed"""
    pipeline = pipe(
        log_file_path,
        read_logs,
        clean_logs,
        parse_logs,
        filter_errors,
        list  # Materializes only at the end
    )
    return pipeline
```

### **Verification:**  
```python
# Test with simulated large file
def test_memory_efficiency():
    # Simulates 1GB file with 10 million lines
    large_file = generate_test_file(lines=10_000_000)
    
    # Checks memory usage during processing
    import tracemalloc
    tracemalloc.start()
    
    result = process_logs_lazy(large_file)
    
    current, peak = tracemalloc.get_traced_memory()
    assert peak < 100 * 1024 * 1024  # Less than 100MB peak
    tracemalloc.stop()
```