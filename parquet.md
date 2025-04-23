# Parquet 

Parquet is an open-source, column-oriented data storage file format optimized  
for big data processing. It provides efficient data compression, advanced encoding schemes,  
and high performance for columnar data storage and retrieval, making it ideal for analytics  
and data warehousing. Parquet supports complex data processing operations and is widely used  
in distributed systems like Apache Hadoop, Spark, and Hive.


PyArrow is an open-source Python library that provides a high-performance interface for  
working with Apache Arrow, an in-memory columnar data format. It enables efficient data  
processing, storage, and interchange for large datasets, with support for advanced features  
like zero-copy access, vectorized operations, and integration with big data tools.  
PyArrow is commonly used for handling Parquet files, Arrow tables, and data pipelines in  
frameworks like Pandas, Apache Spark, and Dask.

Key features include:
- Reading/writing Parquet, CSV, and other file formats.
- In-memory data manipulation with Arrow’s columnar format.
- Integration with Python data science ecosystems.
- Support for compression, partitioning, and streaming data.

It’s a core dependency for many data processing tasks in Python, especially when performance and  
scalability are critical.

Below are a few examples demonstrating how to work with PyArrow in Python, focusing on creating, manipulating,  
and reading/writing data in Apache Arrow and Parquet formats.  

These examples assume you have PyArrow installed (`pip install pyarrow`).

```python
import pyarrow as pa
import pyarrow.parquet as pq
import pyarrow.compute as pc
import pandas as pd

# Example 1: Creating an Arrow Table and Writing to Parquet
def example1_create_and_write_parquet():
    # Create arrays for the table
    names = pa.array(['Alice', 'Bob', 'Charlie'])
    ages = pa.array([25, 30, 35])
    cities = pa.array(['New York', 'Los Angeles', 'Chicago'])

    # Create a table
    table = pa.Table.from_arrays([names, ages, cities], names=['name', 'age', 'city'])

    # Write the table to a Parquet file
    pq.write_table(table, 'example1.parquet', compression='snappy')
    print("Example 1: Created and wrote table to 'example1.parquet'")

# Example 2: Reading a Parquet File and Filtering Data
def example2_read_and_filter():
    # Read the Parquet file
    table = pq.read_table('example1.parquet')

    # Filter rows where age > 28
    mask = pc.greater(table['age'], 28)
    filtered_table = table.filter(mask)

    # Convert to Pandas DataFrame for display
    df = filtered_table.to_pandas()
    print("Example 2: Filtered table (age > 28):")
    print(df)

# Example 3: Converting Pandas DataFrame to Arrow and Back
def example3_pandas_conversion():
    # Create a Pandas DataFrame
    df = pd.DataFrame({
        'name': ['David', 'Eve', 'Frank'],
        'score': [85.5, 90.0, 92.3]
    })

    # Convert to Arrow Table
    table = pa.Table.from_pandas(df)

    # Perform an operation (e.g., add 5 to scores)
    new_scores = pc.add(table['score'], 5.0)
    updated_table = table.set_column(1, 'score', new_scores)

    # Convert back to Pandas
    updated_df = updated_table.to_pandas()
    print("Example 3: Updated DataFrame (scores + 5):")
    print(updated_df)

# Example 4: Working with Chunked Arrays and Large Data
def example4_chunked_arrays():
    # Create a large chunked array
    chunk1 = pa.array([1, 2, 3, 4, 5])
    chunk2 = pa.array([6, 7, 8, 9, 10])
    chunked_array = pa.chunked_array([chunk1, chunk2])

    # Create a table with chunked array
    table = pa.Table.from_arrays([chunked_array], names=['numbers'])

    # Compute sum of all values
    total_sum = pc.sum(table['numbers']).as_py()
    print("Example 4: Sum of chunked array:", total_sum)

# Run all examples
if __name__ == "__main__":
    example1_create_and_write_parquet()
    example2_read_and_filter()
    example3_pandas_conversion()
    example4_chunked_arrays()
```

