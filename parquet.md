# Parquet 




## Example

Example that compares the efficiency of storing and reading data in **CSV** versus **Parquet** formats.  
This example highlights differences in **file size** and **read performance** when working with a large dataset.  

### Example Code:

```python
import pandas as pd
import time
import os

# Generate a large DataFrame
rows = 1_000_000
data = {
    "ID": range(1, rows + 1),
    "Name": [f"Name_{i}" for i in range(1, rows + 1)],
    "Age": [i % 100 for i in range(1, rows + 1)],
    "City": [f"City_{i % 100}" for i in range(1, rows + 1)]
}
df = pd.DataFrame(data)

# File paths
csv_file = "data.csv"
parquet_file = "data.parquet"

# Save to CSV
csv_start_time = time.time()
df.to_csv(csv_file, index=False)
csv_end_time = time.time()

# Save to Parquet
parquet_start_time = time.time()
df.to_parquet(parquet_file, index=False)
parquet_end_time = time.time()

# Measure file sizes
csv_size = os.path.getsize(csv_file)
parquet_size = os.path.getsize(parquet_file)

# Read CSV
csv_read_start = time.time()
csv_data = pd.read_csv(csv_file)
csv_read_end = time.time()

# Read Parquet
parquet_read_start = time.time()
parquet_data = pd.read_parquet(parquet_file)
parquet_read_end = time.time()

# Print results
print("Storage Comparison:")
print(f"CSV file size: {csv_size / (1024 * 1024):.2f} MB")
print(f"Parquet file size: {parquet_size / (1024 * 1024):.2f} MB")

print("\nWrite Time Comparison:")
print(f"Time to write CSV: {csv_end_time - csv_start_time:.2f} seconds")
print(f"Time to write Parquet: {parquet_end_time - parquet_start_time:.2f} seconds")

print("\nRead Time Comparison:")
print(f"Time to read CSV: {csv_read_end - csv_read_start:.2f} seconds")
print(f"Time to read Parquet: {parquet_read_end - parquet_read_start:.2f} seconds")
```

### What This Code Does:
1. **Generates a Large Dataset**: Creates a large `DataFrame` with 1,000,000 rows for benchmarking.
2. **Saves Data**: Saves the dataset in both CSV and Parquet formats.
3. **Measures File Sizes**: Compares the storage size of the two formats.
4. **Measures Read/Write Times**: Tracks the time taken to save and load the data using both formats.
5. **Performance Insights**: Outputs results that reveal the differences between CSV and Parquet.

### Key Insights:
1. **File Size**:
   - **CSV**: A plain-text format that stores data without compression, resulting in larger file sizes.
   - **Parquet**: A columnar storage format with built-in compression, significantly reducing the file size.

2. **Write/Read Speeds**:
   - **CSV**: Slower due to its lack of optimization and larger file size.
   - **Parquet**: Faster read and write operations because it is optimized for large-scale data processing.

3. **Use Case Considerations**:
   - Use **CSV** for simple data storage or compatibility with tools that don't support Parquet.
   - Use **Parquet** when working with large datasets where storage efficiency and processing speed are important (e.g., big data applications).

This example demonstrates the practical benefits of using Parquet over CSV for large-scale data storage and processing.  
Let me know if you want to dive deeper into any aspect! 🚀
