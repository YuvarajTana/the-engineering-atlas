# Advanced JavaScript Concepts: Complete Guide

## 1. Execution Context & Call Stack

**Execution Context** is the environment where JavaScript code is evaluated and executed. Every time a function is called, a new execution context is created.

### Types of Execution Context:
- **Global Execution Context**: Default context where code runs
- **Function Execution Context**: Created when a function is invoked
- **Eval Execution Context**: Created when code runs inside eval()

### Call Stack
The call stack tracks function calls using a LIFO (Last In, First Out) structure.

```javascript
function first() {
    console.log('First function');
    second();
    console.log('First function end');
}

function second() {
    console.log('Second function');
    third();
    console.log('Second function end');
}

function third() {
    console.log('Third function');
}

first();
// Output:
// First function
// Second function  
// Third function
// Second function end
// First function end
```

### Practical Applications:
- **Debugging**: Understanding stack traces
- **Recursion**: Avoiding stack overflow
- **Performance**: Optimizing function call depth

---

## 2. Hoisting

Hoisting is JavaScript's behavior of moving variable and function declarations to the top of their scope during compilation.

### Variable Hoisting:
```javascript
console.log(x); // undefined (not ReferenceError)
var x = 5;

// Interpreted as:
var x;
console.log(x); // undefined
x = 5;
```

### Function Hoisting:
```javascript
sayHello(); // "Hello!" - works before declaration

function sayHello() {
    console.log("Hello!");
}

// But function expressions are NOT hoisted:
sayGoodbye(); // TypeError: sayGoodbye is not a function
var sayGoodbye = function() {
    console.log("Goodbye!");
};
```

### Let and Const (Temporal Dead Zone):
```javascript
console.log(a); // ReferenceError
let a = 10;

console.log(b); // ReferenceError  
const b = 20;
```

### Best Practices:
- Always declare variables before use
- Use `let` and `const` instead of `var`
- Declare functions before calling them

---

## 3. IIFE (Immediately Invoked Function Expression)

IIFE creates an isolated scope and executes immediately, preventing global namespace pollution.

### Basic Syntax:
```javascript
(function() {
    var privateVar = "I'm private!";
    console.log("IIFE executed");
})();

// Arrow function IIFE:
(() => {
    console.log("Arrow IIFE executed");
})();
```

### Module Pattern with IIFE:
```javascript
const MyModule = (function() {
    let privateCounter = 0;
    
    return {
        increment: function() {
            privateCounter++;
        },
        decrement: function() {
            privateCounter--;
        },
        getCount: function() {
            return privateCounter;
        }
    };
})();

MyModule.increment();
MyModule.increment();
console.log(MyModule.getCount()); // 2
```

### Use Cases:
- Creating private scope
- Avoiding variable conflicts
- Module patterns
- One-time initialization

---

## 4. Event Loop & Microtasks Queue

The Event Loop manages asynchronous operations in JavaScript's single-threaded environment.

### Components:
- **Call Stack**: Executes synchronous code
- **Web APIs**: Handle async operations (setTimeout, DOM events)
- **Callback Queue**: Holds callbacks from completed async operations
- **Microtask Queue**: Holds promises and async/await

### Execution Order:
```javascript
console.log('1');

setTimeout(() => console.log('2'), 0);

Promise.resolve().then(() => console.log('3'));

console.log('4');

// Output: 1, 4, 3, 2
// Microtasks (Promise) execute before macrotasks (setTimeout)
```

### Complex Example:
```javascript
console.log('Start');

setTimeout(() => console.log('Timeout 1'), 0);

Promise.resolve().then(() => {
    console.log('Promise 1');
    return Promise.resolve();
}).then(() => console.log('Promise 2'));

setTimeout(() => console.log('Timeout 2'), 0);

console.log('End');

// Output: Start, End, Promise 1, Promise 2, Timeout 1, Timeout 2
```

---

## 5. Debouncing & Throttling

Performance optimization techniques to control function execution frequency.

