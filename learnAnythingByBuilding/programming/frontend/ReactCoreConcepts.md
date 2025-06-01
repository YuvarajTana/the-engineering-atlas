# React core fundamentals and use cases

## **1. How React Works Under the Hood**
React’s core idea is to provide a declarative, component-based way to build UIs while minimizing direct DOM manipulations. Under the hood, React maintains a Virtual DOM (a lightweight, in-memory representation of the real DOM). Whenever your component’s state or props change, React:

* Re-renders the component tree into a new Virtual DOM tree.
* Diffs the new Virtual DOM against the previous Virtual DOM (reconciliation).
* Computes the minimal set of changes needed to update the real DOM.
* Patches only those specific parts of the real DOM (batched for performance).

```jsx
// Sample: A simple counter illustrating Virtual DOM updates
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  // On each click, React reruns Counter() → new Virtual DOM
  // It then diffs against previous Virtual DOM and patches only the text node.
  return (
    <div>
      <p>Current count: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
    </div>
  );
}
```

> **When to care:** Understanding Virtual DOM helps you reason about why React is usually fast (batched updates, minimal DOM writes).
> **When not to care:** Unless you’re debugging a performance bottleneck, you don’t need to manually “optimize” Virtual DOM. React handles reconciliation for you.

---

## 2. Rendering Process

Every React component goes through **three main phases**:

1. **Mounting** (insertion into the DOM):

   * Constructor (class) or initial render (functional)
   * `componentDidMount()` or `useEffect(() ⇒ …, [])` afterward
2. **Updating** (state/props changes):

   * Should component rerender? (`shouldComponentUpdate`/`React.memo`)
   * `componentDidUpdate()` or `useEffect` with dependencies
3. **Unmounting** (removal from the DOM):

   * `componentWillUnmount()` or cleanup function in `useEffect`

```jsx
import React, { useState, useEffect } from 'react';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  // On Mount: start interval
  // On Update: re-run effect only if `seconds` changes (but interval isn’t recreated due to empty deps)
  // On Unmount: cleanup to avoid memory leaks
  useEffect(() => {
    const id = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
    return () => clearInterval(id); // cleanup
  }, []);

  return <p>Timer: {seconds}s</p>;
}
```

> **When to use this knowledge:** When you need to manage side effects (e.g., subscriptions, timers, network requests) tied to lifecycle phases.
> **When not to use:** If your component is “stateless” or purely presentational, you may not need to hook into any lifecycle.

---

## 3. Rendering Lists and Conditional Operators

### Rendering Lists

When you have an array of data, you typically use `.map()` to convert each item into a React element. **Always** supply a `key` prop to help React identify which items changed:

```jsx
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}

// Usage
const todos = [
  { id: 1, text: 'Learn React' },
  { id: 2, text: 'Build a to-do app' },
  { id: 3, text: 'Profit!' },
];
<TodoList todos={todos} />;
```

> **Key pitfalls:**
>
> * Using array index as key can cause unnecessary re-rendering or state loss when items reorder or splice.
> * Always prefer a unique, stable identifier (e.g., database ID).

### Conditional Rendering

React lets you conditionally render parts of the UI. Common patterns:

1. **Logical AND (`&&`)** for simple show/hide:

   ```jsx
   function UserGreeting({ user }) {
     return (
       <div>
         <h1>Welcome back!</h1>
         {user.isPremium && <p>Thanks for being a Premium member 🎉</p>}
       </div>
     );
   }
   ```
2. **Ternary (`? :`)** for binary conditions:

   ```jsx
   function LoginControl({ isLoggedIn }) {
     return (
       <div>
         {isLoggedIn ? (
           <button>Logout</button>
         ) : (
           <button>Login</button>
         )}
       </div>
     );
   }
   ```
3. **Inline `if…else` via helper function** (when logic is complex):

   ```jsx
   function StatusMessage({ status }) {
     const renderMessage = () => {
       if (status === 'loading') return <p>Loading…</p>;
       if (status === 'error') return <p>Error occurred.</p>;
       return <p>Data loaded.</p>;
     };

     return <div>{renderMessage()}</div>;
   }
   ```

