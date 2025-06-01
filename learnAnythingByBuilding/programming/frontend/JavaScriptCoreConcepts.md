## JavaScript Core Concepts in detailed

**1. `var`, `let`, and `const`**
JavaScript historically had only `var`, which is function-scoped and hoisted. ES6 introduced `let` and `const`, both block-scoped and not hoisted in the same way. In modern code, you should default to `const` for values you don’t reassign, use `let` when you need mutable bindings, and avoid `var` entirely unless you’re maintaining legacy projects.

```js
// var (function-scoped, hoisted)
function counterVar() {
  console.log(x); // undefined (due to hoisting)
  var x = 1;
  if (true) {
    var x = 2; // same x, not a new binding
    console.log(x); // 2
  }
  console.log(x); // 2
}

// let/const (block-scoped, temporal dead zone)
function counterLetConst() {
  // console.log(y); // ReferenceError (TDZ)
  let y = 1;
  if (true) {
    let y = 2; // different binding than outer y
    console.log(y); // 2
  }
  console.log(y); // 1

  const z = 5;
  // z = 10; // TypeError (cannot reassign const)
}
```

* **When to use**

  * **`const`**: Default for any variable you don’t reassign (strings, objects, arrays, functions).
  * **`let`**: When you know you’ll reassign (loop counters, temporary values).
* **When not to use**

  * **`var`**: Avoid unless you need function-scoped behavior in ES5 environments.
  * **Overusing `let`**: If you never reassign, lock it down with `const` for readability and safety.

---

**2. `map`, `filter`, and `reduce`**
These are higher-order Array methods that let you transform, filter, or aggregate data declaratively. They’re more readable than `for` loops for array transformations.

```js
const nums = [1, 2, 3, 4, 5];

// map: transforms each element
const squares = nums.map((n) => n * n);
// squares → [1, 4, 9, 16, 25]

// filter: selects elements that pass a test
const evens = nums.filter((n) => n % 2 === 0);
// evens → [2, 4]

// reduce: aggregates to a single value
const sum = nums.reduce((acc, n) => acc + n, 0);
// sum → 15
```

* **Use cases**

  * **`map`**: Converting an array of raw API objects into UI models.
  * **`filter`**: Showing only items that meet certain criteria (e.g., “active users”).
  * **`reduce`**: Summing totals, flattening nested arrays, or building lookup tables.
* **When not to use**

  * If performance is critical and you need to exit early or break, a traditional `for`/`for-of` loop might be better.
  * Over-chaining (e.g., `arr.map(...).filter(...).reduce(...)`) can be harder to debug—consider breaking into intermediate steps.

---

**3. Functions**
Functions in JS can be declared or expressed. They encapsulate logic and can be reused. ES6 introduced arrow functions, which have no own `this`.

```js
// Function declaration (hoisted)
function greet(name) {
  return `Hello, ${name}!`;
}

// Function expression
const greetExpr = function (name) {
  return `Hi, ${name}!`;
};

// Arrow function (concise syntax, no own this)
const greetArrow = (name) => `Hey, ${name}!`;

console.log(greet("Alice"));      // Hello, Alice!
console.log(greetExpr("Bob"));    // Hi, Bob!
console.log(greetArrow("Charlie"));// Hey, Charlie!
```

* **Use cases**

  * **Function declarations**: When you want hoisting (e.g., utility functions at the top).
  * **Function expressions / arrow functions**: In callbacks (e.g., `array.map(...)`) or when you want lexical `this`.
* **When not to use**

  * Avoid overusing arrow functions when you actually need a dynamic `this` (e.g., object methods).
  * Don’t define big logic inside immediately-invoked function expressions if it can be broken into named functions for clarity.

---

**4. Closures**
A closure is when an inner function “closes over” variables from its outer scope. This lets you create private state or factory functions.

```js
function makeCounter() {
  let count = 0; // private variable

  return function () {
    count += 1;
    return count;
  };
}

const counterA = makeCounter();
console.log(counterA()); // 1
console.log(counterA()); // 2

const counterB = makeCounter();
console.log(counterB()); // 1 (separate scope)
```

* **Use cases**

  * **Data privacy**: Hiding state from the outside (e.g., counters, ID generators).
  * **Factories**: Generating specialized functions (e.g., `makeAdder(x) = (y) => x + y`).
* **When not to use**

  * Overusing closures can lead to memory leaks if you accidentally hold onto large data structures.
  * If you need a global or shared state, a closure isn’t the right pattern—use modules or classes.

