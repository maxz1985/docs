# Python

### 1. **Core Concepts**
- `Python`'s key differences compared to other programming languages like `Java` or `C#`.
  - Ability to mix OOP and Imperative programming. 
  - Readability and simplicity with clean syntax. No need for `public static void` declarations. Uses `significant space indentation` to frame code blocks, no need for brackets.
  - **`Dynamic typing`** ( `x = 10`, not `int x = 10;`). `Java` and `C#` are statically typed languages.
  - Super rich collection of well-supported third party packages suited for any purpose.
  - **Interpreted** vs. Compiled. In this context, `Python` is _interpreted_ b/c there is no separate notion of `VM`, pre-compile to bytecode is not a separate stage, and `.pyc` files aren't portable.
- Python’s Global Interpreter Lock (GIL) and its impact on multithreaded applications.
  - `Global Interpreter Lock (GIL)` is a mutex in the Interpreter that allows execution of only one thread at a time. 
  - This is connected to the way Python manages objects in memory via reference counting
  - Especially problematic for CPU-Bound tasks
  - don't use `threading` library for CPU bound tasks, but you can use `threading` for i/o bound tasks.
  - To work around the issue, use `multiprocessing ` library which launches each process on a separate CPU Core with its own Python Interpreter.

### 2. **Practical Use**
- Handling large datasets in Python.
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
- Debugging
  - Logging, IDE debuggers
- Unit Tests and mocking dependencies.
  - Using pytest, use mocking with `pytest-mock` to represent dependencies like API, Databases, Class, Function
Mocking a function
```Python
def fetch_data():
    return "real data"

def process_data():
    data = fetch_data()
    return f"processed {data}"

def test_process_data(mocker):
    mock_fetch_data = mocker.patch('__main__.fetch_data', return_value="mocked data")

    result = process_data()

    assert result == "processed mocked data"
    mock_fetch_data.assert_called_once()

```
## Linked List
```Python
class Node:
    """
    A class to represent a single node in the linked list.
    """
    def __init__(self, data):
        self.data = data  # Store data
        self.next = None  # Reference to the next node (default is None)


class LinkedList:
    """
    A class to represent the linked list.
    """
    def __init__(self):
        self.head = None  # Initialize the head of the linked list

    def is_empty(self):
        """
        Check if the linked list is empty.
        :return: True if the list is empty, False otherwise.
        """
        return self.head is None

    def append(self, data):
        """
        Append a new node to the end of the linked list.
        :param data: Data to be added to the new node.
        """
        new_node = Node(data)
        if self.head is None:  # If the list is empty
            self.head = new_node
        else:
            current = self.head
            while current.next:  # Traverse to the last node
                current = current.next
            current.next = new_node  # Link the last node to the new node

    def prepend(self, data):
        """
        Prepend a new node to the beginning of the linked list.
        :param data: Data to be added to the new node.
        """
        new_node = Node(data)
        new_node.next = self.head  # Point the new node to the current head
        self.head = new_node  # Update the head to the new node

    def delete(self, data):
        """
        Delete the first node with the given data.
        :param data: Data of the node to be deleted.
        """
        if self.head is None:  # If the list is empty
            return

        if self.head.data == data:  # If the head node contains the data
            self.head = self.head.next  # Update the head to the next node
            return

        current = self.head
        while current.next and current.next.data != data:  # Traverse to the node before the target
            current = current.next

        if current.next:  # If the target node is found
            current.next = current.next.next  # Skip the target node

    def find(self, data):
        """
        Find the first node with the given data.
        :param data: Data to search for in the linked list.
        :return: The node containing the data, or None if not found.
        """
        current = self.head
        while current:
            if current.data == data:
                return current
            current = current.next
        return None

    def display(self):
        """
        Display the contents of the linked list.
        """
        current = self.head
        result = []
        while current:
            result.append(current.data)
            current = current.next
        print(" -> ".join(map(str, result)))


# Example Usage
if __name__ == "__main__":
    ll = LinkedList()
    print("Is list empty?", ll.is_empty())

    ll.append(10)
    ll.append(20)
    ll.append(30)
    ll.display()  # Output: 10 -> 20 -> 30

    ll.prepend(5)
    ll.display()  # Output: 5 -> 10 -> 20 -> 30

    ll.delete(20)
    ll.display()  # Output: 5 -> 10 -> 30

    node = ll.find(10)
    print("Found node:", node.data if node else "Not found")  # Output: Found node: 10

    ll.delete(5)
    ll.display()  # Output: 10 -> 30
```
## Extract Property from a collection of objects.
Objects
```Python
class Item:
    def __init__(self, name, description):
        self.name = name
        self.description = description

# Create a collection of objects
items = [
    Item("Apple", "A fruit"),
    Item("Carrot", "A vegetable"),
    Item("Banana", "Another fruit"),
]

```
List Comprehension—Python way, fastest

```Python
names = [item.name for item in items]
print(names)  # Output: ['Apple', 'Carrot', 'Banana']
```
Generator – Lazy evaluation, large data
```Python
names = (item.name for item in items)
print(list(names))  # Output: ['Apple', 'Carrot', 'Banana']
```
List Unpacking—as part of other processing, small data size
```Python
names = [*(item.name for item in items)]
print(names)  # Output: ['Apple', 'Carrot', 'Banana']
```
For loop – expressive classroom
```Python
names = []
for item in items:
    names.append(item.name)
print(names)  # Output: ['Apple', 'Carrot', 'Banana']
```