> **When to use:** Whenever parts of your UI depend on data or state.
> **When not to abuse:** If you find deeply nested ternaries or multiple `&&` chains making JSX unreadable, factor logic out into helper functions or separate components.

---

## 4. Using `map`, `filter`, and `reduce` in React

These array methods help transform data into UI or drive logic inside components:

* **`map`** transforms each item into JSX (most common for lists).
* **`filter`** narrows down an array before rendering.
* **`reduce`** aggregates data, e.g., adding totals, grouping items.

```jsx
function ShoppingCart({ cartItems }) {
  // 1. Filter out items that are no longer in stock
  const availableItems = cartItems.filter(item => item.inStock);

  // 2. Sum total price using reduce
  const totalPrice = availableItems.reduce(
    (sum, item) => sum + item.price * item.quantity,
    0
  );

  return (
    <div>
      <h2>Cart Items</h2>
      <ul>
        {availableItems.map(item => (
          <li key={item.id}>
            {item.name} x {item.quantity} = ₹{item.price * item.quantity}
          </li>
        ))}
      </ul>
      <p>
        <strong>Total:</strong> ₹{totalPrice}
      </p>
    </div>
  );
}

// Example usage:
const cartItems = [
  { id: 'a1', name: 'T-shirt', price: 499, quantity: 2, inStock: true },
  { id: 'b2', name: 'Jeans', price: 999, quantity: 1, inStock: false },
  { id: 'c3', name: 'Shoes', price: 1299, quantity: 1, inStock: true },
];
<ShoppingCart cartItems={cartItems} />;
```

> **When to use:**
>
> * `map`: Rendering lists of components.
> * `filter`: Rendering a subset based on some condition (e.g., search, status).
> * `reduce`: Computing totals, grouping by categories, deriving summary data.
>   **When not to use:** If your transformation is very complex (e.g., heavy computations or nested loops), consider precomputing outside the render or memoizing.

---

## 5. Conditional Operators in React

**Conditional operators** (already touched in section 3) ensure your JSX remains declarative. Key patterns:

1. **`&&` Operator (Render if true)**

   ```jsx
   <div>
     {isAdmin && <button>Delete User</button>}
   </div>
   ```
2. **Ternary (`? :`)**

   ```jsx
   <div>
     {hasNotifications ? (
       <NotificationList items={notifications} />
     ) : (
       <p>No new notifications</p>
     )}
   </div>
   ```
3. **Null Coalescing with `||`** (less common for rendering):

   ```jsx
   <div>{username || 'Guest'}</div>
   ```

> **When to prefer one over another:**
>
> * Use `&&` for simple “render or don’t render” cases.
> * Use ternary if you need to render one of two entirely different elements.
> * Avoid nesting ternaries deeply—abstract them into helper functions or subcomponents.

---

## 6. Components in React — Class Components vs. Functional Components

### Class Components

Before React 16.8 (Hooks), most stateful logic lived in class components:

```jsx
import React from 'react';

class Profile extends React.Component {
  constructor(props) {
    super(props);
    this.state = { age: 30 };
    this.incrementAge = this.incrementAge.bind(this);
  }

  incrementAge() {
    this.setState(prev => ({ age: prev.age + 1 }));
  }

  componentDidMount() {
    console.log('Profile mounted');
  }

  componentDidUpdate(_, prevState) {
    if (prevState.age !== this.state.age) {
      console.log('Age updated:', this.state.age);
    }
  }

  componentWillUnmount() {
    console.log('Cleaning up Profile');
  }

  render() {
    return (
      <div>
        <h2>{this.props.name}</h2>
        <p>Age: {this.state.age}</p>
        <button onClick={this.incrementAge}>+1</button>
      </div>
    );
  }
}
```

> **When to use:**
>
> * Today, almost never start a new component as a class. Classes remain for legacy code.
>   **When not to use:**
> * New projects should use functional components with Hooks. They’re shorter, easier to test, and avoid `this` binding pitfalls.

### Functional Components

Since Hooks, these are the standard way to build components:

```jsx
import React, { useState, useEffect } from 'react';

function Profile({ name }) {
  const [age, setAge] = useState(30);

  useEffect(() => {
    console.log('Profile mounted');
    return () => console.log('Cleaning up Profile');
  }, []);

  useEffect(() => {
    console.log('Age updated:', age);
  }, [age]);

  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <button onClick={() => setAge(a => a + 1)}>+1</button>
    </div>
  );
}
```

> **When to use:**
>
> * Always for new code. Combine multiple Hooks (state, effects, context, etc.) easily.
>   **When not to use:**
> * Only avoid them if your environment doesn’t support React 16.8+ (rare).

---

## 7. State vs. Props in Components

* **Props**: Data passed *down* from a parent to a child. Read-only inside the child.
* **State**: Data *owned* by the component. Can be updated via `setState` (classes) or `useState` (functions).

```jsx
function Parent() {
  const [username, setUsername] = useState('Yuvi');

  return (
    <div>
      <Child name={username} onChangeName={setUsername} />
    </div>
  );
}

function Child({ name, onChangeName }) {
  // `name` is a prop, read-only. `onChangeName` is a prop callback.
  return (
    <div>
      <p>Username: {name}</p>
      <button onClick={() => onChangeName('NewName')}>Change Name</button>
    </div>
  );
}
```

> **Use cases:**
>
> * **Props**: Use whenever data flows from a parent. Ideal for configuring a child component.
> * **State**: Use for values that can change due to user interaction, network responses, timers, etc.
>   **Pitfalls:**
> * Don’t try to mutate props! If you need to change something, lift state up so the parent can update props.
> * Excessive local state can make components hard to reason about—consider using context or Redux for deeply shared state.

---

## 8. Different Types of React Components

1. **Presentational (Stateless) vs. Container (Stateful)**

   * *Presentational*: Focus on markup/CSS, accept data via props, rarely have their own state.
   * *Container*: Manage data fetching, state, and pass props to presentational components.

2. **Pure Components**

   * Class-based: `PureComponent` does a shallow prop/state comparison in `shouldComponentUpdate`.
   * Functional: Wrap with `React.memo` to avoid re-render if props don’t change shallowly.

3. **Higher-Order Components (HOC)**

   * Functions that accept a component and return a new component with extra props or behavior.
   * Example: `withRouter`, `connect` (Redux).

   ```jsx
   function withLogger(WrappedComponent) {
     return function Logger(props) {
       console.log('Rendering with props:', props);
       return <WrappedComponent {...props} />;
     };
   }
   ```

4. **Controlled vs. Uncontrolled Components**

   * *Controlled*: Form inputs whose values are entirely driven by React state (`value={stateValue}`).
   * *Uncontrolled*: Use refs to read input values from the DOM (small forms, quick prototypes).

5. **Error Boundary Components**

   * Class components implementing `static getDerivedStateFromError()` and `componentDidCatch()` to catch rendering errors in descendants. Functional equivalents don’t exist yet (need to be class).

> **When to use each:**
>
> * Presentational/Container: Enforce separation of concerns, easier testing.
> * Pure Components / `React.memo`: Performance optimization when child props rarely change.
> * HOCs: Reuse logic (e.g., theming, data fetching) across multiple components.
> * Controlled inputs: Whenever you need instant validation or to tie input to state.
> * Uncontrolled inputs: Simple forms where you only read the final value on submit.
> * Error Boundaries: Wrap areas where you suspect unpredictable errors (e.g., 3rd-party widgets).

---

## 9. React Hooks Deep Dive

React Hooks let functional components “hook into” state, lifecycle, context, etc. Hooks must be called at the top level of a component and only in React functions.

### `useState`

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0); // initial value 0

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(prev => prev + 1)}>Increment</button>
    </div>
  );
}
```

* **Use cases:** Any time you need component-local state.
* **When not to use:** For global or cross-cutting state—consider context or Redux.

### `useEffect` (and Polyfill)

Runs side effects after render.

```jsx
import React, { useState, useEffect } from 'react';

