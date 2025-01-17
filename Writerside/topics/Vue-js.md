# Vue.js

### **1. Vue.js**

- Vue.js is a **progressive JavaScript framework** used for building **user interfaces** and **single-page applications (SPAs)**.
- It is reactive, component-based, and easy to integrate with other projects.
- Vue.js follows the MVVM pattern while React focused on UI components.
- Uses Vuex for state management while React uses Redux or state flows down or lifting state

1. **Key Features**:
    - **Reactivity System**:
        - Automatically updates the DOM when the state changes.
        - Example:
          ```javascript
          const app = Vue.createApp({
            data() {
              return { message: "Hello, Vue!" };
            },
          });
          app.mount("#app");
          ```

    - **Component-Based Architecture**:
        - The UI is divided into reusable, self-contained components.
        - Example:
          ```javascript
          Vue.component('my-component', {
            template: '<p>This is a component!</p>'
          });
          ```

    - **Directives**:
        - Special attributes for DOM manipulation.
        - Examples: `v-if`, `v-for`, `v-bind`, `v-model`.

    - **Two-Way Data Binding**:
        - Synchronizes data between the model and the view using `v-model`.
        - Example:
          ```html
          <input v-model="message" />
          <p>{{ message }}</p>
          ```

    - **Transition/Animation System**:
        - Built-in support for transitions and animations.

    - **Vue Router and Vuex**:
        - Vue Router: For client-side routing.
        - Vuex: For state management.

---

- Vue.js is easier to learn and provides better integration capabilities.
- React requires more libraries for state management and routing, while Angular is a full-fledged framework.

---

---

### **2. Vue.js reactivity system**

- The Vue.js reactivity system allows the DOM to update automatically when the underlying state changes.

#### **How It Works**:
1. **Proxy-Based Reactivity (Vue 3)**:
    - Vue 3 uses ES6 **Proxies** to detect changes to objects and arrays.
    - Example:
      ```javascript
      const state = Vue.reactive({ count: 0 });
      console.log(state.count); // 0
      state.count++;
      console.log(state.count); // 1
      ```

2. **Data Observation (Vue 2)**:
    - In Vue 2, reactivity is implemented by converting properties into **getters** and **setters** during initialization.
    - Example:
      ```javascript
      const vm = new Vue({
        data: { count: 0 }
      });
      console.log(vm.count); // 0
      vm.count++;
      console.log(vm.count); // 1
      ```

#### **Reactivity Limitations**:
- Vue 2 has issues with detecting property addition/removal and changes to array indexes:
  ```javascript
  Vue.set(array, index, value); // Fixes array reactivity
  ```

---

- Vue 3 uses Proxies, which are faster and more flexible than getters/setters.

---

---

### **3. Vue.js directives**

- Directives are special **HTML attributes** that provide instructions to the DOM.

#### **Commonly Used Directives**:
| **Directive**   | **Description**                       | **Example**                                   |
|-----------------|---------------------------------------|-----------------------------------------------|
| `v-bind`        | Binds attributes dynamically.         | `<img v-bind:src="imageSrc" />`               |
| `v-if`          | Conditionally renders elements.       | `<p v-if="isVisible">Hello</p>`               |
| `v-for`         | Loops through arrays or objects.      | `<li v-for="item in items">{{ item }}</li>`   |
| `v-model`       | Two-way data binding for form inputs. | `<input v-model="message" />`                 |
| `v-on` (or `@`) | Listens to DOM events.                | `<button @click="doSomething">Click</button>` |

---

#### **Using Directives**
```html
<div id="app">
  <p v-if="isVisible">Hello, Vue!</p>
  <ul>
    <li v-for="item in items" :key="item.id">{{ item.name }}</li>
  </ul>
  <input v-model="message" />
  <button @click="showMessage">Submit</button>
</div>

<script>
const app = Vue.createApp({
  data() {
    return {
      isVisible: true,
      items: [{ id: 1, name: "Item 1" }, { id: 2, name: "Item 2" }],
      message: "",
    };
  },
  methods: {
    showMessage() {
      alert(this.message);
    },
  },
});
app.mount("#app");
</script>
```

---

- **Custom directives in Vue.js**
    - Use the `directive` function:
      ```javascript
      Vue.directive("focus", {
        inserted(el) {
          el.focus();
        },
      });
      ```

---

---

### **4. Vue components**

- Components are **reusable building blocks** in Vue.js that encapsulate UI and logic.

---

#### **Example of a Component**:
1. **Define the Component**:
   ```javascript
   Vue.component("hello-world", {
     template: "<p>Hello, {{ name }}!</p>",
     data() {
       return { name: "Vue" };
     },
   });
   ```

2. **Use the Component**:
   ```html
   <hello-world></hello-world>
   ```

---

#### **Advantages of Components**:
1. Reusability.
2. Better organization of code.
3. Encapsulation of logic and styles.

---

- Global components are registered with `Vue.component` and can be used anywhere.
- Local components are registered in a specific component or app instance.

---

---

### *5. Vuex*

- Vuex is Vue.js's official **state management library**.
- It provides a centralized store to manage the application's shared state.

---

#### **Core Concepts of Vuex**:
1. **State**:
    - The shared data across components.
    - Example:
      ```javascript
      const store = new Vuex.Store({
        state: {
          count: 0,
        },
      });
      ```

2. **Mutations**:
    - Synchronous methods to update the state.
    - Example:
      ```javascript
      mutations: {
        increment(state) {
          state.count++;
        },
      },
      ```

3. **Actions**:
    - Asynchronous operations that commit mutations.
    - Example:
      ```javascript
      actions: {
        incrementAsync({ commit }) {
          setTimeout(() => commit("increment"), 1000);
        },
      },
      ```

4. **Getters**:
    - Computed properties for the state.
    - Example:
      ```javascript
      getters: {
        doubleCount: (state) => state.count * 2,
      },
      ```

5. **Modules**:
    - Divide the store into smaller, manageable pieces.

---

- Use Vuex when managing shared state in larger applications.

---

---

### **6. Vue Router**

- Vue Router is the official **routing library** for Vue.js.
- It allows you to build **single-page applications (SPAs)** with navigation.

---

#### **Key Features**:
1. **Dynamic Routing**:
    - Define routes that render specific components.
    - Example:
      ```javascript
      const routes = [
        { path: "/home", component: Home },
        { path: "/about", component: About },
      ];
      ```

2. **Route Parameters**:
    - Pass parameters in URLs.
    - Example:
      ```javascript
      { path: "/user/:id", component: User },
      ```

3. **Navigation Guards**:
    - Protect routes or execute logic before navigating.

4. **Nested Routes**:
    - Define child routes.

---

- **Navigation guards**
    - Example:
      ```javascript
      router.beforeEach((to, from, next) => {
        if (to.meta.requiresAuth && !isAuthenticated) {
          next("/login");
        } else {
          next();
        }
      });
      ```

---

---
