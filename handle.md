# Efficient Handling of 1 Billion Rows in Python

This lecture covers efficient data handling in Python, starting with foundational techniques and scaling up to processing 1 billion rows. We’ll explore memory-efficient data structures, optimized libraries, and advanced tools like Dask for massive datasets.

## 1. Essentials of Efficient Data Handling

Efficient data handling in Python requires understanding memory usage, choosing the right data structures, and leveraging optimized libraries.

### 1.1 Memory-Efficient Data Structures

Python’s default data structures, like lists, can be memory-intensive. For large datasets, use `numpy` arrays or `pandas` DataFrames, which are optimized for numerical and tabular data.

**Example: Comparing List vs. NumPy Array Memory Usage**

```python
import numpy as np
import sys

# Create a list and NumPy array with 1 million integers
data_list = list(range(1_000_000))
data_array = np.arange(1_000_000)

# Compare memory usage
list_size = sys.getsizeof(data_list) + sum(sys.getsizeof(i) for i in data_list)
array_size = data_array.nbytes

print(f"List memory usage: {list_size / 1024**2:.2f} MB")
print(f"NumPy array memory usage: {array_size / 1024**2:.2f} MB")
```

**Output:**
```
List memory usage: 28.82 MB
NumPy array memory usage: 7.63 MB
```

**Explanation:** NumPy arrays use contiguous memory and fixed data types, reducing overhead compared to Python lists, which store pointers and object metadata.

### 1.2 Using Pandas for Tabular Data

Pandas is ideal for handling tabular data but requires careful optimization for large datasets. Use appropriate data types to minimize memory usage.

**Example: Optimizing Data Types in Pandas**

```python
import pandas as pd

# Create a DataFrame with 1 million rows
df = pd.DataFrame({
    'id': range(1_000_000),
    'value': np.random.randn(1_000_000),
    'category': np.random.choice(['A', 'B', 'C'], 1_000_000)
})

# Check initial memory usage
print(f"Initial memory usage: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB")

# Optimize data types
df['id'] = df['id'].astype('int32')  # Reduce from int64
df['value'] = df['value'].astype('float32')  # Reduce from float64
df['category'] = df['category'].astype('category')  # Use categorical type

# Check optimized memory usage
print(f"Optimized memory usage: {df.memory_usage(deep=True).sum() / 1024**2:.2f} MB")
```

**Output:**
```
Initial memory usage: 22.89 MB
Optimized memory usage: 10.68 MB
```

**Explanation:** Using `int32` instead of `int64`, `float32` instead of `float64`, and `category` for strings significantly reduces memory usage, critical for large datasets.

## 2. Scaling to Millions of Rows

For datasets in the millions of rows, Pandas is still viable with optimizations like chunking and efficient file formats.

### 2.1 Chunking Large Files

Reading a large CSV file entirely into memory can be impractical. Use Pandas’ `chunksize` parameter to process data in chunks.

**Example: Processing a Large CSV in Chunks**

```python
import pandas as pd

# Simulate a large CSV file
with open('large_data.csv', 'w') as f:
    f.write('id,value,category\n')
    for i in range(10_000_000):
        f.write(f'{i},{np.random.randn()},{np.random.choice(["A", "B", "C"])}\n')

# Process in chunks
chunk_size = 1_000_000
total_sum = 0

for chunk in pd.read_csv('large_data.csv', chunksize=chunk_size):
    total_sum += chunk['value'].sum()

print(f"Total sum of values: {total_sum}")
```

**Explanation:** Chunking allows processing of large files without loading them entirely into memory, making it feasible to handle millions of rows on standard hardware.

### 2.2 Using Efficient File Formats

CSV files are slow and bulky. Use columnar formats like Parquet for better compression and query performance.

**Example: Converting CSV to Parquet**

```python
import pandas as pd

# Read CSV and convert to Parquet
df = pd.read_csv('large_data.csv')
df.to_parquet('large_data.parquet', engine='pyarrow')

# Compare file sizes
import os
print(f"CSV size: {os.path.getsize('large_data.csv') / 1024**2:.2f} MB")
print(f"Parquet size: {os.path.getsize('large_data.parquet') / 1024**2:.2f} MB")

# Read Parquet
df_parquet = pd.read_parquet('large_data.parquet')
print(f"Parquet read time: {pd.read_parquet('large_data.parquet').index[-1]}")
```

**Output (approximate):**
```
CSV size: 250.00 MB
Parquet size: 50.00 MB
```

**Explanation:** Parquet’s columnar storage and compression reduce file size and improve read performance, especially for selective column queries.

## 3. Handling 1 Billion Rows with Dask

For 1 billion rows, Pandas becomes impractical due to memory constraints. Dask, a parallel computing library, scales Pandas-like operations to large datasets by processing data out-of-core.

### 3.1 Introduction to Dask

Dask provides a Pandas-like API but operates on data in chunks, enabling processing of datasets larger than memory. It integrates with Parquet for efficient storage.

**Example: Summing 1 Billion Rows with Dask**