function DataFetcher({ url }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    let cancelled = false;
    fetch(url)
      .then(res => res.json())
      .then(json => {
        if (!cancelled) setData(json);
      });
    return () => {
      // cleanup on unmount or before next effect run
      cancelled = true;
    };
  }, [url]); // Re-run when `url` changes

  return <div>{data ? JSON.stringify(data) : 'Loading…'}</div>;
}
```

* **Mount:** If dependency array is `[]`, effect runs once after first render.
* **Update:** If you specify variables in `[dep1, dep2]`, effect re-runs whenever they change.
* **Cleanup:** Return a function for teardown (e.g., unsubscribing, clearing timers).

#### Polyfill (class-based equivalent)

```jsx
class DataFetcher extends React.Component {
  state = { data: null };
  _isCancelled = false;

  componentDidMount() {
    this.fetchData(this.props.url);
  }

  componentDidUpdate(prev) {
    if (prev.url !== this.props.url) {
      this.fetchData(this.props.url);
    }
  }

  componentWillUnmount() {
    this._isCancelled = true;
  }

  fetchData(url) {
    this._isCancelled = false;
    fetch(url)
      .then(res => res.json())
      .then(json => {
        if (!this._isCancelled) {
          this.setState({ data: json });
        }
      });
  }

  render() {
    return <div>{this.state.data ? JSON.stringify(this.state.data) : 'Loading…'}</div>;
  }
}
```

> **When to use:** Fetching data, subscriptions, timers, updating the document title, manual DOM manipulations.
> **When to avoid:** If effect logic is purely synchronous rendering logic—keep as pure JSX.

### `useRef`

Gives you a mutable ref object whose `.current` persists across renders without causing re-renders.

```jsx
import React, { useRef, useEffect } from 'react';

function TextInputWithFocusButton() {
  const inputRef = useRef(null);

  useEffect(() => {
    // Imperatively focus the input on mount
    inputRef.current.focus();
  }, []);

  return (
    <div>
      <input ref={inputRef} placeholder="Type something…" />
      <button onClick={() => inputRef.current.select()}>Select Text</button>
    </div>
  );
}
```

* **Use cases:**

  * DOM element access (focus, measure).
  * Storing mutable values that don’t trigger re-render (e.g., `setInterval` IDs, previous props).
* **When not to use:** As a substitute for state if you want changes to reflect in UI.

### `useContext`

Consumes a React Context to avoid prop drilling.

```jsx
import React, { createContext, useContext, useState } from 'react';

// Create context
const ThemeContext = createContext({ theme: 'light', toggle: () => {} });

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  const toggle = () => setTheme(t => (t === 'light' ? 'dark' : 'light'));

  return (
    <ThemeContext.Provider value={{ theme, toggle }}>
      {children}
    </ThemeContext.Provider>
  );
}

function ThemedButton() {
  const { theme, toggle } = useContext(ThemeContext);
  return (
    <button
      onClick={toggle}
      style={{
        background: theme === 'dark' ? '#333' : '#eee',
        color: theme === 'dark' ? '#eee' : '#333',
      }}
    >
      Switch Theme
    </button>
  );
}

// App
function App() {
  return (
    <ThemeProvider>
      <ThemedButton />
    </ThemeProvider>
  );
}
```

> **When to use:**
>
> * Passing down data (themes, auth info, locale) that multiple nested components need.
>   **When not to use:**
> * For large, frequently updating state—context updates re-render all consumers. Consider `useMemo`/`useReducer` or Redux if performance suffers.

### `useReducer`

An alternative to `useState` when state logic is complex or next state depends on previous state.

```jsx
import React, { useReducer } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
    </>
  );
}
```

> **When to use:**
>
> * State with multiple sub-values or when next state depends on past state.
> * Reusable state logic (move `reducer` + initial state into a separate file).
>   **When not to use:**
> * For very simple toggles or individual primitives; `useState` is more concise.

### `useMemo` and `useCallback` (and Polyfill)

* **`useMemo`** memoizes the result of an expensive computation.
* **`useCallback`** memoizes a callback function, so it only changes if its dependencies change.

```jsx
import React, { useState, useMemo, useCallback } from 'react';

function ExpensiveComponent({ compute, value }) {
  const result = useMemo(() => compute(value), [compute, value]);
  // `compute(value)` only re-runs when `value` or `compute` changes

  return <p>Computed: {result}</p>;
}

