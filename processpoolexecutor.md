

# Introduction to Python ProcessPoolExecutor Tutorial

## What is ProcessPoolExecutor?
`ProcessPoolExecutor` is a high-level interface in Python’s `concurrent.futures` module for managing a pool of processes. Unlike threads, processes run in separate memory spaces, making `ProcessPoolExecutor` ideal for CPU-bound tasks (e.g., computations, data processing) as it bypasses Python’s Global Interpreter Lock (GIL).

## Why Use ProcessPoolExecutor?
- Enables true parallelism for CPU-intensive tasks.
- Simplifies process management compared to manually using the `multiprocessing` module.
- Automatically handles process creation, task distribution, and cleanup.
- Efficiently processes large datasets or computationally expensive tasks.

## Key Features
- **Process Pool**: A fixed number of processes execute tasks concurrently.
- **submit()**: Schedules a single task and returns a `Future` object.
- **map()**: Applies a function to an iterable, distributing tasks across processes.
- **Context Manager**: Ensures proper cleanup using `with` statement.
- **max_workers**: Controls the number of processes (defaults to the number of CPU cores).

## Basic Example
Here’s a simple example to get started:

```python
from concurrent.futures import ProcessPoolExecutor

def compute_square(num):
    return num * num

with ProcessPoolExecutor(max_workers=2) as executor:
    numbers = [1, 2, 3, 4]
    results = executor.map(compute_square, numbers)
    for result in results:
        print(f"Square: {result}")
```

## Important Notes
- **Use Case**: Best for CPU-bound tasks like mathematical computations or image processing, not I/O-bound tasks (use `ThreadPoolExecutor` for those).
- **Overhead**: Processes have higher memory and startup overhead than threads, so use them for tasks that benefit from parallelism.
- **Data Serialization**: Arguments and return values are pickled/unpickled, which can be slow for large objects.
- **Process Safety**: No shared memory, so no need for locks, but communication between processes requires explicit mechanisms (e.g., `multiprocessing.Queue`).

---

## 10 Example Programs

### Example 1: Basic Parallel Computation
Compute squares of numbers using `map()`.

```python
from concurrent.futures import ProcessPoolExecutor

def compute_square(num):
    return num * num

with ProcessPoolExecutor(max_workers=2) as executor:
    numbers = [1, 2, 3, 4, 5]
    results = executor.map(compute_square, numbers)
    for num, result in zip(numbers, results):
        print(f"Square of {num}: {result}")
```

### Example 2: Using submit()
Schedule individual tasks and retrieve results.

```python
from concurrent.futures import ProcessPoolExecutor

def compute_cube(num):
    return num ** 3

with ProcessPoolExecutor(max_workers=3) as executor:
    futures = [executor.submit(compute_cube, i) for i in range(1, 5)]
    for future in futures:
        print(f"Cube: {future.result()}")
```

### Example 3: Handling Exceptions
Capture exceptions in processes.

```python
from concurrent.futures import ProcessPoolExecutor

def risky_computation(x):
    if x == 0:
        raise ValueError("Cannot divide by zero!")
    return 100 / x

with ProcessPoolExecutor(max_workers=2) as executor:
    futures = [executor.submit(risky_computation, i) for i in [2, 0, 5]]
    for future in futures:
        try:
            print(f"Result: {future.result()}")
        except Exception as e:
            print(f"Error: {e}")
```

### Example 4: CPU-Intensive Task
Perform a computationally expensive task (prime number check).

```python
from concurrent.futures import ProcessPoolExecutor

def is_prime(n):
    if n < 2:
        return False
    for i in range(2, int(n ** 0.5) + 1):
        if n % i == 0:
            return False
    return True

numbers = [112272535095293, 112582705942171, 112272535095293, 115280095190773]
with ProcessPoolExecutor(max_workers=2) as executor:
    results = executor.map(is_prime, numbers)
    for num, result in zip(numbers, results):
        print(f"{num} is prime: {result}")
```

### Example 5: Processing Large Datasets
Sum squares of chunks of a large list.

