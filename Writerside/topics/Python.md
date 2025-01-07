# Python

### 1. **Core Concepts and Syntax**
- **Question**: What are `Python`'s key differences compared to other programming languages like `Java` or `C#`?
  - Ability to mix OOP and Imperative programming. 
  - Readability and simplicity with clean syntax. No need for `public static void` declarations. Uses `significant space indentation` to frame code blocks, no need for brackets.
  - **`Dynamic typing`** ( `x = 10`, not `int x = 10;`). `Java` and `C#` are statically typed languages.
  - Super rich collection of well-supported third party packages suited for any purpose.
  - **Interpreted** vs. Compiled. In this context, `Python` is _interpreted_ b/c there is no separate notion of `VM`, pre-compile to bytecode is not a separate stage, and `.pyc` files aren't portable.
- **Follow-up**: Can you explain Python’s Global Interpreter Lock (GIL) and how it impacts multithreaded applications?
  - `Global Interpreter Lock (GIL)` is a mutex in the Interpreter that allows execution of only one thread at a time. 
  - This is connected to the way Python manages objects in memory via reference counting
  - Especially problematic for CPU-Bound tasks
  - don't use `threading` library for CPU bound tasks, but you can use `threading` for i/o bound tasks.
  - To work around the issue, use `multiprocessing ` library which launches each process on a separate CPU Core with its own Python Interpreter.

### 2. **Practical Use**
- **Question**: How would you handle and process large datasets in Python? Which libraries would you use?
  - `Pandas` for data, `NumPy` for numerical
  - Chunk processing
```python
import pandas as pd

# Process the file in chunks
chunk_size = 10_000
for chunk in pd.read_csv("large_dataset.csv", chunksize=chunk_size):
    # Process each chunk
    print(chunk.describe())
```
  - Generators – lazy load large files
```Python
def read_large_file(file_path):
    with open(file_path, "r") as file:
        for line in file:
            yield line  # Yield one line at a time (lazy loading)

# Example: Process each line
for line in read_large_file("large_dataset.txt"):
    print(line.strip())
```
 - Parallel processing for large datasets
```Python
from multiprocessing import Pool

def process_row(row):
    # Simulate processing
    return row * row

data = list(range(1_000_000))

# Process data in parallel
with Pool(4) as pool:  # Use 4 processes
    results = pool.map(process_row, data)

print(results[:10])
```

### 3. **Debugging and Testing**
- **Question**: How do you debug Python code, and what tools or techniques do you commonly use?
- **Follow-up**: Have you written unit tests in Python? How would you mock dependencies in a test?

Let me know if you want me to expand on these or move on to the next skill!