### Debouncing
Delays function execution until after a specified time has passed since the last call.

```javascript
function debounce(func, delay) {
    let timeoutId;
    return function(...args) {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => func.apply(this, args), delay);
    };
}

// Usage: Search input
const searchInput = document.getElementById('search');
const debouncedSearch = debounce(function(e) {
    console.log('Searching for:', e.target.value);
    // Perform API call
}, 300);

searchInput.addEventListener('input', debouncedSearch);
```

### Throttling
Limits function execution to once per specified time interval.

```javascript
function throttle(func, delay) {
    let lastCall = 0;
    return function(...args) {
        const now = Date.now();
        if (now - lastCall >= delay) {
            lastCall = now;
            func.apply(this, args);
        }
    };
}

// Usage: Scroll event
const throttledScroll = throttle(function() {
    console.log('Scroll position:', window.scrollY);
}, 100);

window.addEventListener('scroll', throttledScroll);
```

### When to Use:
- **Debouncing**: Search inputs, resize events, button clicks
- **Throttling**: Scroll events, mouse movements, API rate limiting

---

## 6. Prototypal Inheritance

JavaScript uses prototype-based inheritance where objects can inherit directly from other objects.

### Prototype Chain:
```javascript
function Animal(name) {
    this.name = name;
}

Animal.prototype.speak = function() {
    console.log(`${this.name} makes a sound`);
};

function Dog(name, breed) {
    Animal.call(this, name); // Call parent constructor
    this.breed = breed;
}

// Inherit from Animal
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

Dog.prototype.bark = function() {
    console.log(`${this.name} barks!`);
};

const dog = new Dog('Rex', 'Golden Retriever');
dog.speak(); // Rex makes a sound
dog.bark();  // Rex barks!
```

### Modern Class Syntax:
```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    speak() {
        console.log(`${this.name} makes a sound`);
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name);
        this.breed = breed;
    }
    
    bark() {
        console.log(`${this.name} barks!`);
    }
}
```

### Object.create() Pattern:
```javascript
const animal = {
    speak() {
        console.log(`${this.name} makes a sound`);
    }
};

const dog = Object.create(animal);
dog.name = 'Rex';
dog.bark = function() {
    console.log(`${this.name} barks!`);
};
```

---

## 7. Destructuring with Default Values

Extract values from arrays and objects with fallback defaults.

### Array Destructuring:
```javascript
const numbers = [1, 2];
const [a = 0, b = 0, c = 0] = numbers;
console.log(a, b, c); // 1, 2, 0

// Swapping variables
let x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 2, 1

// Rest operator
const [first, ...rest] = [1, 2, 3, 4, 5];
console.log(first); // 1
console.log(rest);  // [2, 3, 4, 5]
```

### Object Destructuring:
```javascript
const user = { name: 'John', age: 30 };

// Basic destructuring with defaults
const { name, age, city = 'Unknown' } = user;
console.log(name, age, city); // John, 30, Unknown

// Renaming with defaults
const { name: userName = 'Anonymous', email = 'no-email' } = user;
console.log(userName, email); // John, no-email

// Nested destructuring
const config = {
    api: {
        url: 'https://api.example.com',
        timeout: 5000
    }
};

const { 
    api: { 
        url = 'localhost', 
        timeout = 3000, 
        retries = 3 
    } = {} 
} = config;
```

### Function Parameters:
```javascript
function createUser({ 
    name = 'Anonymous', 
    age = 0, 
    email = 'no-email@example.com' 
} = {}) {
    return { name, age, email };
}

console.log(createUser()); // Uses all defaults
console.log(createUser({ name: 'John' })); // Partial defaults
```

---

## 8. Typed Arrays

Efficient handling of binary data with specific numeric types.

