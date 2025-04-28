# Priklady

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