function Parent() {
  const [num, setNum] = useState(0);
  const [toggle, setToggle] = useState(false);

  const compute = useCallback(
    n => {
      // simulate heavy computation
      let i = 0;
      while (i < 1000000000) i++;
      return n * 2;
    },
    [] // same function reference across renders
  );

  return (
    <div>
      <ExpensiveComponent compute={compute} value={num} />
      <button onClick={() => setNum(n => n + 1)}>Increment Number</button>
      <button onClick={() => setToggle(t => !t)}>
        Toggle: {toggle ? 'ON' : 'OFF'}
      </button>
    </div>
  );
}
```

#### Polyfill Using `useRef` & `useEffect`

To mimic `useCallback` in older React versions, you’d manually store the function reference in a ref when dependencies change:

```jsx
import React, { useRef, useEffect, useState } from 'react';

function ParentOld() {
  const [num, setNum] = useState(0);
  const [toggle, setToggle] = useState(false);

  const computeRef = useRef(); 

  useEffect(() => {
    computeRef.current = n => {
      let i = 0;
      while (i < 1000000000) i++;
      return n * 2;
    };
  }, []); // would need more logic if compute depended on props

  // Now computeRef.current always has the same function reference

  const result = computeRef.current(num);

  return (
    <div>
      <p>Computed: {result}</p>
      <button onClick={() => setNum(n => n + 1)}>Increment Number</button>
      <button onClick={() => setToggle(t => !t)}>
        Toggle: {toggle ? 'ON' : 'OFF'}
      </button>
    </div>
  );
}
```

> **When to use:**
>
> * **`useMemo`**: Only if a computation is genuinely expensive or prevents passing stable values to deep children.
> * **`useCallback`**: When passing callbacks to optimized child components that rely on referential equality (`React.memo`, dependency arrays).
>   **When not to use:**
> * Unnecessary memoization can bloat code and add overhead. Use profiling first.

### `useImperativeHandle`

Allows you to customize which instance values are exposed to parent components via `ref`. Typically used with `forwardRef`:

```jsx
import React, {
  useImperativeHandle,
  useRef,
  forwardRef,
  useState,
} from 'react';

const FancyInput = forwardRef((props, ref) => {
  const inputRef = useRef(null);

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    },
    clear: () => {
      inputRef.current.value = '';
    },
  }));

  return <input ref={inputRef} {...props} />;
});

function Parent() {
  const fancyInputRef = useRef(null);

  return (
    <div>
      <FancyInput ref={fancyInputRef} />
      <button onClick={() => fancyInputRef.current.focus()}>Focus</button>
      <button onClick={() => fancyInputRef.current.clear()}>Clear</button>
    </div>
  );
}
```

> **When to use:**
>
> * Exposing imperative methods (e.g., `focus()`, `scrollIntoView()`) from a child.
>   **When not to use:**
> * Prefer declarative data flow. Use only if imperative control is absolutely necessary.

---

## 10. Custom Hooks

Custom hooks are simply functions whose names start with “use” and internally call other Hooks. They let you extract and reuse stateful logic.

### **What Are Custom Hooks?**

* Encapsulate reusable logic (e.g., data fetching, subscriptions, form handling).
* Return any value (state, functions, refs, etc.).
* Follow the same rules as built-in Hooks (call at top level, only from React functions).

### **`useWindowSize`**

Tracks viewport dimensions:

```jsx
import { useState, useEffect } from 'react';

function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    const handleResize = () => {
      setSize({ width: window.innerWidth, height: window.innerHeight });
    };
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
}

// Usage
function App() {
  const { width, height } = useWindowSize();
  return (
    <p>
      Width: {width}px, Height: {height}px
    </p>
  );
}
```

> **When to use:** Responsive components needing to know current window size.
> **When not to use:** If your layout can rely on CSS media queries; avoid over-using JS for purely visual adjustments.

### **`useFetch`**

Generalized data fetching hook:

```jsx
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData] = useState(null);
  const [status, setStatus] = useState('idle'); // 'idle' | 'fetching' | 'error' | 'success'
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;
    let isCancelled = false;
    setStatus('fetching');

    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error('Network response was not ok');
        return res.json();
      })
      .then(json => {
        if (!isCancelled) {
          setData(json);
          setStatus('success');
        }
      })
      .catch(err => {
        if (!isCancelled) {
          setError(err);
          setStatus('error');
        }
      });

    return () => {
      isCancelled = true;
    };
  }, [url]);

  return { data, status, error };
}