### Types of Typed Arrays:
```javascript
// Different typed arrays for different data types
const int8 = new Int8Array(4);          // -128 to 127
const uint8 = new Uint8Array(4);        // 0 to 255
const int16 = new Int16Array(4);        // -32,768 to 32,767
const uint16 = new Uint16Array(4);      // 0 to 65,535
const int32 = new Int32Array(4);        // -2^31 to 2^31-1
const uint32 = new Uint32Array(4);      // 0 to 2^32-1
const float32 = new Float32Array(4);    // 32-bit float
const float64 = new Float64Array(4);    // 64-bit float
```

### ArrayBuffer and Views:
```javascript
// Create a buffer of 16 bytes
const buffer = new ArrayBuffer(16);

// Create different views of the same buffer
const int32View = new Int32Array(buffer);
const uint8View = new Uint8Array(buffer);

int32View[0] = 0x12345678;
console.log(uint8View[0]); // 0x78 (little-endian)
console.log(uint8View[1]); // 0x56
console.log(uint8View[2]); // 0x34
console.log(uint8View[3]); // 0x12
```

### Practical Example - Image Processing:
```javascript
function processImageData(imageData) {
    const data = new Uint8ClampedArray(imageData.data);
    
    // Convert to grayscale
    for (let i = 0; i < data.length; i += 4) {
        const gray = (data[i] + data[i + 1] + data[i + 2]) / 3;
        data[i] = gray;     // Red
        data[i + 1] = gray; // Green
        data[i + 2] = gray; // Blue
        // data[i + 3] is alpha, leave unchanged
    }
    
    return new ImageData(data, imageData.width, imageData.height);
}
```

---

## 9. Memoization

Cache function results to avoid redundant calculations.

### Basic Memoization:
```javascript
function memoize(fn) {
    const cache = new Map();
    
    return function(...args) {
        const key = JSON.stringify(args);
        
        if (cache.has(key)) {
            console.log('Cache hit!');
            return cache.get(key);
        }
        
        console.log('Computing...');
        const result = fn.apply(this, args);
        cache.set(key, result);
        return result;
    };
}

// Example: Expensive Fibonacci calculation
const fibonacci = memoize(function(n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(40)); // Fast due to memoization
```

### Advanced Memoization with TTL (Time To Live):
```javascript
function memoizeWithTTL(fn, ttl = 60000) {
    const cache = new Map();
    
    return function(...args) {
        const key = JSON.stringify(args);
        const now = Date.now();
        
        if (cache.has(key)) {
            const { value, timestamp } = cache.get(key);
            if (now - timestamp < ttl) {
                return value;
            }
            cache.delete(key);
        }
        
        const result = fn.apply(this, args);
        cache.set(key, { value: result, timestamp: now });
        return result;
    };
}

// API call memoization
const fetchUser = memoizeWithTTL(async function(userId) {
    const response = await fetch(`/api/users/${userId}`);
    return response.json();
}, 5 * 60 * 1000); // 5 minutes TTL
```

---

## 10. Generators & Iterators

Functions that can pause and resume execution, useful for handling large datasets or creating custom iteration patterns.

### Basic Generator:
```javascript
function* numberGenerator() {
    yield 1;
    yield 2;
    yield 3;
    return 'done';
}

const gen = numberGenerator();
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: 'done', done: true }
```

### Infinite Sequences:
```javascript
function* fibonacci() {
    let a = 0, b = 1;
    while (true) {
        yield a;
        [a, b] = [b, a + b];
    }
}

const fib = fibonacci();
for (let i = 0; i < 10; i++) {
    console.log(fib.next().value);
}
```

### Async Generator for Data Streaming:
```javascript
async function* fetchPages(url) {
    let page = 1;
    let hasMore = true;
    
    while (hasMore) {
        const response = await fetch(`${url}?page=${page}`);
        const data = await response.json();
        
        yield data.items;
        
        hasMore = data.hasNextPage;
        page++;
    }
}

// Usage
(async () => {
    for await (const pageData of fetchPages('/api/data')) {
        console.log('Processing page:', pageData);
    }
})();
```

