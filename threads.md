# Introduction to Python Threads

## What are Threads?

Threads are lightweight processes that allow multiple tasks to run concurrently within a single program.  
In Python, threads share the same memory space, making them efficient for I/O-bound tasks  
(e.g., file operations, network requests) but less effective for CPU-bound tasks  
due to the Global Interpreter Lock (GIL).

## Why Use Threads?
- Improve responsiveness in applications (e.g., GUI apps).
- Handle I/O-bound tasks efficiently.
- Run multiple tasks simultaneously without needing separate processes.

## Python Threading Module
Python’s `threading` module provides tools to create and manage threads. Key components:
- `threading.Thread`: Creates a new thread.
- `start()`: Begins thread execution.
- `join()`: Waits for a thread to complete.
- `Lock`: Prevents multiple threads from accessing shared resources simultaneously.

## Basic Thread Example
Here’s a simple example to get started:

```python
import threading

def print_numbers():
    for i in range(5):
        print(f"Number: {i}")

thread = threading.Thread(target=print_numbers)
thread.start()
thread.join()
```

## Important Notes
- **GIL Limitation**: Python’s GIL allows only one thread to execute Python bytecode at a time, limiting parallelism for CPU-bound tasks.
- **Thread Safety**: Use locks to protect shared resources.
- **Use Cases**: Best for I/O-bound tasks like downloading files or reading from sockets.

---

## 10 Example Programs

### Example 1: Basic Thread Creation
Run two threads that print different messages.

```python
import threading

def print_hello():
    for _ in range(3):
        print("Hello from thread!")

def print_world():
    for _ in range(3):
        print("World from thread!")

t1 = threading.Thread(target=print_hello)
t2 = threading.Thread(target=print_world)

t1.start()
t2.start()

t1.join()
t2.join()
```

### Example 2: Thread with Arguments
Pass arguments to a thread function.

```python
import threading

def greet(name):
    print(f"Hello, {name}!")

t1 = threading.Thread(target=greet, args=("Alice",))
t2 = threading.Thread(target=greet, args=("Bob",))

t1.start()
t2.start()

t1.join()
t2.join()
```

### Example 3: Using a Lock
Protect a shared resource with a lock.

```python
import threading

counter = 0
lock = threading.Lock()

def increment():
    global counter
    for _ in range(100000):
        with lock:
            counter += 1

t1 = threading.Thread(target=increment)
t2 = threading.Thread(target=increment)

t1.start()
t2.start()

t1.join()
t2.join()

print(f"Final counter: {counter}")
```

### Example 4: Thread with Return Value
Store thread results using a shared list.

```python
import threading

results = []

def compute_square(num):
    results.append(num * num)

threads = [threading.Thread(target=compute_square, args=(i,)) for i in range(1, 5)]

for t in threads:
    t.start()

for t in threads:
    t.join()

print(f"Squares: {results}")
```

### Example 5: Thread Synchronization
Use `join()` to synchronize thread execution.

```python
import threading
import time

def task1():
    print("Task 1 started")
    time.sleep(2)
    print("Task 1 finished")

def task2():
    print("Task 2 started")
    time.sleep(1)
    print("Task 2 finished")

t1 = threading.Thread(target=task1)
t2 = threading.Thread(target=task2)

t1.start()
t1.join()  # Wait for task1 to finish
t2.start()
t2.join()
```

### Example 6: Daemon Threads
Run a background thread that exits when the main program ends.

```python
import threading
import time

def background_task():
    while True:
        print("Background task running...")
        time.sleep(1)

t = threading.Thread(target=background_task, daemon=True)
t.start()

time.sleep(3)
print("Main program exiting")
```

### Example 7: Thread Pool
Use `ThreadPoolExecutor` for managing multiple threads.

```python
from concurrent.futures import ThreadPoolExecutor
import time

def process_item(item):
    time.sleep(1)
    return f"Processed {item}"

with ThreadPoolExecutor(max_workers=3) as executor:
    items = ["Apple", "Banana", "Orange", "Mango"]
    results = executor.map(process_item, items)

print(list(results))
```

### Example 8: Timed Thread Execution
Run a thread for a specific duration.

```python
import threading
import time

def timed_task():
    start = time.time()
    while time.time() - start < 5:
        print("Task running...")
        time.sleep(1)

t = threading.Thread(target=timed_task)
t.start()
t.join()
print("Task completed")
```

### Example 9: Thread with Exception Handling
Handle exceptions in threads.

```python
import threading

def risky_task():
    try:
        raise ValueError("Something went wrong!")
    except Exception as e:
        print(f"Error in thread: {e}")

t = threading.Thread(target=risky_task)
t.start()
t.join()
```

### Example 10: Simulating I/O-Bound Task
Simulate downloading files concurrently.

```python
import threading
import time

def download_file(file_name):
    print(f"Starting download: {file_name}")
    time.sleep(2)  # Simulate I/O delay
    print(f"Finished downloading: {file_name}")

files = ["file1.txt", "file2.txt", "file3.txt"]
threads = [threading.Thread(target=download_file, args=(f,)) for f in files]

for t in threads:
    t.start()

for t in threads:
    t.join()

print("All downloads complete")
```

---

## Best Practices
- Use locks to manage shared resources.  
- Avoid CPU-bound tasks in threads; consider `multiprocessing` instead.  
- Use `ThreadPoolExecutor` for managing multiple threads efficiently.  
- Always handle exceptions in threads to avoid silent failures.  
- Use daemon threads for background tasks that don’t need to complete.  

## Exercises for Students
1. Modify Example 3 to use a `Semaphore` instead of a `Lock`.  
2. Create a program with 5 threads that each print a different message 10 times.  
3. Simulate a bank account with deposits and withdrawals using threads and a lock. 
4. Write a program to download 10 URLs concurrently using threads. 


