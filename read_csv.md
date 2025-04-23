# Read CSV using pandas vs csv.reader vs dask

Below is a Python example comparing three methods for reading a large CSV file: (1) using `pandas.read_csv()`  
to load the entire file into memory, (2) using `csv.reader` to process the file line-by-line,  
and (3) using `dask.dataframe.read_csv()` to process the file in chunks. The script generates  
a large CSV file (~500 MB), benchmarks execution time and memory usage for each method, and outputs  
the results for comparison.  

### Prerequisites

Install required libraries:

```bash
pip install pandas dask memory_profiler psutil
```

### Example Script
This script:
1. Generates a ~500 MB CSV file with sample data.
2. Implements three functions to read the CSV file using `pandas`, `csv.reader`, and `dask`.
3. Measures execution time and peak memory usage using `memory_profiler` and `time`.
4. Outputs the number of rows processed, time taken, and memory used for each method.
5. Cleans up the generated file.

```python
import time
import os
import csv
import pandas as pd
import dask.dataframe as dd
from memory_profiler import memory_usage
import psutil

# Function to generate a large CSV file
def generate_large_csv(filename, size_mb=500):
    # Define sample data structure
    headers = ['id', 'name', 'age', 'city']
    sample_row = [0, 'Alice', 25, 'New York']
    target_size = size_mb * 1024 * 1024  # Convert MB to bytes
    
    with open(filename, 'w', newline='') as f:
        writer = csv.writer(f)
        writer.writerow(headers)  # Write header
        current_size = len(','.join(headers) + '\n')
        row_count = 0
        
        while current_size < target_size:
            sample_row[0] = row_count  # Increment ID
            writer.writerow(sample_row)
            current_size += len(','.join(map(str, sample_row)) + '\n')
            row_count += 1
    
    file_size_mb = os.path.getsize(filename) / (1024 * 1024)
    print(f"Generated CSV file '{filename}' with {row_count} rows (~{file_size_mb:.2f} MB)")

# Method 1: Using pandas.read_csv()
def read_with_pandas(filename):
    df = pd.read_csv(filename)
    return len(df)  # Return number of rows

# Method 2: Using csv.reader line-by-line
def read_with_csv_reader(filename):
    row_count = 0
    with open(filename, 'r') as f:
        reader = csv.reader(f)
        next(reader)  # Skip header
        for row in reader:
            row_count += 1
    return row_count

# Method 3: Using dask.dataframe.read_csv()
def read_with_dask(filename):
    ddf = dd.read_csv(filename)
    row_count = len(ddf)  # Compute row count (forces computation)
    return row_count

# Function to measure time and memory
def benchmark_function(func, filename):
    # Measure memory usage
    mem_usage = memory_usage((func, (filename,)), interval=0.1)
    peak_memory = max(mem_usage)  # Peak memory in MB

    # Measure execution time
    start_time = time.time()
    result = func(filename)
    end_time = time.time()
    execution_time = end_time - start_time

    return result, execution_time, peak_memory

# Main execution
if __name__ == "__main__":
    filename = "large_data.csv"
    
    # Generate a large CSV file
    generate_large_csv(filename, size_mb=500)
    
    # Benchmark pandas.read_csv()
    print("\nBenchmarking pandas.read_csv():")
    result_pandas, time_pandas, mem_pandas = benchmark_function(read_with_pandas, filename)
    print(f"Rows processed: {result_pandas}")
    print(f"Execution time: {time_pandas:.2f} seconds")
    print(f"Peak memory usage: {mem_pandas:.2f} MB")
    
    # Benchmark csv.reader
    print("\nBenchmarking csv.reader:")
    result_csv, time_csv, mem_csv = benchmark_function(read_with_csv_reader, filename)
    print(f"Rows processed: {result_csv}")
    print(f"Execution time: {time_csv:.2f} seconds")
    print(f"Peak memory usage: {mem_csv:.2f} MB")
    
    # Benchmark dask.dataframe.read_csv()
    print("\nBenchmarking dask.dataframe.read_csv():")
    result_dask, time_dask, mem_dask = benchmark_function(read_with_dask, filename)
    print(f"Rows processed: {result_dask}")
    print(f"Execution time: {time_dask:.2f} seconds")
    print(f"Peak memory usage: {mem_dask:.2f} MB")
    
    # Clean up
    os.remove(filename)
    print(f"\nCleaned up: Removed '{filename}'")
```