---

**5. Currying**
Currying transforms a function with multiple arguments into a sequence of functions, each taking a single argument. It can make code more reusable and composable.

```js
// Non-curried
function add(x, y) {
  return x + y;
}
console.log(add(2, 3)); // 5

// Curried version
function addCurry(x) {
  return function (y) {
    return x + y;
  };
}
const addTwo = addCurry(2);
console.log(addTwo(3)); // 5

// ES6 arrow syntax
const multiplyCurry = (x) => (y) => x * y;
const double = multiplyCurry(2);
console.log(double(5)); // 10
```

* **Use cases**

  * **Partial application**: Pre-fill some arguments (e.g., logging functions that always tag with a specific prefix).
  * **Composition**: When you want pipeline-style code, curried functions are easier to plug into a `compose` or `pipe`.
* **When not to use**

  * Over-curried code can hurt readability if you force everything into single-arg functions.
  * If you never reuse partially-applied functions, simple multi-arg functions are clearer.

---

**6. Objects**
Objects in JS are key-value maps where values can be primitives or references. They can be created with object literals, `new Object()`, or constructors/`class`.

```js
// Object literal
const user = {
  id: 42,
  name: "Alice",
  isAdmin: false,
  login() {
    console.log(`${this.name} logged in.`);
  },
};

// Access properties
console.log(user.id); // 42
user.login();         // Alice logged in.

// Dynamic property
user["email"] = "alice@example.com";
console.log(user.email); // alice@example.com
```

* **Use cases**

  * Modeling entities (e.g., users, products) with properties and behaviors (methods).
  * Namespacing: Grouping related functions/data under one object to avoid polluting the global scope.
* **When not to use**

  * If you need guaranteed insertion order or non-string keys, prefer `Map` instead of a plain object.
  * Avoid deep nested objects if you can normalize data into flat structures (especially for UI state—consider Redux patterns).

---

**7. `this` in JS (Implicit Binding)**
When you call `obj.method()`, `this` inside `method` implicitly refers to `obj`. But if you extract the method, `this` can be lost. Understanding binding is crucial.

```js
const user = {
  name: "Bob",
  greet() {
    console.log(`Hi, I’m ${this.name}`);
  },
};

user.greet(); // Hi, I’m Bob

const greetFn = user.greet;
greetFn();    // Hi, I'm undefined (global object or undefined in strict mode)

const boundGreet = user.greet.bind(user);
boundGreet(); // Hi, I’m Bob
```

* **Use cases**

  * **Object methods**: When you want methods to act on the instance’s data (`this.property`).
* **When not to use**

  * In callbacks: Passing `user.greet` directly to an event listener loses binding—either bind it or use an arrow function wrapper.
  * Avoid creating unnecessary objects just to have a `this`; consider pure functions or arrow functions instead.

---

**8. `call`, `bind`, and `apply` (Explicit Binding)**
These methods let you explicitly set `this` for a function. Useful for borrowing methods or partial application.

```js
function introduce(city, country) {
  console.log(`I’m ${this.name} from ${city}, ${country}`);
}

const person = { name: "Carol" };

// call: arguments passed individually
introduce.call(person, "Paris", "France"); // I’m Carol from Paris, France

// apply: arguments passed as an array
introduce.apply(person, ["Berlin", "Germany"]); // I’m Carol from Berlin, Germany

// bind: returns a new function with bound this and/or arguments
const introCarol = introduce.bind(person, "Tokyo");
introCarol("Japan"); // I’m Carol from Tokyo, Japan
```

* **Use cases**

  * **Method borrowing**: E.g., `Array.prototype.slice.call(nodeList)` to convert NodeList to Array.
  * **Partial application**: Pre-bind some arguments (e.g., event handlers with a certain context).
* **When not to use**

  * Overbinding: If you only need to pass context once, consider arrow functions or object methods instead.
  * Performance-sensitive loops: Repeatedly binding inside loops can be less efficient than storing a bound function once.

---

**9. Promises (Async JS)**
A `Promise` represents a value that may be available now, later, or never. It can be in one of three states: pending, fulfilled, or rejected. Use them for asynchronous flows instead of deeply nested callbacks.

