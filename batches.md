# Batches


Batch processing is a common technique used to handle large datasets by dividing them into  
smaller, manageable chunks to optimize performance and memory usage. Here's a practical  
example of batch processing where we read a very large dataset and process it in batches  
to calculate the total sum of numbers:

### Example: Processing a Large File in Batches

```python
import time

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

# Benchmark batch processing
start_time = time.time()
total = process_file_in_batches(file_name)
end_time = time.time()

print(f"Total sum of numbers: {total}")
print(f"Time taken for batch processing: {end_time - start_time:.4f} seconds")
```

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

This approach balances memory and processing speed, and you can customize the batch size based on the available  
system resources or file size. 