### Explanation of Examples
1. **Creating and Writing to Parquet**: Builds an Arrow Table from arrays and writes it to a Parquet file with `snappy` compression.
2. **Reading and Filtering**: Reads a Parquet file and filters rows based on a condition (`age > 28`) using PyArrow’s compute functions.
3. **Pandas Conversion**: Converts a Pandas DataFrame to an Arrow Table, performs an operation (adds 5 to scores), and converts back to Pandas.
4. **Chunked Arrays**: Demonstrates handling large datasets with chunked arrays, computing the sum of values across chunks.

### Notes
- **Dependencies**: Ensure `pyarrow` and `pandas` are installed.
- **Performance**: PyArrow is optimized for in-memory columnar data, making it faster than Pandas for large datasets.
- **Compression**: The `snappy` compression is used for a balance of speed and size; alternatives include `gzip` or `zstd`.
- **Use Cases**: These examples are useful for data pipelines, ETL processes, or integrating with big data tools like Spark.


Here are a few examples of working with Parquet files in Python using the `pandas` and `pyarrow` 
libraries, which are commonly used for Parquet operations:

### Prerequisites
Install required libraries:
```bash
pip install pandas pyarrow
```

### Example 1: Writing a DataFrame to a Parquet File
```python
import pandas as pd

# Create a sample DataFrame
data = {
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35],
    'city': ['New York', 'Los Angeles', 'Chicago']
}
df = pd.DataFrame(data)

# Write DataFrame to a Parquet file
df.to_parquet('sample.parquet', engine='pyarrow', index=False)
print("DataFrame written to sample.parquet")
```

### Example 2: Reading a Parquet File
```python
import pandas as pd

# Read a Parquet file into a DataFrame
df = pd.read_parquet('sample.parquet', engine='pyarrow')
print("DataFrame from Parquet file:")
print(df)
```

### Example 3: Filtering Columns When Reading
```python
import pandas as pd

# Read only specific columns from the Parquet file
df = pd.read_parquet('sample.parquet', engine='pyarrow', columns=['name', 'age'])
print("Filtered DataFrame (name and age only):")
print(df)
```

### Example 4: Writing with Compression and Partitioning
```python
import pandas as pd

# Create a larger DataFrame
data = {
    'name': ['Alice', 'Bob', 'Charlie', 'David'],
    'age': [25, 30, 35, 40],
    'city': ['New York', 'Los Angeles', 'Chicago', 'New York']
}
df = pd.DataFrame(data)

# Write to Parquet with compression and partitioning by city
df.to_parquet('partitioned_data', engine='pyarrow', partition_cols=['city'], compression='snappy')
print("DataFrame written to partitioned Parquet files")
```

### Example 5: Using PyArrow Directly for Advanced Operations
```python
import pyarrow.parquet as pq
import pyarrow as pa

# Create a PyArrow Table
data = {
    'name': ['Alice', 'Bob', 'Charlie'],
    'age': [25, 30, 35]
}
table = pa.Table.from_pandas(pd.DataFrame(data))

# Write to a Parquet file
pq.write_table(table, 'sample_arrow.parquet')

# Read and filter rows using PyArrow
parquet_file = pq.ParquetFile('sample_arrow.parquet')
table_read = parquet_file.read()
filtered_table = table_read.filter(pa.compute.field('age') > 28)
print("Filtered PyArrow Table (age > 28):")
print(filtered_table.to_pandas())
```

### Notes
- **Engines**: `pyarrow` is used here, but `fastparquet` is another engine option for `pandas`.
- **Compression**: Common compression options include `snappy` (default), `gzip`, and `zstd`.
- **Partitioning**: Useful for large datasets, allowing data to be split into subdirectories based on column values (e.g., `city=New York`).
- **PyArrow**: Offers more control for advanced use cases, like filtering or working with large datasets that don’t fit in memory.

These examples cover basic read/write operations, filtering, partitioning, and advanced usage with PyArrow. Let me know if you need more specific use cases!


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
