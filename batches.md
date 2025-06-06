# Batches


Batch processing is a common technique used to handle large datasets by dividing them into  
smaller, manageable chunks to optimize performance and memory usage. Here's a practical  
example of batch processing where we read a very large dataset and process it in batches  
to calculate the total sum of numbers:

### Example: Processing a Large File in Batches

```python
import time
import sys

# Simulate a large file with numbers
file_name = "large_numbers.txt"
with open(file_name, "w") as f:
    for i in range(1, 1_000_001):  # 1 million numbers
        f.write(f"{i}\n")

# Function to process data in batches
def process_file_in_batches(file_path, batch_size=10000):
    total_sum = 0
    with open(file_path, "r") as f:
        batch = []
        for line in f:
            batch.append(int(line.strip()))
            if len(batch) == batch_size:  # Process the batch when full
                total_sum += sum(batch)
                batch = []  # Clear the batch for the next chunk
        # Process any remaining items in the last batch
        if batch:
            total_sum += sum(batch)
    return total_sum

# Function to process the entire file without batching
def process_file_without_batches(file_path):
    with open(file_path, "r") as f:
        numbers = [int(line.strip()) for line in f]  # Load all numbers into memory
    return sum(numbers)

# Measure memory usage for batch processing
def measure_batch_memory(file_path, batch_size=10000):
    with open(file_path, "r") as f:
        batch = []
        for line in f:
            batch.append(int(line.strip()))
            if len(batch) == batch_size:
                break  # Measure memory at this point
        return sys.getsizeof(batch)

# Measure memory usage for non-batch processing
def measure_non_batch_memory(file_path):
    with open(file_path, "r") as f:
        numbers = [int(line.strip()) for line in f]  # Load all numbers into memory
        return sys.getsizeof(numbers)

# Benchmark batch processing
start_batch = time.time()
total_batch = process_file_in_batches(file_name)
end_batch = time.time()

# Benchmark non-batch processing
start_non_batch = time.time()
total_non_batch = process_file_without_batches(file_name)
end_non_batch = time.time()

# Memory usage comparison
batch_memory = measure_batch_memory(file_name)
non_batch_memory = measure_non_batch_memory(file_name)

# Print results
print("Processing Results:")
print(f"Total sum using batch processing: {total_batch}")
print(f"Total sum using non-batch processing: {total_non_batch}")

print("\nTime Comparison:")
print(f"Time taken using batch processing: {end_batch - start_batch:.4f} seconds")
print(f"Time taken using non-batch processing: {end_non_batch - start_non_batch:.4f} seconds")

print("\nMemory Comparison:")
print(f"Memory used by batch processing: {batch_memory} bytes")
print(f"Memory used by non-batch processing: {non_batch_memory} bytes")
```

Here’s the modified example that includes memory usage comparison alongside processing times for batch and non-batch approaches:

### Code Example:
```python
import time
import sys

# Simulate a large file with numbers
file_name = "large_numbers.txt"
with open(file_name, "w") as f:
    for i in range(1, 1_000_001):  # 1 million numbers
        f.write(f"{i}\n")

# Function to process data in batches
def process_file_in_batches(file_path, batch_size=10000):
    total_sum = 0
    with open(file_path, "r") as f:
        batch = []
        for line in f:
            batch.append(int(line.strip()))
            if len(batch) == batch_size:  # Process the batch when full
                total_sum += sum(batch)
                batch = []  # Clear the batch for the next chunk
        # Process any remaining items in the last batch
        if batch:
            total_sum += sum(batch)
    return total_sum

# Function to process the entire file without batching
def process_file_without_batches(file_path):
    with open(file_path, "r") as f:
        numbers = [int(line.strip()) for line in f]  # Load all numbers into memory
    return sum(numbers)

# Measure memory usage for batch processing
def measure_batch_memory(file_path, batch_size=10000):
    with open(file_path, "r") as f:
        batch = []
        for line in f:
            batch.append(int(line.strip()))
            if len(batch) == batch_size:
                break  # Measure memory at this point
        return sys.getsizeof(batch)

# Measure memory usage for non-batch processing
def measure_non_batch_memory(file_path):
    with open(file_path, "r") as f:
        numbers = [int(line.strip()) for line in f]  # Load all numbers into memory
        return sys.getsizeof(numbers)

# Benchmark batch processing
start_batch = time.time()
total_batch = process_file_in_batches(file_name)
end_batch = time.time()

# Benchmark non-batch processing
start_non_batch = time.time()
total_non_batch = process_file_without_batches(file_name)
end_non_batch = time.time()

# Memory usage comparison
batch_memory = measure_batch_memory(file_name)
non_batch_memory = measure_non_batch_memory(file_name)

# Print results
print("Processing Results:")
print(f"Total sum using batch processing: {total_batch}")
print(f"Total sum using non-batch processing: {total_non_batch}")

print("\nTime Comparison:")
print(f"Time taken using batch processing: {end_batch - start_batch:.4f} seconds")
print(f"Time taken using non-batch processing: {end_non_batch - start_non_batch:.4f} seconds")

print("\nMemory Comparison:")
print(f"Memory used by batch processing: {batch_memory} bytes")
print(f"Memory used by non-batch processing: {non_batch_memory} bytes")
```

### Explanation:

1. **Batch Processing**:
   - Processes the file incrementally in chunks, keeping memory usage low. Memory usage is
     measured by examining the batch size when it reaches its limit.
   - Useful for large datasets where memory constraints are critical.

2. **Non-Batch Processing**:
   - Loads the entire file into memory, which significantly increases memory consumption.
      Memory usage is measured after all numbers are loaded into a list.
   - Can be faster for small-to-medium files but becomes inefficient for very large datasets.

3. **Memory Usage Measurement**:
   - `sys.getsizeof()` is used to measure the memory footprint of data structures (batch or list).

### Expected Outcome:
- **Batch Processing**:
  - Lower memory usage due to processing smaller chunks.
  - Slightly slower due to incremental processing.

- **Non-Batch Processing**:
  - Higher memory usage as all data is loaded into memory.
  - Typically faster for smaller datasets, but not scalable for massive files.

This example highlights the trade-offs between memory efficiency and speed, making it ideal for practical  
scenarios where resource optimization is required.


### Key Details:

1. **Batch Size**:
   - The data is processed in batches of `10000` lines at a time. This keeps memory usage low by
     avoiding the need to load the entire file into memory.

2. **Processing Logic**:
   - Each batch is summed, and the result is added to the `total_sum`. Once processed, the batch is
     cleared to make room for the next chunk of data.

3. **Efficiency**:
   - By processing data in chunks, this method ensures that memory usage is predictable and efficient,
     making it ideal for large datasets.

### Practical Applications:
- **ETL Pipelines**: Extract, transform, and load large datasets into databases in chunks.
- **File Conversion**: Convert large text files into other formats (e.g., CSV, JSON) in manageable chunks.
- **Data Aggregation**: Aggregate data (e.g., sum, average) from logs or reports without overwhelming memory resources.

