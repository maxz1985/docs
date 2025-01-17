# React

### **1. React**

#### **Key Points to Highlight**:
1. React is a **JavaScript library** for building **user interfaces**. It is **component-based** and maintained by Facebook.

2. **Key Features**:
    - **Declarative**:
        - React makes it easy to describe what the UI should look like for a given state, and React efficiently updates and renders the UI when the state changes.
    - **Component-Based Architecture**:
        - UI is built using reusable, self-contained components.
    - **Virtual DOM**:
        - React uses a Virtual DOM to improve performance by minimizing direct manipulation of the real DOM.
    - **Unidirectional Data Flow**:
        - Data flows from parent to child components, making it easier to understand and debug.
    - **Hooks**:
        - Introduced in React 16.8, hooks allow functional components to manage state and lifecycle events.
    - **JSX**:
        - A syntax extension for JavaScript that allows HTML-like syntax in JavaScript code.

- **Difference between React and other front-end frameworks (for example, Angular, Vue)?**
    - React is a library, not a full framework.
    - React focuses on UI, while Angular and Vue include built-in solutions for routing and state management.

### **2. React Hooks**

- **Hooks** are functions that let you use **state** and **other React features** (like lifecycle methods) in functional components.

#### **Commonly Used Hooks**:
1. **`useState`**:
    - Allows you to add state to functional components.
    - Example:
      ```jsx
      import React, { useState } from "react";
 
      function Counter() {
        const [count, setCount] = useState(0);
 
        return (
          <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
          </div>
        );
      }
      ```

2. **`useEffect`**:
    - Allows you to perform side effects (for example, data fetching, subscriptions).
    - Example:
      ```jsx
      import React, { useState, useEffect } from "react";
 
      function Timer() {
        const [time, setTime] = useState(0);
 
        useEffect(() => {
          const interval = setInterval(() => {
            setTime((prevTime) => prevTime + 1);
          }, 1000);
 
          return () => clearInterval(interval); // Cleanup
        }, []);
 
        return <p>Time: {time}s</p>;
      }
      ```

3. **`useContext`**:
    - Used to consume a context value without a wrapper component.
    - Example:
      ```jsx
      const ThemeContext = React.createContext("light");
 
      function App() {
        return (
          <ThemeContext.Provider value="dark">
            <Toolbar />
          </ThemeContext.Provider>
        );
      }
 
      function Toolbar() {
        const theme = React.useContext(ThemeContext);
        return <p>Current theme: {theme}</p>;
      }
      ```

---

#### **Why Are Hooks Useful?**
1. Simplify component logic by removing the need for class components.
2. Allow state and side effects to be used in functional components.
3. Make it easy to share and reuse logic (via custom hooks).

### **3. Virtual DOM**

- The **Virtual DOM** is an in-memory representation of the real DOM. It allows React to determine the most efficient way to update the UI without directly manipulating the real DOM.

#### **How It Works**:
1. When a React component’s state or props change, React updates the Virtual DOM first.
2. React compares the updated Virtual DOM with the previous one (using a diffing algorithm).
3. React calculates the minimum number of changes required to update the real DOM.
4. The real DOM is updated efficiently.

---

#### **Why Does the Virtual DOM Improve Performance?**
1. **Batch Updates**:
    - React groups multiple DOM updates into a single operation, reducing unnecessary re-flows and repaints.
2. **Efficient Diffing**:
    - React compares the Virtual DOM trees using an optimized algorithm to identify minimal changes.
3. **Abstraction**:
    - Developers don’t need to write manual DOM manipulation code, which can be error-prone and slow.

---

- Angular and Vue also have virtual DOM implementations, but React popularized the concept.

---

---

### **4. How does React handle state management?**

#### **State in React**:
- **Local State**:
    - Managed using `useState` or `this.setState` (in class components).

- **Global State**:
    - Managed across components using **Context API** or external libraries like **Redux**.

---

#### **When to Use Context API vs. Redux?**
| **Feature**        | **Context API**                           | **Redux**                                        |
|--------------------|-------------------------------------------|--------------------------------------------------|
| **Use Case**       | Simple state sharing (e.g., theme, auth). | Complex state with multiple reducers.            |
| **Performance**    | Context can trigger unnecessary renders.  | Optimized with middleware and selectors.         |
| **Learning Curve** | Easier to use and set up.                 | Steeper learning curve due to extra boilerplate. |

---

#### **Example: Context API**
```jsx
const AuthContext = React.createContext();

function App() {
  return (
    <AuthContext.Provider value={{ user: "John Doe" }}>
      <Navbar />
    </AuthContext.Provider>
  );
}

function Navbar() {
  const auth = React.useContext(AuthContext);
  return <p>Logged in as: {auth.user}</p>;
}
```

- To optimize performance when using Context API, Split contexts to reduce unnecessary re-renders.

---

---

### **5. Lifecycle methods**

#### **Lifecycle Phases**:
1. **Mounting**:
    - When a component is created and added to the DOM.
    - Methods:
        - `componentDidMount` (class components).
        - `useEffect` with an empty dependency array for functional components.

2. **Updating**:
    - When a component’s state or props change.
    - Methods:
        - `componentDidUpdate`.
        - `useEffect` with dependencies.

3. **Unmounting**:
    - When a component is removed from the DOM.
    - Methods:
        - `componentWillUnmount`.
        - Cleanup function in `useEffect`.

#### **Example (Class Component)**:
```jsx
class Example extends React.Component {
  componentDidMount() {
    console.log("Component mounted");
  }

  componentDidUpdate(prevProps) {
    console.log("Component updated");
  }

  componentWillUnmount() {
    console.log("Component will unmount");
  }

  render() {
    return <p>Hello, world!</p>;
  }
}
```

#### **Example (Functional Component)**:
```jsx
function Example() {
  React.useEffect(() => {
    console.log("Component mounted");

    return () => {
      console.log("Component will unmount");
    };
  }, []);

  return <p>Hello, world!</p>;
}
```

---

---

### **6. Higher-order components (HOCs) in React**

#### **What Are HOCs?**
- HOCs are **functions that take a component as input** and return a new component with additional functionality.

---

#### **Use Cases**:
1. Reuse logic across components.
2. Add features like authentication, logging, or styling.

---

#### **Example**:
```jsx
function withLogger(WrappedComponent) {
  return function Logger(props) {
    console.log("Rendering component:", WrappedComponent.name);
    return <WrappedComponent {...props} />;
  };
}

function HelloWorld() {
  return <p>Hello, World!</p>;
}

const EnhancedComponent = withLogger(HelloWorld);
```

- Alternatives to HOCs are **Render props** and **custom hooks**.