### Custom Iterator:
```javascript
class Range {
    constructor(start, end, step = 1) {
        this.start = start;
        this.end = end;
        this.step = step;
    }
    
    *[Symbol.iterator]() {
        for (let i = this.start; i < this.end; i += this.step) {
            yield i;
        }
    }
}

const range = new Range(0, 10, 2);
for (const num of range) {
    console.log(num); // 0, 2, 4, 6, 8
}
```

---

## 11. Currying & Partial Application

Transform functions to improve reusability and create specialized versions.

### Currying:
```javascript
// Manual currying
function curry(fn) {
    return function curried(...args) {
        if (args.length >= fn.length) {
            return fn.apply(this, args);
        }
        return function(...nextArgs) {
            return curried.apply(this, args.concat(nextArgs));
        };
    };
}

// Example function
function add(a, b, c) {
    return a + b + c;
}

const curriedAdd = curry(add);

// Different ways to call
console.log(curriedAdd(1)(2)(3)); // 6
console.log(curriedAdd(1, 2)(3)); // 6
console.log(curriedAdd(1)(2, 3)); // 6

// Create specialized functions
const add5 = curriedAdd(5);
const add5and3 = add5(3);
console.log(add5and3(2)); // 10
```

### Practical Currying Example:
```javascript
const multiply = curry((a, b, c) => a * b * c);

// Create specialized multipliers
const double = multiply(2);
const triple = multiply(3);
const byTenThenDouble = double(10);

console.log(byTenThenDouble(5)); // 100 (2 * 10 * 5)

// Function composition with curried functions
const numbers = [1, 2, 3, 4, 5];
const doubledNumbers = numbers.map(double(1)); // [2, 4, 6, 8, 10]
```

### Partial Application:
```javascript
function partial(fn, ...argsToApply) {
    return function(...remainingArgs) {
        return fn(...argsToApply, ...remainingArgs);
    };
}

function greet(greeting, title, firstName, lastName) {
    return `${greeting} ${title} ${firstName} ${lastName}!`;
}

const sayHelloTo = partial(greet, 'Hello');
const sayHelloToMr = partial(sayHelloTo, 'Mr.');

console.log(sayHelloToMr('John', 'Doe')); // "Hello Mr. John Doe!"
```

---

## 12. Memory Management & Garbage Collection

Understanding how JavaScript manages memory helps prevent memory leaks and optimize performance.

### Memory Lifecycle:
1. **Allocation**: Memory is allocated when objects are created
2. **Usage**: Reading and writing to allocated memory
3. **Release**: Memory is freed when no longer needed

### Common Memory Leaks:
```javascript
// 1. Global variables
var globalVar = 'This stays in memory';

// 2. Forgotten timers
const timer = setInterval(() => {
    // This keeps running and holds references
    console.log('Timer running');
}, 1000);
// Remember to clear: clearInterval(timer);

// 3. Closures holding references
function createHandler() {
    const largeData = new Array(1000000).fill('data');
    
    return function(event) {
        // largeData is kept in memory due to closure
        console.log('Handling event');
    };
}

// 4. Detached DOM nodes
let elements = [];
function addElement() {
    const el = document.createElement('div');
    elements.push(el); // Keeps reference even after removal
    document.body.appendChild(el);
}
```

### Best Practices:
```javascript
// 1. Null references when done
let heavyObject = { /* large object */ };
// Use the object...
heavyObject = null; // Allow GC to clean up

// 2. Use WeakMap for temporary associations
const elementData = new WeakMap();
elementData.set(element, { someData: 'value' });
// When element is removed, data is automatically cleaned up

// 3. Remove event listeners
function cleanup() {
    element.removeEventListener('click', handler);
    clearInterval(timer);
    clearTimeout(timeout);
}

// 4. Use object pools for frequently created objects
class ObjectPool {
    constructor(createFn, resetFn) {
        this.createFn = createFn;
        this.resetFn = resetFn;
        this.pool = [];
    }
    
    acquire() {
        return this.pool.length > 0 ? 
            this.pool.pop() : 
            this.createFn();
    }
    
    release(obj) {
        this.resetFn(obj);
        this.pool.push(obj);
    }
}
```

