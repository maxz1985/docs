# Python Code Reviews

## Summary

| Aspect                                                            | Notes                                                                                                                                                                                                                                  |
|-------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Code Readability & Style](#code-readability-and-style)           | - Use **meaningful variable and function names**.<br/>- Keep functions **small and focused**.<br/>- Use **consistent indentation** and formatting (e.g., spaces around operators).<br/>- Use **docstrings and comments** where needed. |
| [Correctness & Logic](#correctness-and-logic)                     | - Test with **normal, boundary, and edge cases**.<br/>- Watch for **off-by-one errors, division by zero, incorrect loop conditions**.                                                                                                  |
| [Performance & Efficiency](#performance-and-efficiency)           | - Use **list comprehensions** instead of loops where applicable.<br/>- Avoid **O(n²) operations** when an **O(n) or O(log n)** alternative exists.<br/>- Use **sets, dictionaries, and built-in functions** for efficiency.            |
| [Code Reusability & Modularity](#code-reusability-and-modularity) | - Follow **DRY (Don't Repeat Yourself)**.<br/>- Use **helper functions** instead of copying logic.<br/>- Use **classes or modules** for structured organization.                                                                       |
| [Security & Error Handling](#security-and-error-handling)         | - Use **try-except** to handle predictable errors.<br/>- Avoid **catching broad exceptions (`except Exception`)**.<br/>- Validate **user input** and prevent **injection attacks**.                                                    |
| [Testability](#testability)                                       | - Avoid **hardcoded values** inside functions.<br/>- Use **dependency injection** to make testing easier.<br/>- Write **unit tests** with `unittest` or `pytest`.                                                                      |
| [Proper Use of Python Features](#proper-use-of-python-features)   | - Use **enumerate()** instead of `range(len(...))`.<br/>- Use **list comprehensions** instead of `for` loops for simple transformations.<br/>- Use **context managers (`with` statements)** for resource management.                   |
| [Documentation](#documentation)                                   | - Each function should have a **docstring** describing its purpose and parameters.<br/>- Use **type hints** to improve clarity.                                                                                                        |

## Code Readability and Style
<compare type="top-bottom">
<code-block lang="python">
# Bad: Non-descriptive variable names, no spaces
def f(a,b): return a+b
</code-block>
<code-block lang="Python">
# Good: Clear function name, readable format
def add_numbers(x: int, y: int) -> int:
    """Returns the sum of x and y."""
    return x + y
</code-block>
</compare>

[Back to Summary](#summary)

## Correctness and Logic
<compare type="top-bottom">
<code-block lang="Python">
# Bug: Will fail when denominator is zero
def divide(x, y):
    return x / y
</code-block>
<code-block lang="Python">
# Fixed: Prevents division by zero
def divide(x, y):
    if y == 0:
        raise ValueError("Cannot divide by zero")
    return x / y
</code-block>
</compare>

[Back to Summary](#summary)

## Performance and Efficiency
<compare type="top-bottom">
<code-block lang="Python">
# Inefficient O(n²) search
def has_duplicates(lst):
    return any(lst.count(x) > 1 for x in lst)
</code-block>
<code-block lang="Python">
# Efficient O(n) solution using a set
def has_duplicates(lst):
    return len(lst) != len(set(lst))
</code-block>
</compare>
<compare type="top-bottom">
<code-block lang="Python">
# Inefficient using loop
numbers = [1, 2, 3, 4, 5]
squared_numbers = []

for num in numbers:
squared_numbers.append(num ** 2)

print(squared_numbers) # Output: [1, 4, 9, 16, 25]
</code-block>
<code-block lang="Python">
# Efficient using list comprehension
numbers = [1, 2, 3, 4, 5]
squared_numbers = [num ** 2 for num in numbers]

print(squared_numbers) # Output: [1, 4, 9, 16, 25]
</code-block>
</compare>

[Back to Summary](#summary)

## Code reusability and modularity
<compare type="top-bottom">
<code-block lang="Python">
# Repeating logic multiple times
def process_data1(data):
    ...
    cleaned = [x.strip().lower() for x in data]
    return cleaned

def process_data2(data):
    ...
    cleaned = [x.strip().lower() for x in data]
    return cleaned
</code-block>
<code-block lang="Python">
# Refactored into a reusable function
def clean_data(data):
    return [x.strip().lower() for x in data]

def process_data1(data):
    return clean_data(data)

def process_data2(data):
    return clean_data(data)
</code-block>
</compare>

[Back to Summary](#summary)

## Security and Error Handling
<compare type="top-bottom">
<code-block lang="Python">
# Bad: Catches all exceptions, hides real issues
try:
    result = int(input("Enter a number: "))
except Exception:
    print("Something went wrong")
</code-block>
<code-block lang="Python">
# Good: Catches specific errors
try:
    result = int(input("Enter a number: "))
except ValueError:
    print("Invalid input! Please enter a valid integer.")
</code-block>
</compare>

[Back to Summary](#summary)

## Testability
<compare type="top-bottom">
<code-block lang="Python">
# Hardcoded values make it difficult to test
def connect_to_db():
    conn = Database("prod-db-url")  # Hardcoded production URL
    return conn
</code-block>
<code-block lang="Python">
# Uses parameters for flexibility & testability
def connect_to_db(db_url):
    conn = Database(db_url)
    return conn
</code-block>
</compare>
<compare type="top-bottom">
<code-block lang="Python">
# Tightly coupled: The UserService class is tightly coupled with the Database class
# Difficult to test: We cannot easily substitute a fake or mock database for testing.
class UserService:
    def __init__(self):
        self.db = Database("prod-db-url")  # Hardcoded dependency

    def get_user(self, user_id):
        return self.db.fetch_user(user_id)  # Directly using the real database
</code-block>
<code-block lang="Python">
# Uses parameters for flexibility & testability
# Since UserService no longer directly creates the database, we can inject a mock database during testing:
class UserService:
    def __init__(self, db):
        """Inject the database dependency via the constructor."""
        self.db = db

    def get_user(self, user_id):
        return self.db.fetch_user(user_id)

</code-block>
</compare>

Testing with the updated class using a mock database
```Python
class MockDatabase:
    """A fake database class for testing."""
    def fetch_user(self, user_id):
        return {"id": user_id, "name": "Test User"}  # Simulated user data

# Inject the mock database into the service
mock_db = MockDatabase()
user_service = UserService(mock_db)

# Now the test doesn't depend on a real database
print(user_service.get_user(42))  # Output: {'id': 42, 'name': 'Test User'}
```
[Back to Summary](#summary)

## Proper use of Python Features
<compare type="top-bottom">
<code-block lang="Python">
# Bad: Uses range(len(...)) manually
for i in range(len(names)):
    print(i, names[i])
</code-block>
<code-block lang="Python">
# Good: Uses enumerate()
for i, name in enumerate(names):
    print(i, name)
</code-block>
</compare>
<compare type="top-bottom">
<code-block lang="Python">
# Bad: no context manager
# If an exception occurs before file.close(), the file remains open, leading to resource leaks.
def read_file(filename):
    file = open(filename, "r")  # Opens the file
    content = file.read()
    file.close()  # You must remember to close the file
    return content
</code-block>
<code-block lang="Python">
# Good: Uses context manager
def read_file(filename):
    with open(filename, "r") as file:  # Context manager automatically handles cleanup
        content = file.read()
    return content  # File is automatically closed when the block ends

</code-block>
</compare>

Context manager for database connections
```Python
import sqlite3

def get_users():
    with sqlite3.connect("users.db") as conn:  # Automatically closes connection
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM users")
        return cursor.fetchall()  # No need to manually close the connection
```
Custom context manager for special resources
```Python
class FileHandler:
    def __init__(self, filename):
        self.filename = filename
    
    def __enter__(self):
        self.file = open(self.filename, "r")
        return self.file  # This is returned when `with` starts

    def __exit__(self, exc_type, exc_value, traceback):
        self.file.close()  # Automatically closes the file

# Using the custom context manager
with FileHandler("test.txt") as file:
    content = file.read()
    print(content)  # File is closed when the block ends
```
[Back to Summary](#summary)

## Documentation
<compare type="top-bottom">
<code-block lang="Python">
# No docstring, unclear function
def add(x, y):
    return x + y
</code-block>
<code-block lang="Python">
# Properly documented function with type hints
def add(x: int, y: int) -> int:
    """Returns the sum of two integers."""
    return x + y
</code-block>
</compare>

[Back to Summary](#summary)

## Interesting Example
I find this function in a code review.
```Python
def add_distance_from_base(coords, base_coords):
    """
    Accepts `coords` list of `[lat, lon]` elements and a tuple `base_coords (lat, lon)`
    representing coordinates of the base station.
    
    Returns a modified `coords` list containing computed distance from base to each coordinate.
    """
    return [coord + [geodesic(base_coords, tuple(coord)).kilometers] for coord in coords]
```
Well-written.
Take a list of `lat`,`lon` points and add distance from the base station (radio) to each point.
Uses list comprehension so that is good too.
Clear and concise.

In the body of `main` it is called as follows:
```Python
distances = add_distance_from_base(coords_list, base)
```
The solution runs in a container so resources are at a premium. The typical list passed into the function is large.

So what is the issue? The function is creating and returning a **NEW LIST**. So we're consuming memory twice.

What we should do is **modify** list **in place** like so:

```Python
def add_distance_from_base(coords, base_coords):
    
    for coord in coords:
        # Compute distance in km and Append the distance to the sublist
        coord.append(geodesic(base_coords, tuple(coord)).kilometers)  
    return coords  # Return the modified list
```
Why no list comprehension in this case? 

List comprehension **always** creates **NEW** lists. Modification in place is done via loop iterators.

Now what happens if we call this updated function from the `main`?
```Python
distances = add_distance_from_base(coords_list, base)
```
The `distance` and `coords_list` objects point to the same list. So if you do `(distances is coords_list)`, it will return `True`

Any modifications to `distance` will reflect in `coords_list` and vice versa. In this case it is ok.

We can now call the function without an assignment and use `coords_list` in the calculations.
```Python
add_distance_from_base(coords_list, base)
```
Now why keep the `return` statement if we aren't using the result?
* Supports method calling on the function. For example, you can do `print(add_distance_from_base(coords_list, base))`
* Allows for assignment when wanted `distances = add_distance_from_base(coords_list, base)`
* Clarity – Returning the modified list makes it clear that the function has an effect.


### Aside
Someone suggested that if we want to support independent modifications to `distances` and `coord_list`
**AND save memory**, we should pass a shallow copy of the list to the function like so:
```Python
distances = add_distance_from_base(coords_list[:], base)
```
This would work **if the function was modifying only a _small_ part of the list**.

In our case, the approach will still consume twice the memory **because we're modifying _each element of the list_**.

The shallow copy in python copies all the pointers into another object.

So initially after doing 
```Python
new_list = old_list[:]
```
before any modifications to either of the lists, the following will be true
```Python
(new_list is old_list) # -> False -> a new list object is created
(new_list == old_list) # -> True -> the pointers in both objects are identical
```
The memory consumption at this point is single list.

Now let us say we start modifying the `new_list`.
Python will begin creating new pointers and allocating memory for each modified element.


The `(new_list == old_list)` becomes `False`
and, if **each** element of the `new_list` gets modified,
we have double the memory consumption – full `new_list` and full `old_list`.

In our case, the `add_distance_from_base` does modify **each** element of the shallow copy,
and therefore, in the end will create a `distances` list of the same size as `coords_list` saving no memory.
