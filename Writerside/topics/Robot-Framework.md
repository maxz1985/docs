# Robot Framework

---

### **1. Robot Framework**

#### **Key Points**:
1. **Definition**:
    - Robot Framework is a **keyword-driven** test automation framework written in Python. It is used for **acceptance testing**, **behavior-driven development (BDD)**, and **RPA (Robotic Process Automation)**.

2. **Key Features**:
    - **Keyword-Driven Testing**:
        - Tests are written using reusable keywords (like functions) instead of hardcoding logic.
    - **Human-Readable Syntax**:
        - Test cases are simple to understand and use a tabular format (text files or `.robot` files).
    - **Library Support**:
        - Built-in libraries (for example, `BuiltIn`, `Collections`) and external libraries (for example, `SeleniumLibrary`, `AppiumLibrary`).
    - **Extensibility**:
        - You can create custom keywords in Python, Java, or other languages.
    - **Reports and Logs**:
        - Automatically generates detailed test reports and logs in HTML format.
    - **Cross-Platform**:
        - Works on Windows, macOS, and Linux.

3. **Use Cases**:
    - Test automation for web, APIs, databases, desktop apps, and mobile apps.
    - Robotic Process Automation (RPA) tasks.

---

- **Difference from Selenium**
    - **Robot Framework** is a higher-level framework that integrates with Selenium (via `SeleniumLibrary`) to perform browser-based automation, making it more user-friendly for non-developers.

---

#### **Example Test Case**:
```robotframework
*** Settings ***
Library    SeleniumLibrary

*** Variables ***
${BROWSER}    Chrome
${URL}        https://example.com

*** Test Cases ***
Verify Page Title
    Open Browser    ${URL}    ${BROWSER}
    Title Should Be    Example Domain
    Close Browser
```

---

### **2. Custom keywords in Robot Framework**

#### **WCustom Keywords**
Custom keywords are user-defined reusable functions or methods that extend Robot Framework’s functionality. You can:
- Define them in the test case file itself.
- Implement them in a separate resource file or a Python script.

---

#### **Defining Custom Keywords in `.robot` Files**:
```robotframework
*** Keywords ***
Login To Application
    Input Text    username_field    my_username
    Input Text    password_field    my_password
    Click Button    login_button
```

You can now use `Login To Application` as a keyword in your test cases:
```robotframework
*** Test Cases ***
Verify Login
    Open Browser    https://example.com    Chrome
    Login To Application
    Page Should Contain    Welcome
    Close Browser
```

---

#### **Defining Custom Keywords in Python**:
1. Create a Python file (for example, `custom_keywords.py`).
2. Implement your custom keywords:
   ```python
   from robot.api.deco import keyword

   @keyword("Add Two Numbers")
   def add_two_numbers(a, b):
       return int(a) + int(b)
   ```
3. Import and use the custom keyword in a `.robot` file:
   ```robotframework
   *** Settings ***
   Library    custom_keywords.py

   *** Test Cases ***
   Verify Addition
       ${result}=    Add Two Numbers    5    3
       Should Be Equal    ${result}    8
   ```

---

- Custom Keywords enhance **reusability**, **modularity**, and **readability**.
- Name keywords clearly, keep them modular, and use resource files or Python libraries for better organization.

---

### **3. Integrate Robot Framework with Selenium for web automation**

#### **Integration Steps**:
1. **Install `SeleniumLibrary`**:
    - Use `pip` to install the library:
      ```bash
      pip install robotframework-seleniumlibrary
      ```

2. **Import the Library**:
    - In your `.robot` file, import `SeleniumLibrary`:
      ```robotframework
      *** Settings ***
      Library    SeleniumLibrary
      ```

3. **Write Web Automation Test Cases**:
    - Use keywords from `SeleniumLibrary` for browser-based actions.

---

#### **Example**:
```robotframework
*** Settings ***
Library    SeleniumLibrary

*** Variables ***
${BROWSER}    Chrome
${URL}        https://example.com

*** Test Cases ***
Verify Login Functionality
    Open Browser    ${URL}    ${BROWSER}
    Input Text      username_field    test_user
    Input Text      password_field    test_password
    Click Button    login_button
    Page Should Contain    Welcome
    Close Browser
```

---

#### **Key SeleniumLibrary Keywords**:
| **Keyword**              | **Description**                               |
|--------------------------|-----------------------------------------------|
| `Open Browser`           | Opens a browser to a specified URL.           |
| `Input Text`             | Enters text into a form field.                |
| `Click Button`           | Clicks a button on the page.                  |
| `Page Should Contain`    | Asserts that the page contains specific text. |
| `Close Browser`          | Closes the currently opened browser.          |

---

- Use XPath/CSS selectors to locate elements dynamically:
  ```robotframework
  Click Element    xpath=//button[text()='Login']
  ```

- Use SeleniumLibrary’s `Wait Until Element Is Visible` or `Wait Until Page Contains`:
  ```robotframework
  Wait Until Element Is Visible    login_button    10s
  ```
---

- Use `Suite Setup`, `Suite Teardown`, `Test Setup`, and `Test Teardown` in your test files.
- Use resource files, variable files, and folder-based organization.
- Reports are auto-generated in HTML, but you can customize them using plugins like `robotframework-reports`.