// Usage
function PostList() {
  const { data: posts, status, error } = useFetch(
    'https://jsonplaceholder.typicode.com/posts'
  );

  if (status === 'idle' || status === 'fetching') return <p>Loading…</p>;
  if (status === 'error') return <p>Error: {error.message}</p>;

  return (
    <ul>
      {posts.map(post => (
        <li key={post.id}>{post.title}</li>
      ))}
    </ul>
  );
}
```

> **When to use:** Standardize fetching logic across multiple components.
> **When not to use:** If you need advanced caching, pagination, or real-time updates—consider libraries like React Query or SWR.

### **`useDebounce`**

Debounce a rapidly changing value (e.g., search input):

```jsx
import { useState, useEffect } from 'react';

function useDebounce(value, delay = 300) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(handler);
  }, [value, delay]);

  return debouncedValue;
}

// Usage: Debounce search input
function SearchBox() {
  const [term, setTerm] = useState('');
  const debouncedTerm = useDebounce(term, 500);

  useEffect(() => {
    if (debouncedTerm) {
      // fetch search results for debouncedTerm
    }
  }, [debouncedTerm]);

  return (
    <input
      value={term}
      onChange={e => setTerm(e.target.value)}
      placeholder="Search…"
    />
  );
}
```

> **When to use:** Avoid flooding APIs or expensive computations with every keystroke.
> **When not to use:** If you need immediate feedback (e.g., a real-time chat), debounce would introduce undesirable delay.

### **`useLocalStorage`**

Sync state with `localStorage`:

```jsx
import { useState, useEffect } from 'react';

function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item !== null ? JSON.parse(item) : initialValue;
    } catch (err) {
      console.error('useLocalStorage read error:', err);
      return initialValue;
    }
  });

  const setValue = value => {
    try {
      const valueToStore =
        value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (err) {
      console.error('useLocalStorage set error:', err);
    }
  };

  return [storedValue, setValue];
}

// Usage
function DarkModeToggle() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  return (
    <button onClick={() => setTheme(t => (t === 'light' ? 'dark' : 'light'))}>
      Theme: {theme}
    </button>
  );
}
```

> **When to use:** Persistent preferences (theme, language, tokens).
> **When not to use:** For large data sets—`localStorage` is synchronous and can block the main thread.

### **`useIntersectionObserver`**

Detect when an element enters or leaves the viewport:

```jsx
import { useState, useEffect, useRef } from 'react';

function useIntersectionObserver(options) {
  const [entry, setEntry] = useState(null);
  const observerRef = useRef(null);
  const elementRef = useRef(null);

  useEffect(() => {
    observerRef.current = new IntersectionObserver(
      ([observedEntry]) => setEntry(observedEntry),
      options
    );

    const { current: el } = elementRef;
    const { current: observer } = observerRef;

    if (el) observer.observe(el);
    return () => {
      if (el) observer.unobserve(el);
    };
  }, [options]);

  return [elementRef, entry];
}

// Usage: Lazy-loading images or infinite scrolling
function LazyImage({ src, alt }) {
  const [ref, entry] = useIntersectionObserver({ threshold: 0.1 });
  const isVisible = entry?.isIntersecting;

  return (
    <img
      ref={ref}
      src={isVisible ? src : ''}
      alt={alt}
      style={{ minHeight: '200px', background: '#eee' }}
    />
  );
}
```

> **When to use:** Lazy-loading images, infinite scroll, tracking element visibility (e.g., analytics).
> **When not to use:** If your target browsers don’t support `IntersectionObserver` and you can’t polyfill, fallback to scroll events (more expensive).

---

## 11. Implementing Light and Dark Mode

A typical approach uses Context + CSS Custom Properties (variables):

```jsx
// theme.js
export const lightTheme = {
  '--bg-color': '#ffffff',
  '--text-color': '#333333',
};
export const darkTheme = {
  '--bg-color': '#1a1a1a',
  '--text-color': '#f5f5f5',
};

