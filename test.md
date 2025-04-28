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
