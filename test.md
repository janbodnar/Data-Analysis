# Priklady

## Proxies

```python
#!/usr/bin/python

import httpx
import asyncio
import time
from typing import List

# URLs to fetch
urls = [
    "http://webcode.me",
    "https://httpbin.org/get",
    "https://google.com",
    "https://stackoverflow.com",
    "https://github.com",
    "https://sme.sk",
]

# Asynchronous version
async def get_async(url: str) -> httpx.Response:
    async with httpx.AsyncClient(proxies=None) as client:
        return await client.get(url)

async def fetch_async() -> List[int]:
    resps = await asyncio.gather(*map(get_async, urls))
    return [resp.status_code for resp in resps]

# Synchronous version
def get_sync(url: str) -> httpx.Response:
    with httpx.Client(proxies=None) as client:
        return client.get(url)

def fetch_sync() -> List[int]:
    return [get_sync(url).status_code for url in urls]

# Benchmarking function
def benchmark():
    print("Running benchmarks...\n")

    # Benchmark synchronous version
    start_time = time.time()
    sync_status_codes = fetch_sync()
    sync_duration = time.time() - start_time
    print("Synchronous Results:")
    for url, status_code in zip(urls, sync_status_codes):
        print(f"{url}: {status_code}")
    print(f"Synchronous Duration: {sync_duration:.2f} seconds\n")

    # Benchmark asynchronous version
    start_time = time.time()
    async_status_codes = asyncio.run(fetch_async())
    async_duration = time.time() - start_time
    print("Asynchronous Results:")
    for url, status_code in zip(urls, async_status_codes):
        print(f"{url}: {status_code}")
    print(f"Asynchronous Duration: {async_duration:.2f} seconds\n")

    # Summary
    print("Benchmark Summary:")
    print(f"Synchronous: {sync_duration:.2f} seconds")
    print(f"Asynchronous: {async_duration:.2f} seconds")
    print(
        f"Asynchronous is {((sync_duration - async_duration) / sync_duration * 100):.2f}% faster"
        if sync_duration > async_duration
        else f"Synchronous is {((async_duration - sync_duration) / async_duration * 100):.2f}% faster"
    )

if __name__ == "__main__":
    benchmark()
```


## Benchmark

```python
#!/usr/bin/python

import httpx
import asyncio
import time
from typing import List

# URLs to fetch
urls = [
    "http://webcode.me",
    "https://httpbin.org/get",
    "https://google.com",
    "https://stackoverflow.com",
    "https://github.com",
]

# Asynchronous version
async def get_async(url: str) -> httpx.Response:
    async with httpx.AsyncClient() as client:
        return await client.get(url)

async def fetch_async() -> List[int]:
    resps = await asyncio.gather(*map(get_async, urls))
    return [resp.status_code for resp in resps]

# Synchronous version
def get_sync(url: str) -> httpx.Response:
    with httpx.Client() as client:
        return client.get(url)

def fetch_sync() -> List[int]:
    return [get_sync(url).status_code for url in urls]

# Benchmarking function
def benchmark():
    print("Running benchmarks...\n")

    # Benchmark synchronous version
    start_time = time.time()
    sync_status_codes = fetch_sync()
    sync_duration = time.time() - start_time
    print("Synchronous Results:")
    for url, status_code in zip(urls, sync_status_codes):
        print(f"{url}: {status_code}")
    print(f"Synchronous Duration: {sync_duration:.2f} seconds\n")

    # Benchmark asynchronous version
    start_time = time.time()
    async_status_codes = asyncio.run(fetch_async())
    async_duration = time.time() - start_time
    print("Asynchronous Results:")
    for url, status_code in zip(urls, async_status_codes):
        print(f"{url}: {status_code}")
    print(f"Asynchronous Duration: {async_duration:.2f} seconds\n")

    # Summary
    print("Benchmark Summary:")
    print(f"Synchronous: {sync_duration:.2f} seconds")
    print(f"Asynchronous: {async_duration:.2f} seconds")
    print(
        f"Asynchronous is {((sync_duration - async_duration) / sync_duration * 100):.2f}% faster"
        if sync_duration > async_duration
        else f"Synchronous is {((async_duration - sync_duration) / async_duration * 100):.2f}% faster"
    )

if __name__ == "__main__":
    benchmark()
```


## Async requests

```python
#!/usr/bin/python

import httpx
import asyncio

async def get_async(url):
    async with httpx.AsyncClient() as client:
        return await client.get(url)

urls = ['http://webcode.me', 'https://httpbin.org/get',
    'https://google.com', 'https://stackoverflow.com',
    'https://github.com']

async def launch():
    resps = await asyncio.gather(*map(get_async, urls))
    data = [resp.status_code for resp in resps]

    for status_code in data:
        print(status_code)

asyncio.run(launch())
```

## Async screenshots

