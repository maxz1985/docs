# PyTest

---

### **1. Key features of `pytest`, difference from `unittest`.**

#### **Key Features of `pytest`**:
1. **Simple Test Syntax**:
    - Tests are plain functions, and assertions use Python's built-in `assert`.
    - Example:
      ```python
      def test_addition():
          assert 1 + 1 == 2
      ```

2. **Fixtures**:
    - `pytest` provides a powerful way to set up and tear down test dependencies using **fixtures**.
    - Example:
      ```python
      import pytest
 
      @pytest.fixture
      def sample_data():
          return [1, 2, 3]
 
      def test_length(sample_data):
          assert len(sample_data) == 3
      ```

3. **Parameterization**:
    - Easily test functions with multiple input values using `@pytest.mark.parametrize`.
    - Example:
      ```python
      @pytest.mark.parametrize("a, b, expected", [(1, 1, 2), (2, 3, 5), (3, 5, 8)])
      def test_addition(a, b, expected):
          assert a + b == expected
      ```

4. **Plugins**:
    - `pytest` supports a variety of plugins like `pytest-django`, `pytest-mock`, and more, allowing extended functionality.

5. **Rich Command-Line Interface**:
    - Run tests, filter by keywords, or debug with a single command:
      ```bash
      pytest -v -k "test_name"
      ```

6. **Built-in Fixtures**:
    - Includes fixtures like `tmp_path`, `caplog`, and `monkeypatch` for common testing needs.

---

#### **Comparison to `unittest`**:
| **Feature**      | **`pytest`**                      | **`unittest`**              |
|------------------|-----------------------------------|-----------------------------|
| Test Structure   | Function-based (no class needed). | Class-based by default.     |
| Assertions       | Plain `assert`.                   | Requires `self.assert*()`.  |
| Fixtures         | Powerful and flexible.            | Limited and less intuitive. |
| Parameterization | Built-in.                         | Requires third-party tools. |
| Plugins          | Extensive plugin ecosystem.       | Limited or absent.          |

---

### **2. Using `pytest` fixtures**

#### **Fixtures
**
- Fixtures in `pytest` are reusable test setup functions.
- They help in **setting up test dependencies**, such as initializing a database, creating temporary files, or configuring mocks.

---

#### **Example: Using Fixtures**
```python
import pytest

# Define a fixture
@pytest.fixture
def sample_data():
    return [1, 2, 3]

# Use the fixture in a test
def test_sum(sample_data):
    assert sum(sample_data) == 6
```

---

#### **Advantages of Fixtures**:
1. **Code Reusability**:
    - Avoid repetitive setup code by reusing fixtures across multiple tests.
2. **Modularity**:
    - Fixtures can be composed by using one fixture inside another.
3. **Automatic Cleanup**:
    - Use the `yield` statement in fixtures to include teardown logic.

---

#### **Example: Fixture with Setup and Teardown**
```python
@pytest.fixture
def database_connection():
    db = connect_to_database()  # Setup
    yield db  # Provide the resource to the test
    db.close()  # Teardown
```

---

#### **Scope of Fixtures**:
Fixtures can have different scopes:
- **`function`** (default): Runs once per test function.
- **`module`**: Runs once per module.
- **`session`**: Runs once for the entire test session.
- **`class`**: Runs once per test class.

Example:
```python
@pytest.fixture(scope="module")
def setup_module():
    print("Setup for the module")
    yield
    print("Teardown for the module")
```

---

### **3. Parametrizing tests in `pytest`**

#### **What is Parameterization?**
- Parameterization allows you to run a single test function multiple times with different input values and expected results.
- This reduces code duplication and makes it easy to test multiple scenarios.

---

#### **Example: Basic Parameterization**
```python
import pytest

# Use @pytest.mark.parametrize
@pytest.mark.parametrize("a, b, expected", [
    (1, 2, 3),
    (2, 3, 5),
    (3, 5, 8)
])
def test_addition(a, b, expected):
    assert a + b == expected
```

**Output** when you run the test:
```
test_example.py::test_addition[1-2-3] PASSED
test_example.py::test_addition[2-3-5] PASSED
test_example.py::test_addition[3-5-8] PASSED
```

---

#### **Advantages of Parameterization**:
1. **Test Coverage**:
    - Run the same test with multiple inputs to ensure the function works in all scenarios.
2. **Clean Code**:
    - Avoid writing multiple test functions for each set of inputs.
3. **Improved Debugging**:
    - Each set of parameters is treated as a separate test, making it easy to identify failing cases.

---

#### **Advanced Parameterization: Combining Parameters**
You can combine multiple sets of parameters using **`@pytest.mark.parametrize`**:
```python
import pytest

@pytest.mark.parametrize("x", [1, 2, 3])
@pytest.mark.parametrize("y", [10, 20])      
def test_multiplication(x, y):
    assert x * y == x * y
```

**Output**:
- The test will run 6 times (3 values of `x` × 2 values of `y`).

---

### **Other `pytest`**
- Use `pytest-mock` or `unittest.mock` for mocking dependencies.
- Use the `-k` flag to filter tests by keyword.
      ```bash
      pytest -k "test_function_name"
      ```
- Use the `pytest-cov` plugin for coverage:
      ```bash
      pip install pytest-cov
      pytest --cov=your_module tests/
      ```

---
