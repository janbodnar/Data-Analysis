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

Example with thread IDs:

```python
import threading

def print_numbers():
    # Get the current thread ID
    thread_id = threading.get_ident()
    for i in range(5):
        print(f"Thread ID: {thread_id}, Number: {i}")

# Create and start the thread
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

### Example 6: TKinter long running task

Run a long running task with and without a thread. 

```python
import tkinter as tk
from tkinter import messagebox
import time
import threading

# Example 1: Long-Running Task in Main Thread (GUI Freezes)
def example_without_thread():
    root = tk.Tk()
    root.title("Tkinter Without Thread")
    root.geometry("300x150")

    def long_running_task():
        status_label.config(text="Task Running...")
        root.update()  # Force update to show status
        for i in range(5):
            time.sleep(3)  # Simulate long task
            status_label.config(text=f"Processing {i+1}/5")
            root.update()
        status_label.config(text="Task Completed!")
        messagebox.showinfo("Done", "Task finished!")

    def start_task():
        start_button.config(state="disabled")
        long_running_task()
        start_button.config(state="normal")

    start_button = tk.Button(root, text="Start Task", command=start_task)
    start_button.pack(pady=20)

    status_label = tk.Label(root, text="Ready")
    status_label.pack(pady=10)

    tk.Label(root, text="Note: GUI freezes during task").pack(pady=10)

    root.mainloop()

# Example 2: Long-Running Task in Separate Thread (GUI Responsive)
def example_with_thread():
    root = tk.Tk()
    root.title("Tkinter With Thread")
    root.geometry("300x150")

    def long_running_task():
        for i in range(5):
            time.sleep(3)  # Simulate long task
            status_label.config(text=f"Processing {i+1}/5")
        status_label.config(text="Task Completed!")
        start_button.config(state="normal")
        messagebox.showinfo("Done", "Task finished!")

    def start_task():
        start_button.config(state="disabled")
        status_label.config(text="Task Running...")
        thread = threading.Thread(target=long_running_task)
        thread.start()

    start_button = tk.Button(root, text="Start Task", command=start_task)
    start_button.pack(pady=20)

    status_label = tk.Label(root, text="Ready")
    status_label.pack(pady=10)

    tk.Label(root, text="Note: GUI remains responsive").pack(pady=10)

    root.mainloop()

# Run both examples (one at a time)
if __name__ == "__main__":
    print("Running Example 1 (Without Thread)...")
    example_without_thread()
    print("Running Example 2 (With Thread)...")
    example_with_thread()
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

## Example 11: Queue

```python
import threading
import time

# Function to simulate work
def simulate_work(task_id):
    thread_id = threading.get_ident()
    print(f"Thread ID: {thread_id}, Task ID: {task_id} started.")
    time.sleep(5)  # Simulate work with 5-second sleep
    print(f"Thread ID: {thread_id}, Task ID: {task_id} completed.")

# List of 20 tasks
tasks = list(range(1, 21))

# Worker function to process tasks
def worker(task_queue):
    while not task_queue.empty():
        try:
            task_id = task_queue.get_nowait()  # Get next task from the queue
            simulate_work(task_id)
        except Exception as e:
            pass  # Handle empty queue case gracefully

# Main function to manage threads
def main():
    from queue import Queue

    # Create a queue for the tasks
    task_queue = Queue()
    for task in tasks:
        task_queue.put(task)

    # Start timing
    start_time = time.time()

    # Create and start 5 threads
    threads = []
    for i in range(5):
        thread = threading.Thread(target=worker, args=(task_queue,))
        threads.append(thread)
        thread.start()

    # Wait for all threads to complete
    for thread in threads:
        thread.join()

    # End timing
    end_time = time.time()
    print(f"All tasks completed in {end_time - start_time:.2f} seconds.")

# Run the program
if __name__ == "__main__":
    main()
```

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


