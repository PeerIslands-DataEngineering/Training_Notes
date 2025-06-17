# **Unit Testing **  

## **What is Unit Testing?**  
Unit testing is a software testing technique where **individual components (units) of a program** are tested in isolation to ensure correctness.  

## **Why Unit Testing?**  
✅ **Detects bugs early**  
✅ **Ensures code reliability**  
✅ **Facilitates refactoring**  
✅ **Improves maintainability**  

## **Key Concepts:**  
1. **Test Cases** – Define input and expected output.  
2. **Assertions** – Verify expected vs. actual results.  
3. **Mocks & Stubs** – Simulate dependencies (e.g., databases, APIs).  
4. **Fixtures** – Setup and teardown test environments.  

# **Pytest  **  

Pytest is a powerful and flexible testing framework for Python. It is widely used for writing unit tests, integration tests, and functional tests.

---

## **1. Why Use Pytest?**
✅ Simple and easy-to-use  
✅ Supports **fixtures** for setup and teardown  
✅ Built-in **assertions** for better readability  
✅ **Parameterization** for running multiple test cases  
✅ **Plugins and extensions** for advanced features  

---

## **2. Installing Pytest**
To install pytest, run:  
```bash
pip install pytest
```
To check if pytest is installed:  
```bash
pytest --version
```

---

## **3. Writing a Basic Pytest Test**
### **Example: Testing a Simple Function**
```python
# calculator.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b
```

### **Test Case Using Pytest**
Create a file named **`test_calculator.py`**:
```python
import pytest
from calculator import add, subtract

def test_add():
    assert add(3, 5) == 8
    assert add(-1, 1) == 0

def test_subtract():
    assert subtract(10, 5) == 5
    assert subtract(2, 2) == 0
```

### **Running Tests**
Execute tests by running:  
```bash
pytest
```
Pytest will automatically find files that start with **`test_`** and execute the test functions.

---

## **4. Using Fixtures in Pytest**
Fixtures are used for setting up and tearing down test data.

### **Example: Using Fixtures for Setup**
```python
import pytest

@pytest.fixture
def sample_data():
    return {"name": "Alice", "age": 30}

def test_sample_data(sample_data):
    assert sample_data["name"] == "Alice"
    assert sample_data["age"] == 30
```
### **Running Tests with Fixtures**
Pytest automatically injects the fixture into the test function.

---

## **5. Parameterized Testing**
Pytest allows running multiple variations of a test.

### **Example: Using `@pytest.mark.parametrize`**
```python
import pytest
from calculator import add

@pytest.mark.parametrize("a, b, expected", [
    (2, 3, 5),
    (-1, -1, -2),
    (0, 5, 5),
])
def test_add(a, b, expected):
    assert add(a, b) == expected
```
### **Running the Parameterized Tests**
Each test case will be executed separately.

---

## **6. Grouping and Marking Tests**
You can categorize tests using markers.

### **Example: Using `@pytest.mark.slow`**
```python
import pytest

@pytest.mark.slow
def test_slow_function():
    import time
    time.sleep(2)
    assert True
```
### **Running Only Slow Tests**
```bash
pytest -m slow
```

---

## **7. Skipping and XFail (Expected Failures)**
You can skip tests or mark them as expected failures.

### **Example: Skipping Tests**
```python
import pytest

@pytest.mark.skip(reason="Skipping for now")
def test_skip_example():
    assert 1 == 1
```

### **Example: Expected Failure (`xfail`)**
```python
@pytest.mark.xfail
def test_expected_fail():
    assert 1 == 2
```

---

## **8. Capturing Logs in Tests**
Pytest can capture logs and verify them.

### **Example: Using `caplog` Fixture**
```python
import logging
import pytest

def function_with_logging():
    logger = logging.getLogger(__name__)
    logger.info("This is an info message.")

def test_logging(caplog):
    function_with_logging()
    assert "This is an info message." in caplog.text
```

---

## **9. Running Specific Tests**
You can run specific test files or functions.

```bash
pytest test_calculator.py  # Run all tests in test_calculator.py
pytest test_calculator.py::test_add  # Run only test_add
```

---

## **10. Generating Test Reports**
Generate a detailed report:
```bash
pytest --html=report.html
```

---

## **11. Summary**
| Feature | Pytest Advantage |
|---------|----------------|
| **Easy Assertions** | No need for `self.assertEqual()`, just use `assert` |
| **Fixtures** | Automates setup and teardown |
| **Parameterization** | Runs multiple test cases with different values |
| **Markers** | Categorize tests (e.g., `@pytest.mark.slow`) |
| **Logging** | Captures logs for debugging |
| **Skipping & XFail** | Skip tests or mark expected failures |