// ThemeContext.js
import React, { createContext, useContext, useState, useEffect } from 'react';
import { lightTheme, darkTheme } from './theme';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [mode, setMode] = useState(
    () => window.localStorage.getItem('theme') || 'light'
  );

  const toggleTheme = () =>
    setMode(prev => (prev === 'light' ? 'dark' : 'light'));

  // Apply CSS variables to <html> or <body>
  useEffect(() => {
    const theme = mode === 'light' ? lightTheme : darkTheme;
    Object.entries(theme).forEach(([varName, varValue]) => {
      document.documentElement.style.setProperty(varName, varValue);
    });
    window.localStorage.setItem('theme', mode);
  }, [mode]);

  return (
    <ThemeContext.Provider value={{ mode, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  return useContext(ThemeContext);
}
```

```jsx
// App.jsx
import React from 'react';
import { ThemeProvider, useTheme } from './ThemeContext';

function ThemedApp() {
  const { mode, toggleTheme } = useTheme();
  return (
    <div
      style={{
        backgroundColor: 'var(--bg-color)',
        color: 'var(--text-color)',
        minHeight: '100vh',
        padding: '2rem',
      }}
    >
      <h1>{mode === 'light' ? 'Light Mode' : 'Dark Mode'}</h1>
      <button onClick={toggleTheme}>Toggle Theme</button>
      <p>This is a sample text to demonstrate theme colors.</p>
    </div>
  );
}

export default function App() {
  return (
    <ThemeProvider>
      <ThemedApp />
    </ThemeProvider>
  );
}
```

> **Best Practices:**
>
> * Store preference in `localStorage` or respect user’s OS preference via `prefers-color-scheme` media query.
> * Keep your CSS variables consistent (e.g., `--bg-color`, `--text-color`, `--link-color`).
>   **When not to use:**
> * If your design is very minimal and you can rely on CSS variables only, you could skip context and toggle by adding a class on `<body>`.

---

## 12. Routing in React with React Router DOM (With Project Example)

**React Router DOM** provides client-side routing. Here’s how to set up a small project:

1. **Install**

   ```bash
   npm install react-router-dom
   ```
2. **Project Structure**

   ```
   src/
     App.jsx
     index.jsx
     pages/
       Home.jsx
       About.jsx
       User.jsx
   ```
3. **Create Pages**

   ```jsx
   // pages/Home.jsx
   import React from 'react';
   import { Link } from 'react-router-dom';

   export default function Home() {
     return (
       <div>
         <h2>Home Page</h2>
         <nav>
           <Link to="/about">About</Link> | <Link to="/user/42">User 42</Link>
         </nav>
       </div>
     );
   }

   // pages/About.jsx
   import React from 'react';

   export default function About() {
     return <h2>About Page</h2>;
   }

   // pages/User.jsx
   import React from 'react';
   import { useParams } from 'react-router-dom';

   export default function User() {
     const { id } = useParams();
     return <h2>User Profile for ID: {id}</h2>;
   }
   ```
4. **Configure Routes**

   ```jsx
   // App.jsx
   import React from 'react';
   import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';
   import Home from './pages/Home';
   import About from './pages/About';
   import User from './pages/User';

   export default function App() {
     return (
       <Router>
         <header>
           <Link to="/">Home</Link> | <Link to="/about">About</Link>
         </header>
         <main>
           <Routes>
             <Route path="/" element={<Home />} />
             <Route path="/about" element={<About />} />
             <Route path="/user/:id" element={<User />} />
             {/* 404 Route */}
             <Route path="*" element={<h2>404 - Not Found</h2>} />
           </Routes>
         </main>
       </Router>
     );
   }
   ```
5. **Entry Point**

   ```jsx
   // index.jsx
   import React from 'react';
   import { createRoot } from 'react-dom/client';
   import App from './App';

   const root = createRoot(document.getElementById('root'));
   root.render(<App />);
   ```

> **When to use React Router:**
>
> * Any single-page application (SPA) where you need multiple distinct “views” without a full page reload.
>   **When not to use:**
> * If your app is extremely small (one or two views) and you don’t need deep linking—then conditional rendering may suffice.
> * If you rely on server-side rendering only (e.g., Next.js routing).

---

## 13. Advanced State Management with Redux and Redux Toolkit

When your application grows and you have shared state that many components must access/modify, Redux (with Redux Toolkit) provides a predictable way to manage global state.

### **Core Concepts**

1. **Store**: Single object holding entire application state.
2. **Actions**: Plain objects describing “what happened.”
3. **Reducers**: Pure functions that take previous state + action → new state.
4. **Dispatch**: Function to send actions to the store.
5. **Selectors**: Functions to extract specific slices of state.

### **Why Redux Toolkit?**

* Provides `configureStore`, `createSlice`, `createAsyncThunk`, and other utilities that remove boilerplate.
* Enforces best practices out of the box (e.g., immutable updates via Immer).

### **Setup Example**

1. **Install**

   ```bash
   npm install @reduxjs/toolkit react-redux
   ```
2. **Create a Slice**

   ```jsx
   // features/counter/counterSlice.js
   import { createSlice } from '@reduxjs/toolkit';

   const initialState = { value: 0 };

   const counterSlice = createSlice({
     name: 'counter',
     initialState,
     reducers: {
       increment: state => {
         // Immer makes this “mutating” syntax safe
         state.value += 1;
       },
       decrement: state => {
         state.value -= 1;
       },
       incrementByAmount: (state, action) => {
         state.value += action.payload;
       },
     },
   });

   export const { increment, decrement, incrementByAmount } = counterSlice.actions;
   export default counterSlice.reducer;
   ```
3. **Configure Store**

   ```jsx
   // app/store.js
   import { configureStore } from '@reduxjs/toolkit';
   import counterReducer from '../features/counter/counterSlice';

   export const store = configureStore({
     reducer: {
       counter: counterReducer,
       // add other slices here
     },
   });
   ```
4. **Provide Store to App**

   ```jsx
   // index.jsx
   import React from 'react';
   import { createRoot } from 'react-dom/client';
   import { Provider } from 'react-redux';
   import App from './App';
   import { store } from './app/store';

   const root = createRoot(document.getElementById('root'));
   root.render(
     <Provider store={store}>
       <App />
     </Provider>
   );
   ```
5. **Use in Components**

   ```jsx
   // components/CounterComponent.jsx
   import React from 'react';
   import { useSelector, useDispatch } from 'react-redux';
   import { increment, decrement, incrementByAmount } from '../features/counter/counterSlice';

   export default function CounterComponent() {
     const count = useSelector(state => state.counter.value);
     const dispatch = useDispatch();

     return (
       <div>
         <h2>Global Counter: {count}</h2>
         <button onClick={() => dispatch(decrement())}>−</button>
         <button onClick={() => dispatch(increment())}>+</button>
         <button onClick={() => dispatch(incrementByAmount(5))}>
           +5
         </button>
       </div>
     );
   }
   ```

> **When to use:**
>
> * Large applications where multiple components across different branches of the tree need to read/update the same state.
> * Complex flows with asynchronous logic (use `createAsyncThunk` for async actions).
>   **When not to use:**
> * Simple apps with minimal shared state—React’s Context + `useReducer` or local state may suffice.
> * Redux adds boilerplate and some runtime overhead; profile if performance or bundle size is critical.

---

### Final Thoughts

* **Performance:** Avoid premature optimizations. Start with clear, straightforward code and measure performance when you suspect bottlenecks.
* **Maintainability:** Favor composition (small, single-responsibility components/hooks) over massive monolithic components.
* **When in doubt:** Opt for the simplest solution that meets requirements. Only introduce advanced patterns (Redux, `useMemo`, HOCs) once you face real complexity or performance issues.
* **Forward-Thinking:** React’s ecosystem evolves quickly—hooks and functional components are the norm. Understanding these core concepts positions you to adopt new best practices (e.g., Concurrent Mode, Suspense) as they stabilize.