---

## 13. Module Patterns

Organize code into reusable, maintainable modules.

### ES6 Modules:
```javascript
// math.js - Named exports
export const PI = 3.14159;
export function add(a, b) {
    return a + b;
}
export function multiply(a, b) {
    return a * b;
}

// calculator.js - Default export
export default class Calculator {
    add(a, b) { return a + b; }
    subtract(a, b) { return a - b; }
}

// main.js - Importing
import Calculator from './calculator.js';
import { add, multiply, PI } from './math.js';
import * as MathUtils from './math.js';

const calc = new Calculator();
console.log(calc.add(5, 3));
console.log(MathUtils.add(5, 3));
```

### Module Pattern with IIFE:
```javascript
const UserModule = (function() {
    // Private variables and functions
    let users = [];
    let currentUser = null;
    
    function validateUser(user) {
        return user.name && user.email;
    }
    
    // Public API
    return {
        addUser(user) {
            if (validateUser(user)) {
                users.push(user);
                return true;
            }
            return false;
        },
        
        getUsers() {
            return [...users]; // Return copy to prevent mutation
        },
        
        setCurrentUser(userId) {
            currentUser = users.find(user => user.id === userId);
        },
        
        getCurrentUser() {
            return currentUser ? { ...currentUser } : null;
        }
    };
})();
```

### Revealing Module Pattern:
```javascript
const APIModule = (function() {
    let baseURL = 'https://api.example.com';
    let authToken = null;
    
    function makeRequest(endpoint, options = {}) {
        const config = {
            ...options,
            headers: {
                'Content-Type': 'application/json',
                ...(authToken && { Authorization: `Bearer ${authToken}` }),
                ...options.headers
            }
        };
        
        return fetch(`${baseURL}${endpoint}`, config);
    }
    
    function setAuthToken(token) {
        authToken = token;
    }
    
    function clearAuth() {
        authToken = null;
    }
    
    function get(endpoint) {
        return makeRequest(endpoint);
    }
    
    function post(endpoint, data) {
        return makeRequest(endpoint, {
            method: 'POST',
            body: JSON.stringify(data)
        });
    }
    
    // Reveal public methods
    return {
        setAuthToken,
        clearAuth,
        get,
        post
    };
})();
```

---

## 14. Shadow DOM

Encapsulates DOM and CSS, creating isolated components.

