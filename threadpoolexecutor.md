

# Introduction to Python ThreadPoolExecutor Tutorial

## What is ThreadPoolExecutor?
`ThreadPoolExecutor` is a high-level interface in Python’s `concurrent.futures` module for managing a pool of threads. It simplifies thread management by handling thread creation, task scheduling, and cleanup automatically. It’s ideal for I/O-bound tasks like network requests or file operations.

## Why Use ThreadPoolExecutor?
- Simplifies thread management compared to manually creating `threading.Thread` objects.
- Reuses threads, reducing overhead.
- Provides easy ways to handle task results and exceptions.
- Scales efficiently for multiple tasks.

## Key Features
- **Thread Pool**: A fixed number of threads execute tasks from a queue.
- **submit()**: Schedules a single task and returns a `Future` object.
- **map()**: Applies a function to an iterable, distributing tasks across the thread pool.
- **Context Manager**: Ensures proper cleanup using `with` statement.
- **max_workers**: Controls the number of threads in the pool.

## Basic Example
Here’s a simple example to get started:

```python
from concurrent.futures import ThreadPoolExecutor
import time

def process_task(name):
    time.sleep(1)
    return f"Task {name} processed"

with ThreadPoolExecutor(max_workers=2) as executor:
    results = executor.map(process_task, ["A", "B", "C"])
    for result in results:
        print(result)
```

## Important Notes
- **GIL Limitation**: Like the `threading` module, `ThreadPoolExecutor` is affected by Python’s Global Interpreter Lock (GIL), making it best for I/O-bound tasks.
- **Thread Safety**: Use locks for shared resources, as threads share memory.
- **Error Handling**: Exceptions in tasks are captured in `Future` objects or propagated when using `map()`.

---

## 10 Example Programs

### Example 1: Basic Task Execution
Process multiple tasks using `map()`.

```python
from concurrent.futures import ThreadPoolExecutor

def greet(name):
    return f"Hello, {name}!"

with ThreadPoolExecutor(max_workers=2) as executor:
    names = ["Alice", "Bob", "Charlie"]
    results = executor.map(greet, names)
    for result in results:
        print(result)
```

### Example 2: Using submit()
Schedule individual tasks and retrieve results.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def compute_square(num):
    time.sleep(1)
    return num * num

with ThreadPoolExecutor(max_workers=3) as executor:
    futures = [executor.submit(compute_square, i) for i in range(1, 5)]
    for future in futures:
        print(f"Square: {future.result()}")
```

### Example 3: Handling Exceptions
Capture exceptions in tasks.

```python
from concurrent.futures import ThreadPoolExecutor

def risky_task(x):
    if x == 0:
        raise ValueError("Cannot process zero!")
    return 10 / x

with ThreadPoolExecutor(max_workers=2) as executor:
    futures = [executor.submit(risky_task, i) for i in [2, 0, 5]]
    for future in futures:
        try:
            print(f"Result: {future.result()}")
        except Exception as e:
            print(f"Error: {e}")
```

### Example 4: Simulating I/O-Bound Task
Simulate downloading files concurrently.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def download_file(file_name):
    time.sleep(2)
    return f"Downloaded {file_name}"

files = ["file1.txt", "file2.txt", "file3.txt"]
with ThreadPoolExecutor(max_workers=2) as executor:
    results = executor.map(download_file, files)
    for result in results:
        print(result)
```

### Example 5: Combining map() and submit()
Mix `map()` and `submit()` for different task types.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def process_data(data):
    time.sleep(1)
    return f"Processed {data}"

def log_completion():
    return "All tasks completed"

with ThreadPoolExecutor(max_workers=3) as executor:
    data = ["X", "Y", "Z"]
    results = executor.map(process_data, data)
    log_future = executor.submit(log_completion)
    
    for result in results:
        print(result)
    print(log_future.result())
```

### Example 6: Controlling max_workers
Demonstrate the effect of different `max_workers` values.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def task(n):
    time.sleep(1)
    return f"Task {n} done"

for workers in [1, 3]:
    print(f"\nUsing {workers} workers:")
    start = time.time()
    with ThreadPoolExecutor(max_workers=workers) as executor:
        results = executor.map(task, range(5))
        for result in results:
            print(result)
    print(f"Time taken: {time.time() - start:.2f} seconds")
```

### Example 7: Collecting Results in Order
Ensure results are processed in the order tasks were submitted.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def process_item(item):
    time.sleep(1)
    return f"Item {item} processed"

items = ["Apple", "Banana", "Orange"]
with ThreadPoolExecutor(max_workers=2) as executor:
    futures = [executor.submit(process_item, item) for item in items]
    for future, item in zip(futures, items):
        print(f"{item}: {future.result()}")
```

### Example 8: Cancelling Tasks
Cancel pending tasks in the pool.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def long_task(n):
    time.sleep(5)
    return f"Task {n} completed"

with ThreadPoolExecutor(max_workers=2) as executor:
    futures = [executor.submit(long_task, i) for i in range(4)]
    time.sleep(1)
    for future in futures:
        if future.running():
            print(f"Task running, cannot cancel")
        else:
            future.cancel()
            print(f"Task cancelled: {future.cancelled()}")
    for future in futures:
        if not future.cancelled():
            try:
                print(future.result())
            except Exception as e:
                print(f"Error: {e}")
```

### Example 9: Using as_completed()
Process results as tasks complete.

```python
from concurrent.futures import ThreadPoolExecutor
from concurrent.futures import as_completed
import time

def process_task(n):
    time.sleep(n)
    return f"Task {n} done"

with ThreadPoolExecutor(max_workers=3) as executor:
    futures = [executor.submit(process_task, i) for i in [3, 1, 2]]
    for future in as_completed(futures):
        print(future.result())
```

### Example 10: Parallel URL Fetching
Fetch multiple URLs concurrently (simulated).

```python
from concurrent.futures import ThreadPoolExecutor
import time

def fetch_url(url):
    time.sleep(2)  # Simulate network delay
    return f"Fetched {url}"

urls = ["http://example.com", "http://test.com", "http://demo.com"]
with ThreadPoolExecutor(max_workers=2) as executor:
    results = executor.map(fetch_url, urls)
    for url, result in zip(urls, results):
        print(f"{url}: {result}")
```

---

## Best Practices
- Use `ThreadPoolExecutor` for I/O-bound tasks, not CPU-bound tasks (use `ProcessPoolExecutor` for CPU-bound tasks).
- Set `max_workers` based on the task type and system resources (e.g., 2–4 for network tasks).
- Always use the context manager (`with` statement) to ensure proper cleanup.
- Handle exceptions in `Future` objects or via `try-except` with `map()`.
- Use `as_completed()` for tasks where order of completion matters.

## Exercises for Students
1. Modify Example 4 to use `submit()` instead of `map()` and compare the code.
2. Create a program to process 10 text files concurrently using `ThreadPoolExecutor`.
3. Simulate a web scraper that fetches 5 URLs with random delays and prints results as they complete.
4. Write a program to compute squares of 100 numbers, experimenting with different `max_workers` values.

