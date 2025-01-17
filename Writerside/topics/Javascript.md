# Javascript

### **1. Core JavaScript Concepts**

#### **1.1. Data types in JavaScript**
- JavaScript has **7 primitive types**:
    - `string`, `number`, `bigint`, `boolean`, `null`, `undefined`, `symbol`.
- **Objects**: Everything else, including arrays and functions, is an object.

#### **1.2. Difference between `==` and `===`**
- `==` (abstract equality): Compares values **with type coercion**.
- `===` (strict equality): Compares values **without type coercion**.
- Example:
  ```javascript
  5 == "5";  // true (type coercion)
  5 === "5"; // false (different types)
  ```

#### **1.3. The `this` keyword in JavaScript**
- `this` refers to the object executing the current function.
- Its value depends on how the function is called:
    - In a **method**, `this` refers to the object.
    - In a **regular function**, `this` is `undefined` in strict mode or the global object in non-strict mode.
    - In **arrow functions**, `this` is inherited from the surrounding lexical scope.

---

### **2. Functions and Closures**

#### **2.1. Closures in JavaScript**
- A closure is a function that **remembers the variables from its outer scope**, even after the outer function has finished executing.
- Example:
  ```javascript
  function outer() {
    let count = 0;
    return function inner() {
      count++;
      return count;
    };
  }

  const increment = outer();
  console.log(increment()); // 1
  console.log(increment()); // 2
  ```

#### **2.2. Higher-order functions.**
- Higher-order functions are functions that:
    1. Take other functions as arguments.
    2. Return other functions.
- Example:
  ```javascript
  function add(a, b) {
    return a + b;
  }

  function calculate(a, b, operation) {
    return operation(a, b);
  }

  console.log(calculate(5, 3, add)); // 8
  ```

#### **2.3. Difference between `var`, `let`, and `const`**
- `var`:
    - Function-scoped.
    - Hoisted but initialized as `undefined`.
    - Allows re-declaration.
- `let`:
    - Block-scoped.
    - Hoisted but not initialized.
    - Can't be re-declared in the same scope.
- `const`:
    - Block-scoped.
    - Hoisted but not initialized.
    - Must be initialized at declaration and can't be reassigned.

---

### **3. Objects and Prototypes**

#### **3.1. Prototypal inheritance**
- JavaScript uses **prototypal inheritance** to allow objects to inherit properties and methods from other objects.
- Every object has an internal property `[[Prototype]]`, which refers to another object (its prototype).
- Example:
  ```javascript
  const parent = { greet: () => "Hello!" };
  const child = Object.create(parent);
  console.log(child.greet()); // "Hello!"
  ```

#### **3.2. Difference between `Object.create()` and a constructor function**
- `Object.create()`:
    - Create an object with a specified prototype.
- Constructor function:
    - Creates objects and sets the prototype using the `new` keyword.

#### **3.3. Behaviour of `this` keyword in classes**
- In classes, `this` refers to the current instance of the class.
- Example:
  ```javascript
  class Person {
    constructor(name) {
      this.name = name;
    }
    greet() {
      return `Hello, ${this.name}`;
    }
  }

  const john = new Person("John");
  console.log(john.greet()); // "Hello, John"
  ```

---

### **4. Asynchronous JavaScript**

#### **4.1. Promises**
- Promises represent a value that will be available **asynchronously**.
- States:
    - **Pending**: Initial state.
    - **Fulfilled**: Operation completed successfully.
    - **Rejected**: Operation failed.
- Example:
  ```javascript
  const promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("Success!"), 1000);
  });

  promise.then(console.log); // Logs "Success!" after 1 second
  ```

#### **4.2. Async/await, and its difference from Promises**
- `async/await` is syntactic sugar over Promises that makes asynchronous code look synchronous.
- Example:
  ```javascript
  async function fetchData() {
    const response = await fetch("https://api.example.com/data");
    const data = await response.json();
    console.log(data);
  }
  ```

#### **4.3. Event Loop in JavaScript**
- The Event Loop is responsible for handling asynchronous code execution.
- It processes the **call stack** and the **message queue** to ensure non-blocking behavior.
- Example:
  ```javascript
  console.log("Start");

  setTimeout(() => console.log("Timeout"), 0);

  console.log("End");
  // Output: "Start", "End", "Timeout"
  ```

---

### **5. Miscellaneous**

#### **5.1. Difference between `null` and `undefined`**
- `null`: A value explicitly set to represent "no value."
- `undefined`: A variable that has been declared but not assigned a value.

#### **5.2. Difference between `call()`, `apply()`, and `bind()`**
- All three are used to set the `this` context for a function.
- **`call()`**: Invokes the function immediately and accepts arguments individually.
  ```javascript
  greet.call(obj, arg1, arg2);
  ```
- **`apply()`**: Invokes the function immediately and accepts arguments as an array.
  ```javascript
  greet.apply(obj, [arg1, arg2]);
  ```
- **`bind()`**: Returns a new function with `this` bound to the specified object.
  ```javascript
  const boundGreet = greet.bind(obj);
  boundGreet(arg1, arg2);
  ```

#### **5.3.`hoisting` in JavaScript.**
- Variables and functions are **hoisted** to the top of their scope during compilation.
- Only the declarations are hoisted, not the initializations.
- Example:
  ```javascript
  console.log(a); // undefined
  var a = 5;
  ```

---