### Basic Shadow DOM:
```javascript
class CustomButton extends HTMLElement {
    constructor() {
        super();
        
        // Create shadow root
        this.attachShadow({ mode: 'open' });
        
        // Create template
        this.shadowRoot.innerHTML = `
            <style>
                button {
                    background: linear-gradient(45deg, #667eea 0%, #764ba2 100%);
                    border: none;
                    color: white;
                    padding: 12px 24px;
                    border-radius: 25px;
                    cursor: pointer;
                    font-size: 16px;
                    transition: transform 0.2s;
                }
                
                button:hover {
                    transform: scale(1.05);
                }
                
                button:active {
                    transform: scale(0.95);
                }
            </style>
            <button>
                <slot></slot>
            </button>
        `;
        
        // Add event listeners
        this.shadowRoot.querySelector('button').addEventListener('click', 
            () => this.handleClick()
        );
    }
    
    handleClick() {
        this.dispatchEvent(new CustomEvent('custom-click', {
            detail: { message: 'Custom button clicked!' },
            bubbles: true
        }));
    }
}

// Register the custom element
customElements.define('custom-button', CustomButton);
```

### Advanced Shadow DOM with Templates:
```javascript
class UserCard extends HTMLElement {
    static get observedAttributes() {
        return ['name', 'email', 'avatar'];
    }
    
    constructor() {
        super();
        this.attachShadow({ mode: 'open' });
        this.render();
    }
    
    attributeChangedCallback(name, oldValue, newValue) {
        if (oldValue !== newValue) {
            this.render();
        }
    }
    
    render() {
        const name = this.getAttribute('name') || 'Unknown';
        const email = this.getAttribute('email') || 'No email';
        const avatar = this.getAttribute('avatar') || 'default-avatar.png';
        
        this.shadowRoot.innerHTML = `
            <style>
                :host {
                    display: block;
                    max-width: 300px;
                    margin: 10px;
                }
                
                .card {
                    border: 1px solid #ddd;
                    border-radius: 8px;
                    padding: 16px;
                    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
                    background: white;
                }
                
                .avatar {
                    width: 60px;
                    height: 60px;
                    border-radius: 50%;
                    object-fit: cover;
                }
                
                .name {
                    font-size: 18px;
                    font-weight: bold;
                    margin: 8px 0 4px 0;
                }
                
                .email {
                    color: #666;
                    font-size: 14px;
                }
            </style>
            <div class="card">
                <img class="avatar" src="${avatar}" alt="${name}">
                <div class="name">${name}</div>
                <div class="email">${email}</div>
            </div>
        `;
    }
}

customElements.define('user-card', UserCard);
```

---

## 15. Functional Programming in JavaScript

Write cleaner, more maintainable code using functional programming principles.

### Pure Functions:
```javascript
// Pure function - same input always produces same output
function add(a, b) {
    return a + b;
}

// Impure function - depends on external state
let total = 0;
function addToTotal(value) {
    total += value; // Modifies external state
    return total;
}

// Pure alternative
function calculateNewTotal(currentTotal, value) {
    return currentTotal + value;
}
```

### Immutability:
```javascript
// Instead of mutating arrays
const numbers = [1, 2, 3];
const newNumbers = [...numbers, 4]; // Create new array

// Instead of mutating objects
const user = { name: 'John', age: 30 };
const updatedUser = { ...user, age: 31 }; // Create new object

// Immutable operations
const users = [
    { id: 1, name: 'John', active: true },
    { id: 2, name: 'Jane', active: false }
];

// Add new user
const withNewUser = [...users, { id: 3, name: 'Bob', active: true }];

// Update user
const withUpdatedUser = users.map(user => 
    user.id === 1 ? { ...user, active: false } : user
);

// Remove user
const withoutUser = users.filter(user => user.id !== 2);
```

### Higher-Order Functions:
```javascript
// Functions that take or return other functions
function createMultiplier(factor) {
    return function(number) {
        return number * factor;
    };
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5)); // 10
console.log(triple(4)); // 12

// Function composition
const compose = (...fns) => (value) => fns.reduceRight((acc, fn) => fn(acc), value);

const addOne = x => x + 1;
const square = x => x * x;
const double = x => x * 2;

const transform = compose(double, square, addOne);
console.log(transform(3)); // 32 ((3 + 1) ^ 2 * 2)
```

### Functional Array Methods:
```javascript
const data = [
    { name: 'John', age: 25, department: 'IT' },
    { name: 'Jane', age: 30, department: 'HR' },
    { name: 'Bob', age: 35, department: 'IT' },
    { name: 'Alice', age: 28, department: 'Finance' }
];

// Chain functional operations
const result = data
    .filter(person => person.department === 'IT')
    .map(person => ({ ...person, senior: person.age > 30 }))
    .reduce((acc, person) => {
        acc[person.name] = person;
        return acc;
    }, {});

console.log(result);
```

### Monads (Optional Pattern):
```javascript
class Maybe {
    constructor(value) {
        this.value = value;
    }
    
    static of(value) {
        return new Maybe(value);
    }
    
    isNothing() {
        return this.value === null || this.value === undefined;
    }
    
    map(fn) {
        return this.isNothing() ? Maybe.of(null) : Maybe.of(fn(this.value));
    }
    
    flatMap(fn) {
        return this.isNothing() ? Maybe.of(null) : fn(this.value);
    }
    
    getOrElse(defaultValue) {
        return this.isNothing() ? defaultValue : this.value;
    }
}

// Usage
const safeParseInt = (str) => {
    const parsed = parseInt(str);
    return isNaN(parsed) ? Maybe.of(null) : Maybe.of(parsed);
};

const result = Maybe.of('42')
    .flatMap(safeParseInt)
    .map(x => x * 2)
    .map(x => x + 10)
    .getOrElse(0);

console.log(result); // 94
```

---

## 16. Proxy

Intercept and customize operations on objects (property lookup, assignment, enumeration, function invocation, etc.).

### Basic Proxy:
```javascript
const target = {
    name: 'John',
    age: 30
};

const handler = {
    get(target, property, receiver) {
        console.log(`Getting ${property}`);
        return target[property];
    },
    
    set(target, property, value, receiver) {
        console.log(`Setting ${property} to ${value}`);
        target[property] = value;
        return true;
    }
};

const proxy = new Proxy(target, handler);

console.log(proxy.name); // "Getting name" -> "John"
proxy.age = 31;          // "Setting age to 31"
```

### Validation Proxy:
```javascript
function createValidatedUser(validationRules) {
    return new Proxy({}, {
        set(target, property, value) {
            const rule = validationRules[property];
            
            if (rule && !rule.validator(value)) {
                throw new Error(rule.message);
            }
            
            target[property] = value;
            return true;
        }
    });
}

const userValidation = {
    name: {
        validator: value => typeof value === 'string' && value.length > 0,
        message: 'Name must be a non-empty string'
    },
    age: {
        validator: value => Number.isInteger(value) && value >= 0 && value <= 150,
        message: 'Age must be an integer between 0 and 150'
    },
    email: {
        validator: value => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value),
        message: 'Email must be valid'
    }
};

const user = createValidatedUser(userValidation);

try {
    user.name = 'John';
    user.age = 30;
    user.email = 'john@example.com';
    console.log('User created successfully:', user);
} catch (error) {
    console.error(error.message);
}
```

### API Wrapper with Proxy:
```javascript
function createAPIWrapper(baseURL) {
    return new Proxy({}, {
        get(target, property) {
            return async function(...args) {
                const endpoint = property.replace(/([A-Z])/g, '-$1').toLowerCase();
                const url = `${baseURL}/${endpoint}`;
                
                if (args.length === 0) {
                    // GET request
                    return fetch(url).then(res => res.json());
                } else if (args.length === 1) {
                    // POST request
                    return fetch(url, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(args[0])
                    }).then(res => res.json());
                }
            };
        }
    });
}

const api = createAPIWrapper('https://api.example.com');

// These method calls are dynamically created:
// api.getUsers() -> GET /get-users
// api.createUser({name: 'John'}) -> POST /create-user
// api.getUserProfile() -> GET /get-user-profile
```

### Observable Object with Proxy:
```javascript
function createObservable(target, onChange) {
    return new Proxy(target, {
        set(target, property, value, receiver) {
            const oldValue = target[property];
            target[property] = value;
            
            onChange({
                property,
                oldValue,
                newValue: value,
                target
            });
            
            return true;
        }
    });
}

const data = createObservable({ count: 0 }, (change) => {
    console.log(`${change.property} changed from ${change.oldValue} to ${change.newValue}`);
});

data.count = 1; // "count changed from 0 to 1"
data.name = 'Test'; // "name changed from undefined to Test"
```

---

## Summary

These 16 concepts form the foundation of advanced JavaScript development:

- **Runtime Understanding**: Execution context, hoisting, event loop
- **Performance**: Debouncing, throttling, memoization, memory management
- **Code Organization**: IIFE, modules, functional programming
- **Modern Features**: Destructuring, typed arrays, generators, proxy
- **Object-Oriented**: Prototypal inheritance, shadow DOM
- **Functional Patterns**: Currying, immutability, pure functions

Mastering these concepts will significantly improve your ability to write efficient, maintainable, and scalable JavaScript applications.