```python
import asyncio
from playwright.async_api import async_playwright

async def take_screenshot(url, output_file):
    async with async_playwright() as p:
        browser = await p.chromium.launch()
        page = await browser.new_page()
        try:
            await page.goto(url, wait_until="networkidle")
            await page.screenshot(path=output_file, full_page=True)
            print(f"Screenshot saved: {output_file}")
        except Exception as e:
            print(f"Error processing {url}: {e}")
        finally:
            await browser.close()

async def main():
    # List of URLs to capture
    pages = [
        {"url": "https://example.com", "output": "example_com.png"},
        {"url": "https://python.org", "output": "python_org.png"},
        {"url": "https://github.com", "output": "github_com.png"}
    ]
    
    # Create tasks for each screenshot
    tasks = [take_screenshot(page["url"], page["output"]) for page in pages]
    
    # Run all tasks concurrently
    await asyncio.gather(*tasks)

if __name__ == "__main__":
    asyncio.run(main())
```


## read with Go

```go
package main

import (
	"encoding/csv"
	"fmt"
	"os"
	"strconv"
)

func main() {
	// Open the CSV file
	file, err := os.Open("fake_data_with_salary.csv")
	if err != nil {
		fmt.Printf("Error opening file: %v\n", err)
		return
	}
	defer file.Close()

	// Create a CSV reader
	reader := csv.NewReader(file)
	records, err := reader.ReadAll()
	if err != nil {
		fmt.Printf("Error reading CSV: %v\n", err)
		return
	}

	// Initialize sum
	var totalSalary float64

	// Skip header and sum salaries
	for i, record := range records {
		if i == 0 {
			continue // Skip header row
		}
		salary, err := strconv.ParseFloat(record[3], 64)
		if err != nil {
			fmt.Printf("Error parsing salary in row %d: %v\n", i+1, err)
			continue
		}
		totalSalary += salary
	}

	// Print the result
	fmt.Printf("Total sum of all salaries: %.0f\n", totalSalary)
}
```


## read pandas

```python
import pandas as pd

# Read the CSV file using Pandas
df = pd.read_csv('fake_data_with_salary.csv')

# Compute the sum of the Salary column
total_salary = df['Salary'].sum()

# Print the result
print(f"Total sum of all salaries: {total_salary}")
```


## read dusk

```python
import dask.dataframe as dd

# Read the CSV file using Dask
df = dd.read_csv('fake_data_with_salary.csv')

# Compute the sum of the Salary column
total_salary = df['Salary'].sum().compute()

# Print the result
print(f"Total sum of all salaries: {total_salary}")
```



```go
package main

import (
	"encoding/csv"
	"fmt"
	"math/rand"
	"os"
	"time"

	"github.com/bxcodec/faker/v3"
)

// User structure to define fake user data
type User struct {
	Name   string `faker:"name"`
	Email  string `faker:"email"`
	Phone  string `faker:"phone_number"`
	Salary int    // Salary will be generated manually
}

func main() {
	// Seed the random number generator
	rand.Seed(time.Now().UnixNano())

	// Open a file to save the generated data
	file, err := os.Create("fake_data_with_salary.csv")
	if err != nil {
		fmt.Println("Error creating file:", err)
		return
	}
	defer file.Close()

	// Create a CSV writer
	writer := csv.NewWriter(file)
	defer writer.Flush()

	// Write header row to the CSV file
	headers := []string{"Name", "Email", "Phone", "Salary"}
	if err := writer.Write(headers); err != nil {
		fmt.Println("Error writing headers:", err)
		return
	}

	// Generate 1 million rows of fake data
	for i := 0; i < 1000000; i++ {
		// Create a fake user
		user := User{}
		if err := faker.FakeData(&user); err != nil {
			fmt.Println("Error generating fake data:", err)
			return
		}

		// Generate a random salary between 30,000 and 150,000
		user.Salary = rand.Intn(120001) + 30000

		// Write user data to the CSV file
		row := []string{user.Name, user.Email, user.Phone, fmt.Sprintf("%d", user.Salary)}
		if err := writer.Write(row); err != nil {
			fmt.Println("Error writing row:", err)
			return
		}

		// Optional: Display progress every 100,000 rows
		if (i+1)%100000 == 0 {
			fmt.Printf("%d rows generated...\n", i+1)
		}
	}

	fmt.Println("Fake data generation completed. File saved as 'fake_data_with_salary.csv'.")
}
```


## Pomale riesenie

```python
import random

with open('data.txt', 'w') as f:

    count = 0

    for _ in range(1000):

        r = random.randint(1, 100)

        f.write(f'{r} ')
        count += 1

        if count % 10 == 0:
            f.write('\n')
```

## Lepsie riesenie

```python
import random

with open('data.txt', 'w') as f:

    vals_10 = []

    for n in range(1, 1001):

        r = random.randint(1, 100)
        vals_10.append(r)

        if len(vals_10) == 10:
            f.write(" ".join(map(str, vals_10)) + '\n')
            vals_10 = []
```

## Este lepsie riesenie

```python
import random

with open('data.txt', 'w') as f:

    vals_10 = []

    for n in range(1, 1001):
        f.write(' '.join(map(str, random.sample(range(1, 100), 10))) + '\n')
```