```python
from concurrent.futures import ProcessPoolExecutor

def sum_squares(chunk):
    return sum(x * x for x in chunk)

data = list(range(10000))
chunk_size = 2500
chunks = [data[i:i + chunk_size] for i in range(0, len(data), chunk_size)]

with ProcessPoolExecutor(max_workers=4) as executor:
    results = executor.map(sum_squares, chunks)
    total = sum(results)
    print(f"Total sum of squares: {total}")
```

### Example 6: Controlling max_workers
Compare performance with different `max_workers`.

```python
from concurrent.futures import ProcessPoolExecutor
import time

def compute_factorial(n):
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result

numbers = [10000, 15000, 20000]
for workers in [1, 3]:
    print(f"\nUsing {workers} workers:")
    start = time.time()
    with ProcessPoolExecutor(max_workers=workers) as executor:
        results = executor.map(compute_factorial, numbers)
        for num, result in zip(numbers, results):
            print(f"Factorial of {num}: {result}")
    print(f"Time taken: {time.time() - start:.2f} seconds")
```

### Example 7: Using as_completed()
Process results as tasks complete.

```python
from concurrent.futures import ProcessPoolExecutor
from concurrent.futures import as_completed

def compute_fibonacci(n):
    if n <= 1:
        return n
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b

with ProcessPoolExecutor(max_workers=2) as executor:
    futures = [executor.submit(compute_fibonacci, i) for i in [40, 35, 30]]
    for future in as_completed(futures):
        print(f"Fibonacci result: {future.result()}")
```

### Example 8: Matrix Multiplication

Parallelize matrix multiplication.

```python
from concurrent.futures import ProcessPoolExecutor
import numpy as np

def multiply_row(row, matrix):
    return np.dot(row, matrix).tolist()

matrix_a = np.array([[1, 2], [3, 4]])
matrix_b = np.array([[5, 6], [7, 8]])

with ProcessPoolExecutor(max_workers=2) as executor:
    results = executor.map(lambda row: multiply_row(row, matrix_b), matrix_a)
    result_matrix = list(results)
    print("Matrix multiplication result:")
    for row in result_matrix:
        print(row)
```

### Example 9: Image Processing (Simulated)

Simulate applying a filter to image chunks.

```python
from concurrent.futures import ProcessPoolExecutor

def apply_filter(chunk):
    # Simulate heavy computation (e.g., grayscale conversion)
    return sum(x * x for x in chunk)

image_data = [list(range(1000)) for _ in range(4)]  # Simulated image chunks
with ProcessPoolExecutor(max_workers=2) as executor:
    results = executor.map(apply_filter, image_data)
    for i, result in enumerate(results):
        print(f"Processed chunk {i}: {result}")
```

### Example 10: Parallel Sorting

Sort large lists in parallel.

```python
from concurrent.futures import ProcessPoolExecutor
import random

def sort_list(lst):
    return sorted(lst)

lists = [random.sample(range(10000), 5000) for _ in range(4)]
with ProcessPoolExecutor(max_workers=3) as executor:
    results = executor.map(sort_list, lists)
    for i, sorted_list in enumerate(results):
        print(f"Sorted list {i} (first 5 elements): {sorted_list[:5]}")
```

---

## Best Practices
- Use `ProcessPoolExecutor` for CPU-bound tasks, not I/O-bound tasks (use `ThreadPoolExecutor` for those).
- Set `max_workers` to the number of CPU cores (`os.cpu_count()`) or slightly higher for optimal performance.
- Minimize data transfer between processes to reduce pickling/unpickling overhead.
- Always use the context manager (`with` statement) to ensure proper cleanup.
- Handle exceptions in `Future` objects or via `try-except` with `map()`.

## Exercises for Students
1. Modify Example 5 to compute the sum of cubes instead of squares.
2. Create a program to calculate factorials of 10 large numbers using `ProcessPoolExecutor`.
3. Parallelize a program to find prime numbers in a range (e.g., 1 to 100,000).
4. Write a program to perform matrix addition for large matrices using `ProcessPoolExecutor`.