```js
// Creating a promise
function doAsyncTask() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const success = true;
      if (success) resolve("Task finished");
      else reject("Task failed");
    }, 1000);
  });
}

// Consuming a promise with .then / .catch
doAsyncTask()
  .then((result) => {
    console.log(result); // "Task finished"
    return "Next step";
  })
  .then((message) => {
    console.log(message); // "Next step"
  })
  .catch((error) => {
    console.error(error);
  });

// Or using async/await (modern style)
async function run() {
  try {
    const res = await doAsyncTask();
    console.log(res); // "Task finished"
  } catch (err) {
    console.error(err);
  }
}
run();
```

* **Use cases**

  * **Network requests**: `fetch()` returns a promise you can `await`.
  * **Sequential async steps**: Chain `.then()` or use `async/await` to keep code linear.
* **When not to use**

  * For extremely simple, one-off callbacks (like `setTimeout` without needing to chain), a callback might suffice.
  * Avoid mixing callbacks and promises haphazardly—pick one style to keep the code consistent.

---

**10. Debouncing and Throttling**
Both are techniques to limit how often a function runs in response to rapid events (like scrolling, resizing, or keypresses).

* **Debouncing**: Ensures a function only runs *after* a burst of events has stopped for a certain interval.
* **Throttling**: Ensures a function runs at most once every specified interval, regardless of how many events occur.

```js
// Debounce utility
function debounce(fn, delay) {
  let timeoutId;
  return function (...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}

// Throttle utility
function throttle(fn, interval) {
  let lastTime = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastTime >= interval) {
      lastTime = now;
      fn.apply(this, args);
    }
  };
}

// Usage: e.g., resize or input events
window.addEventListener(
  "resize",
  throttle(() => {
    console.log("Resized (throttled)");
  }, 200)
);

document
  .getElementById("search")
  .addEventListener(
    "input",
    debounce((e) => {
      console.log("Searching for", e.target.value);
    }, 300)
  );
```

* **Use cases**

  * **Debounce**: Search-as-you-type inputs (wait until user pauses typing).
  * **Throttle**: Scroll or mousemove events (limit calls to once every 100ms).
* **When not to use**

  * If you genuinely need *every* event (e.g., drawing apps capturing each pixel movement).
  * Over-debouncing can make the UI feel unresponsive; test thresholds carefully.

---

**11. Event Bubbling, Capturing, and Delegation**

* **Capturing phase**: Event travels from `window` → `document` → down to the target.
* **Target phase**: Event reaches the actual element.
* **Bubbling phase**: Event bubbles up from the target back to `window`.

By default, event listeners listen in the bubbling phase. Event delegation attaches a single listener at a parent to handle events for many child elements, leveraging bubbling.

```html
<ul id="list">
  <li data-id="1">Item 1</li>
  <li data-id="2">Item 2</li>
  <li data-id="3">Item 3</li>
</ul>
```

```js
// Delegation: one listener on the <ul> handles clicks for all <li>
document.getElementById("list").addEventListener("click", (e) => {
  if (e.target && e.target.matches("li")) {
    console.log("Clicked item ID:", e.target.dataset.id);
  }
});

// Capturing vs Bubbling:
// By default: third argument false → listener in bubbling phase.
// To listen on capture: pass { capture: true }
document.getElementById("list").addEventListener(
  "click",
  (e) => {
    console.log("Capturing:", e.target.textContent);
  },
  { capture: true }
);
```

* **Use cases**

  * **Delegation**: Large lists or dynamic children (e.g., data-driven menus) → only one listener attached.
  * **Capturing**: Rarely used, but useful if you want a parent to intercept events before children.
* **When not to use**

  * If there are only a few static elements, individual listeners might be clearer.
  * Over-delegation can complicate code if you’re not careful with event target checks.

---

**12. Event Loop**
Understanding the event loop is key to grasping JS’s nonblocking nature. At a high level:

1. **Call Stack**: Executes synchronous code.
2. **Callback (Task) Queue**: Holds callbacks from timers, I/O, etc., waiting for the call stack to clear.
3. **Microtask Queue**: Holds promise callbacks and `process.nextTick` (in Node). Microtasks run *before* the next task.

```js
console.log("Start");

setTimeout(() => {
  console.log("setTimeout callback"); // logs third
}, 0);

Promise.resolve()
  .then(() => {
    console.log("First then"); // logs second
  })
  .then(() => {
    console.log("Second then"); // logs third before setTimeout
  });

console.log("End"); // logs second
```

**Logged order**:

```
Start
End
First then
Second then
setTimeout callback
```