### Explanation
1. **File Generation**: The `generate_large_csv` function creates a ~500 MB CSV file with columns `id`, `name`, `age`, and `city`. The file size is approximate, based on the number of rows written until the target size is reached.
2. **Reading Methods**:
   - `read_with_pandas`: Uses `pandas.read_csv()` to load the entire file into a DataFrame, which is memory-intensive but convenient.
   - `read_with_csv_reader`: Uses `csv.reader` to process the file line-by-line, minimizing memory usage but requiring manual parsing.
   - `read_with_dask`: Uses `dask.dataframe.read_csv()` to read the file in chunks, suitable for out-of-core processing of very large files.
3. **Benchmarking**:
   - **Time**: Measured using `time.time()` to capture the duration of each function.
   - **Memory**: Measured using `memory_profiler.memory_usage` to track peak memory consumption in MB.
4. **Output**: Displays the number of rows processed, execution time, and peak memory usage for each method.
5. **Cleanup**: Deletes the generated CSV file to avoid clutter.

### Expected Results
- **Memory Usage**:
  - `pandas.read_csv()`: High memory usage (e.g., 1–2 GB for a 500 MB file) since it loads the entire file into a DataFrame.
  - `csv.reader`: Very low memory usage (e.g., <100 MB) as it processes one row at a time.
  - `dask.dataframe.read_csv()`: Moderate memory usage (e.g., 200–500 MB), depending on chunk size, as it processes data in chunks.
- **Execution Time**:
  - `pandas`: Fast for smaller files but scales poorly with file size due to memory overhead.
  - `csv.reader`: Slower due to Python-level row iteration but consistent regardless of file size.
  - `dask`: May have overhead for smaller files but excels with very large files due to parallel processing.

Sample output (on a typical system with a 500 MB CSV file):
```
Generated CSV file 'large_data.csv' with 12500000 rows (~500.12 MB)

Benchmarking pandas.read_csv():
Rows processed: 12500000
Execution time: 8.25 seconds
Peak memory usage: 1800.45 MB

Benchmarking csv.reader:
Rows processed: 12500000
Execution time: 12.60 seconds
Peak memory usage: 75.20 MB

Benchmarking dask.dataframe.read_csv():
Rows processed: 12500000
Execution time: 10.15 seconds
Peak memory usage: 350.30 MB

Cleaned up: Removed 'large_data.csv'
```

### Notes

- **File Size**: Adjust `size_mb` in `generate_large_csv` to test with different file sizes (e.g., 100 MB or 1 GB) to highlight scalability differences.
- **System Variability**: Results depend on hardware (CPU, RAM, disk speed), OS, and Python version. SSDs will improve I/O performance.
- **Use Cases**:
  - Use `pandas` for small-to-medium files or when you need DataFrame functionality.
  - Use `csv.reader` for memory-constrained environments or when manual parsing is acceptable.
  - Use `dask` for very large files that don’t fit in memory or for distributed computing.
- **Dependencies**: Requires `pandas`, `dask`, `memory_profiler`, and `psutil`.
- **Dask Configuration**: Dask’s performance can be tuned by adjusting chunk size or enabling multiprocessing (e.g., `npartitions`).
- **Scalability**: For even larger files, `dask` will likely outperform `pandas`, and `csv.reader` will remain memory-efficient but slow.

Run the script to see the benchmarks on your system. If you want to modify the example (e.g., add specific CSV processing tasks, change file size, or include additional metrics), let me know!