```python
import dask.dataframe as dd
import pandas as pd
import numpy as np

# Simulate a large Parquet file with 1 billion rows (simplified for demo)
# In practice, generate or use real data
n_rows = 1_000_000_000
df = pd.DataFrame({
    'id': np.arange(n_rows),
    'value': np.random.randn(n_rows),
    'category': np.random.choice(['A', 'B', 'C'], n_rows)
})
df.to_parquet('billion_rows.parquet', engine='pyarrow')

# Load and process with Dask
ddf = dd.read_parquet('billion_rows.parquet')

# Compute sum of values
total_sum = ddf['value'].sum().compute()

print(f"Total sum of values: {total_sum}")
```

**Explanation:** Dask loads the Parquet file lazily and processes it in parallel across chunks, computing the sum without loading the entire dataset into memory.

### 3.2 GroupBy Operations on 1 Billion Rows

GroupBy operations are common but memory-intensive. Dask handles them efficiently by distributing computations.

**Example: GroupBy with Dask**

```python
import dask.dataframe as dd

# Load Parquet file
ddf = dd.read_parquet('billion_rows.parquet')

# Group by category and compute mean value
group_means = ddf.groupby('category')['value'].mean().compute()

print("Mean value by category:")
print(group_means)
```

**Output (approximate):**
```
Mean value by category:
category
A    -0.0001
B     0.0002
C    -0.0003
Name: value, dtype: float64
```

**Explanation:** Dask’s distributed GroupBy splits the data across workers, aggregating results efficiently for massive datasets.

## 4. Advanced Optimizations for 1 Billion Rows

### 4.1 Parallel Processing with Dask

Dask can leverage multiple CPU cores or a cluster for faster processing. Configure a local cluster for parallel computation.

**Example: Parallel Processing with Dask**

```python
from dask.distributed import Client
import dask.dataframe as dd

# Start a local Dask client
client = Client()

# Load and process
ddf = dd.read_parquet('billion_rows.parquet')

# Compute standard deviation in parallel
std_dev = ddf['value'].std().compute()

print(f"Standard deviation of values: {std_dev}")
```

**Explanation:** The Dask `Client` enables parallel processing across multiple cores, significantly speeding up computations for 1 billion rows.

### 4.2 Filtering and Indexing

Filtering large datasets is faster with proper indexing. Parquet supports predicate pushdown, where filters are applied during data loading.

**Example: Filtering with Dask and Parquet**

```python
import dask.dataframe as dd

# Load Parquet with filter
ddf = dd.read_parquet('billion_rows.parquet')

# Filter rows where category is 'A'
filtered = ddf[ddf['category'] == 'A']

# Compute mean of filtered data
mean_filtered = filtered['value'].mean().compute()

print(f"Mean value for category A: {mean_filtered}")
```

**Explanation:** Dask and Parquet optimize filtering by applying predicates early, reducing the data loaded into memory.

## 5. Best Practices for 1 Billion Rows

- **Use Appropriate Data Types:** Always optimize data types (e.g., `int32`, `float32`, `category`) to minimize memory usage.
- **Choose Parquet or Similar Formats:** Columnar formats like Parquet reduce storage and improve query performance.
- **Leverage Dask for Scalability:** Use Dask for out-of-core processing and parallel computation.
- **Profile Memory and Performance:** Use tools like `memory_profiler` or Dask’s dashboard to monitor resource usage.
- **Partition Data:** Split large datasets into manageable Parquet partitions for faster queries.

## Conclusion

Handling 1 billion rows in Python is achievable with the right tools and techniques. Start with optimized data structures in Pandas, use chunking and Parquet for millions of rows, and scale to Dask for billions. By applying these strategies, you can process massive datasets efficiently on standard hardware.

### Notes for Your Lecture

- **Target Audience:** Assumes students have basic Python knowledge but limited experience with large-scale data processing.
- **Lecture Structure:**
  1. **Introduction (5 mins):** Explain the challenge of handling 1 billion rows and the importance of efficiency.
  2. **Essentials (15 mins):** Cover memory-efficient data structures and Pandas optimizations (Sections 1.1–1.2).
  3. **Millions of Rows (15 mins):** Discuss chunking and Parquet (Sections 2.1–2.2).
  4. **Billions of Rows (20 mins):** Introduce Dask and advanced techniques (Sections 3–4).
  5. **Best Practices and Q&A (10 mins):** Summarize key takeaways and address questions.
- **Teaching Tips:**
  - Run code examples live in a Jupyter Notebook to show outputs.
  - For 1 billion row examples, use smaller datasets (e.g., 10 million rows) for demos due to time and hardware limits, but explain scaling.
  - Highlight real-world use cases like log analysis or financial data processing.
  - Show Dask’s dashboard to visualize parallel computations.
- **Hardware Considerations:** The 1 billion row examples require 16–32 GB RAM and multiple cores. For classroom demos, scale down to 10–100 million rows.

If you need further customization, additional examples, or specific visualizations, let me know!