* **Use cases**

  * **Debugging**: Knowing why an async callback doesn’t fire immediately.
  * **Performance**: Deciding between `setTimeout(..., 0)` vs. `Promise.resolve().then(...)` (microtask vs. macrotask).
* **When not to overthink it**

  * For everyday UI code, you rarely need to orchestrate microtasks manually. Just use `async/await` or promise chains and trust the event loop.

---

**13. Class and Constructors**
ES6 classes are syntactic sugar over prototype-based constructors. They make inheritance and object creation more readable.

```js
// ES6 class
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    console.log(`Hi, I'm ${this.name}, ${this.age} years old.`);
  }
}

class Student extends Person {
  constructor(name, age, major) {
    super(name, age); // call parent constructor
    this.major = major;
  }

  study() {
    console.log(`${this.name} is studying ${this.major}.`);
  }
}

const alice = new Student("Alice", 22, "CS");
alice.greet(); // Hi, I'm Alice, 22 years old.
alice.study(); // Alice is studying CS.
```

* **Use cases**

  * Modeling objects with shared behaviors and inheritance (e.g., `Employee` → `Manager`).
  * If you prefer a classical OOP syntax, this is straightforward.
* **When not to use**

  * If you only need a single object or simple data structure, a plain object or factory function is simpler.
  * Overusing deep inheritance hierarchies can lead to fragile code—favor composition (`has-a`) over inheritance (`is-a`) when possible.

---

**14. `compose` and `pipe`**
These are functional programming utilities that let you combine smaller functions into larger ones. `compose` passes data right-to-left, `pipe` left-to-right.

```js
// Example helpers:
const compose = (...fns) => (x) => fns.reduceRight((v, f) => f(v), x);
const pipe = (...fns) => (x) => fns.reduce((v, f) => f(v), x);

// Small functions
const double = (n) => n * 2;
const increment = (n) => n + 1;
const toString = (n) => n.toString();

// Using compose (right-to-left)
const showDoublePlusOne = compose(toString, increment, double);
console.log(showDoublePlusOne(3)); // "7"  => double(3)=6 → increment(6)=7 → toString(7)="7"

// Using pipe (left-to-right)
const showDoublePlusOnePipe = pipe(double, increment, toString);
console.log(showDoublePlusOnePipe(3)); // "7"
```

* **Use cases**

  * **Data transformation pipelines**: When you have a chain of pure functions and want a clear flow.
  * **Middleware patterns**: In Redux or Express, you often see similar pipelines.
* **When not to use**

  * If you have side effects or async operations, plain `compose`/`pipe` needs extra handling (e.g., promise-aware variants).
  * Over-composing trivial one-line functions can hurt readability; don’t force everything into a pipeline.

---

**15. Prototypes**
Every JS object has a hidden `[[Prototype]]` (accessible via `__proto__` or `Object.getPrototypeOf`). Methods and properties are looked up the prototype chain if not found on the instance itself.

```js
const animal = {
  speak() {
    console.log(`${this.name} makes a noise.`);
  },
};

const dog = Object.create(animal);
dog.name = "Rex";
dog.speak(); // Rex makes a noise.
```

Under the hood, when you do `dog.speak()`, JS looks:

1. Does `dog` have `speak`? No
2. Does `dog.__proto__` (which is `animal`) have `speak`? Yes → call it

* **Use cases**

  * **Code reuse**: Attach shared methods to a prototype rather than duplicating in every instance.
  * **Polyfills**: Add missing functionality (e.g., `Array.prototype.myMap = function (…) { … }`).
* **When not to use**

  * Mutating built-in prototypes (like `Object.prototype`) can break other code—avoid unless you really need to polyfill.
  * If you need true private data per instance, classes with `WeakMap` or closures are safer than exposing everything on the prototype.

---

### Final Tips

* **Practice small examples**: For each concept, try writing a tiny snippet in the browser console to see how it behaves.
* **Readability > cleverness**: Modern JS offers lots of sugar (arrow functions, `async/await`, chaining). Always choose the pattern that makes your code clear to teammates.
* **Use linters and formatters**: Enforcing rules like “no `var`” or “always declare functions before use” helps catch errors early.
* **Keep learning by building**: As a fresh graduate, pair each concept with a small project: e.g., build a search-as-you-type field (debounce + promises), or a mini task pipeline (compose + reduce).

You’ve got this—keep coding, break things, and refactor until each concept clicks. Good luck!
