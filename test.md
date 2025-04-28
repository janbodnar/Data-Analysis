# Priklady



```go
package main

import (
	"encoding/csv"
	"fmt"
	"os"
	"github.com/bxcodec/faker/v3"
)

// User structure to define fake user data
type User struct {
	Name  string `faker:"name"`
	Email string `faker:"email"`
	Phone string `faker:"phone_number"`
}

func main() {
	// Open a file to save the generated data
	file, err := os.Create("fake_data.csv")
	if err != nil {
		fmt.Println("Error creating file:", err)
		return
	}
	defer file.Close()

	// Create a CSV writer
	writer := csv.NewWriter(file)
	defer writer.Flush()

	// Write header row to the CSV file
	headers := []string{"Name", "Email", "Phone"}
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

		// Write user data to the CSV file
		row := []string{user.Name, user.Email, user.Phone}
		if err := writer.Write(row); err != nil {
			fmt.Println("Error writing row:", err)
			return
		}

		// Optional: Display progress every 100,000 rows
		if (i+1)%100000 == 0 {
			fmt.Printf("%d rows generated...\n", i+1)
		}
	}

	fmt.Println("Fake data generation completed. File saved as 'fake_data.csv'.")
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
