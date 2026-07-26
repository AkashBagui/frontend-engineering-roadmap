# JavaScript Interview Questions

## 1. Explain closures with examples.

**Answer:** A closure is a function that remembers its lexical scope even when the function executes outside that scope.

```javascript
function createCounter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const counter = createCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

**Practical uses of closures:**
```javascript
// 1. Data privacy (encapsulation)
function createBankAccount(initialBalance) {
  let balance = initialBalance;
  return {
    deposit: (amount) => { balance += amount; },
    withdraw: (amount) => { if (amount <= balance) balance -= amount; },
    getBalance: () => balance
  };
}

// 2. Function factories
function multiply(factor) {
  return (number) => number * factor;
}
const double = multiply(2);
const triple = multiply(3);

// 3. Module pattern
const counterModule = (function() {
  let count = 0;
  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count
  };
})();
```

**Common closure interview problem:**
```javascript
// Problem: What gets logged?
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000); // 3, 3, 3 (with var)
}

// Fix 1: Use let (block scope)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 1000); // 0, 1, 2
}

// Fix 2: IIFE closure
for (var i = 0; i < 3; i++) {
  ((j) => setTimeout(() => console.log(j), 1000))(i); // 0, 1, 2
}
```

## 2. How does the event loop work?

**Answer:** The event loop is JavaScript's mechanism for handling asynchronous operations in a single-threaded environment.

```
Execution order:
1. Synchronous code on call stack
2. Microtasks (Promise.then, queueMicrotask, MutationObserver)
3. Render (paint)
4. Macrotasks (setTimeout, setInterval, I/O, UI events)
```

```javascript
console.log('1'); // Sync - 1st

setTimeout(() => console.log('2'), 0); // Macrotask - 4th

Promise.resolve().then(() => console.log('3')); // Microtask - 3rd

console.log('4'); // Sync - 2nd

// Output: 1, 4, 3, 2
```

```javascript
// More complex example
console.log('start');

setTimeout(() => console.log('timeout1'), 0);

Promise.resolve().then(() => {
  console.log('promise1');
  setTimeout(() => console.log('timeout2'), 0);
});

Promise.resolve().then(() => console.log('promise2'));

console.log('end');

// Output: start, end, promise1, promise2, timeout1, timeout2
```

## 3. Explain `this` keyword behavior.

**Answer:** The value of `this` depends on how a function is called, not where it's defined.

**Five rules (in order of precedence):**

```javascript
// 1. New binding (highest)
function Person(name) {
  this.name = name; // 'this' is the new object being created
}
const p = new Person('Alice');

// 2. Explicit binding
function greet() { return 'Hello, ' + this.name; }
const user = { name: 'Bob' };
console.log(greet.call(user));   // Hello, Bob
console.log(greet.apply(user));  // Hello, Bob
const bound = greet.bind(user);  // Creates a new function with 'this' bound

// 3. Implicit binding (method call)
const obj = {
  name: 'Charlie',
  greet() { return this.name; }
};
console.log(obj.greet()); // Charlie

// 4. Default binding (global/undefined)
function showThis() { console.log(this); }
showThis(); // Window (browser) or global (Node) | undefined in strict mode

// 5. Arrow functions - no own 'this', captures from lexical scope
const arrowObj = {
  name: 'Dave',
  greet: () => this.name, // 'this' is outer scope (not obj)
  greetRegular() {
    const inner = () => this.name; // 'this' is obj (lexical)
    return inner();
  }
};
```

**Common interview scenario:**
```javascript
const person = {
  name: 'Alice',
  greet() { return this.name; },
  greetArrow: () => this.name
};

console.log(person.greet());       // Alice
console.log(person.greetArrow());  // undefined (arrow, this = outer scope)

const greetFn = person.greet;
console.log(greetFn()); // undefined (loses this context)
```

## 4. How do Promises work?

**Answer:** A Promise represents a value that may be available now, later, or never.

```javascript
// Creating a Promise
const fetchData = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = true;
    if (success) {
      resolve({ id: 1, name: 'Data' });
    } else {
      reject(new Error('Failed to fetch'));
    }
  }, 1000);
});

// Consuming a Promise
fetchData
  .then(data => console.log(data))
  .catch(error => console.error(error))
  .finally(() => console.log('Done'));

// Promise states: pending -> fulfilled | rejected
```

**Promise static methods:**
```javascript
// Promise.all - waits for all to resolve, reject if any fails
Promise.all([promise1, promise2, promise3])
  .then(([result1, result2, result3]) => {});

// Promise.allSettled - waits for all to settle (resolve or reject)
Promise.allSettled([promise1, promise2])
  .then(results => console.log(results));
  // [{status: 'fulfilled', value: ...}, {status: 'rejected', reason: ...}]

// Promise.race - resolves/rejects with first settled
Promise.race([promise1, promise2]);

// Promise.any - resolves with first fulfilled (ignores rejections)
Promise.any([promise1, promise2]);
```

**Promise chaining:**
```javascript
fetch('/api/user')
  .then(res => res.json())
  .then(user => fetch('/api/posts/' + user.id))
  .then(res => res.json())
  .then(posts => renderPosts(posts))
  .catch(err => console.error('Any error in chain:', err));
```

## 5. Explain async/await.

**Answer:** async/await is syntactic sugar over Promises, making asynchronous code read like synchronous code.

```javascript
// Marking a function as async makes it return a Promise
async function fetchUserData(userId) {
  try {
    const response = await fetch('/api/users/' + userId);
    if (!response.ok) throw new Error('HTTP error');
    const data = await response.json();
    return data;
  } catch (error) {
    console.error('Error:', error);
    throw error; // Re-throw if caller needs to handle
  }
}

// Always returns a Promise
const result = fetchUserData(1); // Promise
result.then(data => console.log(data));

// Sequential vs parallel
// Sequential (slower)
async function sequential() {
  const a = await fetch('/api/a'); // 1s
  const b = await fetch('/api/b'); // 1s - total: 2s
}

// Parallel (faster)
async function parallel() {
  const [a, b] = await Promise.all([
    fetch('/api/a'), // 1s
    fetch('/api/b')  // 1s - total: 1s
  ]);
}
```

**Async/await with higher-order operations:**
```javascript
// Sequential iteration
async function processSequentially(items) {
  const results = [];
  for (const item of items) {
    results.push(await processItem(item)); // One at a time
  }
  return results;
}

// Parallel iteration
async function processParallel(items) {
  return Promise.all(items.map(item => processItem(item))); // All at once
}
```

## 6. What is hoisting?

**Answer:** Hoisting is JavaScript's behavior of moving declarations to the top of their scope during compilation.

```javascript
// Variable hoisting with var
console.log(x); // undefined (not ReferenceError)
var x = 5;
// Above is interpreted as:
// var x;
// console.log(x);
// x = 5;

// let and const are hoisted but NOT initialized (Temporal Dead Zone)
console.log(y); // ReferenceError: Cannot access 'y' before initialization
let y = 10;

// Function declarations are fully hoisted
sayHello(); // "Hello!"
function sayHello() {
  console.log('Hello!');
}

// Function expressions are NOT hoisted (depends on declaration type)
sayHi(); // TypeError: sayHi is not a function
var sayHi = function() { console.log('Hi!'); };
// With let:
sayHi2(); // ReferenceError
let sayHi2 = function() { console.log('Hi!'); };
```

**TDZ (Temporal Dead Zone):**
```javascript
{
  // TDZ starts
  console.log(a); // ReferenceError
  let a = 3;      // TDZ ends

  console.log(b); // ReferenceError
  const b = 5;    // TDZ ends
}
```

## 7. How does prototypal inheritance work?

**Answer:** Every JavaScript object has an internal [[Prototype]] property that points to another object, forming a prototype chain.

```javascript
// Constructor function
function Animal(name) {
  this.name = name;
}

// Methods on prototype (shared across all instances)
Animal.prototype.speak = function() {
  return this.name + ' makes a sound';
};

const dog = new Animal('Dog');
console.log(dog.speak()); // Dog makes a sound

// Prototype chain look-up:
// dog -> Animal.prototype -> Object.prototype -> null

// ES6 class syntax (syntactic sugar over prototypes)
class Animal {
  constructor(name) { this.name = name; }
  speak() { return this.name + ' makes a sound'; }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  speak() { return this.name + ' barks'; }
  fetch() { return this.name + ' fetches the ball'; }
}
// Dog instance -> Dog.prototype -> Animal.prototype -> Object.prototype -> null
```

**Prototypal inheritance with Object.create:**
```javascript
const animal = {
  speak() { return this.name + ' makes a sound'; }
};

const dog = Object.create(animal);
dog.name = 'Dog';
dog.bark = function() { return this.name + ' barks'; };

console.log(dog.speak()); // Dog makes a sound (inherited)
console.log(dog.bark());  // Dog barks (own)
```

## 8. What are the different ways to create objects?

**Answer:**

```javascript
// 1. Object literal
const obj1 = { name: 'Alice', age: 30 };

// 2. Constructor function
function Person(name) {
  this.name = name;
}
const obj2 = new Person('Bob');

// 3. Object.create
const proto = { greet() { return 'Hi, I am ' + this.name; } };
const obj3 = Object.create(proto);
obj3.name = 'Charlie';

// 4. ES6 Class
class Animal {
  constructor(name) { this.name = name; }
}
const obj4 = new Animal('Dog');

// 5. Factory function
function createPerson(name) {
  return { name, greet() { return 'Hello, ' + name; } };
}
const obj5 = createPerson('Dave');

// 6. Object.assign (for merging/defaults)
const defaults = { role: 'user', active: true };
const user = Object.assign({}, defaults, { name: 'Eve' });

// 7. Spread operator
const obj6 = { ...defaults, name: 'Frank' };

// 8. JSON.parse (for serialization/deserialization)
const obj7 = JSON.parse('{"name":"Grace"}');
```

## 9. Explain the difference between `==` and `===`.

**Answer:**

```javascript
// === strict equality - NO type coercion
5 === 5;          // true
5 === '5';        // false
null === undefined; // false
true === 1;       // false
NaN === NaN;      // false (NaN is never equal to itself)

// == loose equality - WITH type coercion
5 == 5;           // true
5 == '5';         // true (string '5' converted to number 5)
null == undefined; // true (special case)
true == 1;        // true
false == 0;       // true
'' == false;      // true
[] == false;      // true
[1] == true;      // true
[1,2] == NaN;     // false

// Object.is - same-value equality (ES6)
Object.is(NaN, NaN);   // true
Object.is(0, -0);      // false
Object.is(+0, -0);     // false
Object.is(5, 5);       // true

// Abstract Equality Comparison algorithm highlights:
// 1. Same type? Use ===
// 2. null/undefined? Equal to each other only
// 3. Number vs String? ToNumber(string)
// 4. Boolean vs Any? ToNumber(boolean)
// 5. Object vs Primitive? ToPrimitive(object)
```

**Best practice:** Always use `===` unless you specifically need type coercion.

## 10. How do `call`, `apply`, and `bind` work?

**Answer:** These methods explicitly set `this` context for function execution.

```javascript
const person = { name: 'Alice' };

function greet(greeting, punctuation) {
  return greeting + ', ' + this.name + punctuation;
}

// call - arguments passed individually, executes immediately
console.log(greet.call(person, 'Hello', '!')); // Hello, Alice!

// apply - arguments passed as array, executes immediately
console.log(greet.apply(person, ['Hi', '?'])); // Hi, Alice?

// bind - creates new bound function, does NOT execute
const boundGreet = greet.bind(person);
console.log(boundGreet('Hey', '.')); // Hey, Alice.

// Partial application with bind
const greetHello = greet.bind(person, 'Hello');
console.log(greetHello('!!!')); // Hello, Alice!!!
```

**Use cases:**
```javascript
// Borrowing methods
const arr = [1, 2, 3];
const args = { 0: 'a', 1: 'b', length: 2 };
Array.prototype.push.call(args, 'c'); // args = {0: 'a', 1: 'b', 2: 'c', length: 3}

// Slicing arguments
function logArgs() {
  const args = Array.prototype.slice.call(arguments);
  console.log(args);
}

// With spread (modern equivalent)
function logArgs(...args) {
  console.log(args);
}
```

## 11. Explain the spread operator and rest parameters.

**Answer:**

```javascript
// Spread (...) - expands iterable into individual elements
// Arrays
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5]; // [1, 2, 3, 4, 5]
const combined = [...arr1, ...arr2];
const copy = [...arr1]; // Shallow copy

// Objects
const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 }; // { a: 1, b: 2, c: 3 }
const merged = { ...obj1, ...obj2 };

// Strings
const chars = [...'hello']; // ['h', 'e', 'l', 'l', 'o']

// Function calls
const nums = [1, 2, 3];
Math.max(...nums); // 3

// Rest parameters (...) - collects remaining arguments into array
function sum(...numbers) {
  return numbers.reduce((acc, n) => acc + n, 0);
}
sum(1, 2, 3, 4); // 10

function log(greeting, ...names) {
  names.forEach(name => console.log(greeting + ', ' + name));
}
log('Hello', 'Alice', 'Bob', 'Charlie');

// Rest in destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
// first=1, second=2, rest=[3,4,5]

const { name, ...details } = { name: 'Alice', age: 30, role: 'dev' };
// name='Alice', details={age:30, role:'dev'}
```

## 12. Explain Array methods: map, filter, reduce, forEach.

**Answer:**

```javascript
const numbers = [1, 2, 3, 4, 5];

// map - transform each element, returns new array
const doubled = numbers.map(n => n * 2); // [2, 4, 6, 8, 10]

// filter - keep elements that pass test, returns new array
const evens = numbers.filter(n => n % 2 === 0); // [2, 4]

// reduce - accumulate values, returns single value
const sum = numbers.reduce((acc, n) => acc + n, 0); // 15
// With initial value: acc=0, n=1 -> acc=1, n=2 -> acc=3, n=3 -> acc=6...

// More reduce examples
const max = numbers.reduce((acc, n) => Math.max(acc, n), -Infinity);
const grouped = [1, 2, 3, 1, 2, 1].reduce((acc, n) => {
  acc[n] = (acc[n] || 0) + 1;
  return acc;
}, {}); // {1: 3, 2: 2, 3: 1}

const flattened = [[1, 2], [3, 4], [5]].reduce((acc, arr) => [...acc, ...arr], []);

// forEach - execute function on each element (no return)
numbers.forEach(n => console.log(n)); // undefined

// Chaining
const result = numbers
  .filter(n => n > 2)     // [3, 4, 5]
  .map(n => n * 2)        // [6, 8, 10]
  .reduce((a, b) => a + b); // 24
```

**Other useful Array methods:**
```javascript
const arr = [1, 2, 3, 4, 5];

arr.some(n => n > 3);  // true (at least one passes)
arr.every(n => n > 0); // true (all pass)
arr.find(n => n > 3);  // 4 (first match)
arr.findIndex(n => n > 3); // 3 (index of first match)
arr.includes(3);       // true
arr.flat();            // Flatten nested arrays
arr.flatMap(n => [n, n * 2]); // [1,2, 2,4, 3,6, 4,8, 5,10]
```

## 13. What is currying?

**Answer:** Currying transforms a function that takes multiple arguments into a sequence of functions, each taking a single argument.

```javascript
// Normal function
function add(a, b, c) {
  return a + b + c;
}

// Curried version
function curriedAdd(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

curriedAdd(1)(2)(3); // 6

// With arrow functions
const add = a => b => c => a + b + c;

// Generic curry function
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    }
    return function(...args2) {
      return curried.apply(this, args.concat(args2));
    };
  };
}

// Real-world usage
const multiply = (a, b, c) => a * b * c;
const curriedMultiply = curry(multiply);

const double = curriedMultiply(2);
const doubleByThree = double(3);
console.log(doubleByThree(4)); // 24

// Partial application vs currying
// Partial: pre-fill some arguments
const partialMultiply = multiply.bind(null, 2); // Fix 'a' to 2
partialMultiply(3, 4); // 24

// Currying: each argument gets its own function
curriedMultiply(2)(3)(4); // 24
```

## 14. Implement debounce and throttle.

**Answer:**

```javascript
// Debounce - delays execution until after a period of inactivity
// Use case: search input, auto-save, resize handler
function debounce(fn, delay) {
  let timeoutId;
  return function(...args) {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => fn.apply(this, args), delay);
  };
}

// Leading debounce - executes immediately, then debounces
function debounceLeading(fn, delay) {
  let timeoutId;
  return function(...args) {
    if (!timeoutId) fn.apply(this, args);
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => { timeoutId = null; }, delay);
  };
}

// Throttle - ensures function is called at most once per specified interval
// Use case: scroll handler, resize, rate limiting API calls
function throttle(fn, limit) {
  let inThrottle = false;
  return function(...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => { inThrottle = false; }, limit);
    }
  };
}

// Throttle with trailing call (guarantees last call executes)
function throttleTrailing(fn, limit) {
  let inThrottle, lastArgs, lastThis;
  return function(...args) {
    if (inThrottle) {
      lastArgs = args;
      lastThis = this;
      return;
    }
    fn.apply(this, args);
    inThrottle = true;
    setTimeout(() => {
      inThrottle = false;
      if (lastArgs) {
        fn.apply(lastThis, lastArgs);
        lastArgs = lastThis = null;
      }
    }, limit);
  };
}
```

**Debounce vs Throttle:**
| Aspect | Debounce | Throttle |
|--------|----------|----------|
| When it fires | After pause in events | At regular intervals |
| Example | Search while typing | Scroll at fixed rate |
| Continuous rapid events | Fires once at end | Fires at intervals |
| First call | Delayed (unless leading) | Immediate |

## 15. How do you deep clone an object?

**Answer:**

```javascript
const obj = {
  name: 'Alice',
  address: { city: 'NYC', zip: 10001 },
  hobbies: ['reading', 'coding'],
  date: new Date(),
  fn: () => {},
  undef: undefined
};

// 1. JSON methods (fast but limited - loses functions, undefined, Symbols, dates, circular)
const clone1 = JSON.parse(JSON.stringify(obj));
// Problem: date becomes string, fn/undefined are lost

// 2. Structured clone (modern browser API)
const clone2 = structuredClone(obj);
// Handles dates, Map, Set, ArrayBuffer, but NOT functions, DOM nodes

// 3. Recursive deep clone
function deepClone(value, seen = new WeakMap()) {
  if (value === null || typeof value !== 'object') return value;
  if (seen.has(value)) return seen.get(value); // Handle circular references
  if (value instanceof Date) return new Date(value);
  if (value instanceof RegExp) return new RegExp(value);
  if (value instanceof Map) {
    const map = new Map();
    seen.set(value, map);
    value.forEach((v, k) => map.set(deepClone(k, seen), deepClone(v, seen)));
    return map;
  }
  if (value instanceof Set) {
    const set = new Set();
    seen.set(value, set);
    value.forEach(v => set.add(deepClone(v, seen)));
    return set;
  }
  if (Array.isArray(value)) {
    const arr = [];
    seen.set(value, arr);
    value.forEach((item, i) => { arr[i] = deepClone(item, seen); });
    return arr;
  }
  const clone = Object.create(Object.getPrototypeOf(value));
  seen.set(value, clone);
  for (const key of [...Object.keys(value), ...Object.getOwnPropertySymbols(value)]) {
    clone[key] = deepClone(value[key], seen);
  }
  return clone;
}

// 4. Shallow clone is often sufficient
const shallow = { ...obj };    // Spread
const shallow2 = Object.assign({}, obj); // Object.assign
```

## 16. Explain the Module pattern and ES6 modules.

**Answer:**

```javascript
// ES6 Modules (static, tree-shakeable)
// math.js
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export default class Calculator { /* ... */ }

// app.js
import Calculator, { PI, add as sum } from './math.js';
import * as MathUtils from './math.js'; // Namespace import

// Dynamic imports (lazy loading)
button.addEventListener('click', async () => {
  const module = await import('./heavyModule.js');
  module.heavyFunction();
});

// Module pattern (IIFE-based, before ES6)
const CounterModule = (function() {
  let count = 0; // Private

  function log(message) {
    console.log('[Counter] ' + message);
  }

  return {
    increment() {
      count++;
      log('Count: ' + count);
    },
    decrement() {
      count--;
      log('Count: ' + count);
    },
    getCount() { return count; }
  };
})();
```

**Module features:**
- `export` - exposes bindings (not values)
- `import` - imports exported bindings (live bindings)
- `default` - single default export per module
- Static analysis enables tree-shaking
- Strict mode by default

## 17. How does garbage collection work in JavaScript?

**Answer:** JavaScript automatically manages memory through garbage collection.

**Main algorithm: Mark-and-Sweep**
1. **Mark:** GC starts from roots (global object, current function scope, etc.) and marks all reachable objects
2. **Sweep:** Unmarked objects are considered unreachable and memory is reclaimed

**Memory leak patterns:**
```javascript
// 1. Global variables
function leak() {
  leaked = 'I am global'; // No var/let/const -> global
}

// 2. Forgotten timers/intervals
function startTimer() {
  const data = getData();
  setInterval(() => {
    console.log(data); // data can't be GC'd
  }, 1000);
}

// 3. Closures holding large data
function createProcessor() {
  const hugeArray = new Array(1000000).fill('data');
  return function() { /* Uses hugeArray */ };
}

// 4. Detached DOM elements
const element = document.getElementById('button');
element.parentNode.removeChild(element);
// Reference still exists -> element not GC'd

// 5. Event listeners not removed
function addHandler() {
  const element = document.getElementById('button');
  element.addEventListener('click', () => { /* ... */ });
  // If element is removed, listener prevents GC
}
```

**V8 Optimization: Generational GC**
- **Young generation:** New objects, collected frequently (Scavenger)
- **Old generation:** Surviving objects, collected less often (Mark-Compact)
- Objects move from young to old after surviving one collection

## 18. Explain the difference between `var`, `let`, and `const`.

**Answer:**

```javascript
// Scope
if (true) {
  var v = 'function scoped';  // Or global if outside function
  let l = 'block scoped';
  const c = 'block scoped';
}
console.log(v); // 'function scoped'
console.log(l); // ReferenceError
console.log(c); // ReferenceError

// Hoisting
console.log(v1); // undefined
var v1 = 'hoisted';

console.log(l1); // ReferenceError (TDZ)
let l1 = 'hoisted but TDZ';

console.log(c1); // ReferenceError (TDZ)
const c1 = 'hoisted but TDZ';

// Reassignment
var v2 = 1; v2 = 2; // OK
let l2 = 1; l2 = 2; // OK
const c2 = 1; c2 = 2; // TypeError

// But const objects can be mutated
const obj = { a: 1 };
obj.a = 2; // OK
obj = {};  // TypeError

// Redeclaration
var v3 = 1; var v3 = 2; // OK (overwritten)
let l3 = 1; let l3 = 2; // SyntaxError
const c3 = 1; const c3 = 2; // SyntaxError
```

| | var | let | const |
|---|-----|-----|-------|
| Scope | Function | Block | Block |
| Hoisted | Yes (undefined) | Yes (TDZ) | Yes (TDZ) |
| Reassign | Yes | Yes | No |
| Redeclare | Yes | No | No |
| Global property | Yes (window.x) | No | No |

## 19. What are generators and iterators?

**Answer:**

```javascript
// Generator function - produces a sequence of values lazily
function* numberGenerator() {
  yield 1;
  yield 2;
  yield 3;
}

const gen = numberGenerator();
console.log(gen.next()); // { value: 1, done: false }
console.log(gen.next()); // { value: 2, done: false }
console.log(gen.next()); // { value: 3, done: false }
console.log(gen.next()); // { value: undefined, done: true }

// Generators are iterable
for (const value of numberGenerator()) {
  console.log(value); // 1, 2, 3
}

// Infinite generator
function* fibonacci() {
  let a = 0, b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}
const fib = fibonacci();
console.log([...Array(10)].map(() => fib.next().value)); // First 10 fib numbers

// yield* delegates to another generator
function* combined() {
  yield* [1, 2, 3];  // Delegates to array iterator
  yield* numberGenerator(); // Delegates to another generator
}

// Custom iterator
const range = {
  from: 1,
  to: 5,
  [Symbol.iterator]() {
    let current = this.from;
    const end = this.to;
    return {
      next() {
        return current <= end
          ? { value: current++, done: false }
          : { done: true };
      }
    };
  }
};

console.log([...range]); // [1, 2, 3, 4, 5]
```

## 20. How does `bind`, `call`, and `apply` differ for arrow functions?

**Answer:** Arrow functions don't have their own `this` binding - they inherit from lexical scope. So `call`, `apply`, and `bind` cannot override the `this` of an arrow function.

```javascript
const obj = {
  value: 42,
  regularFunc: function() {
    return this.value;
  },
  arrowFunc: () => {
    return this.value; // 'this' is outer scope, not obj
  }
};

console.log(obj.regularFunc()); // 42

// Cannot override this of arrow function
console.log(obj.arrowFunc.call({ value: 100 })); // undefined (outer this)
console.log(obj.arrowFunc.apply({ value: 100 })); // undefined

const boundArrow = obj.arrowFunc.bind({ value: 100 });
console.log(boundArrow()); // undefined

// Practical implication
const obj2 = {
  items: [1, 2, 3],
  process: function() {
    // Arrow function here captures 'this' from process
    this.items.forEach(item => {
      console.log(this); // obj2 (correct, due to arrow)
    });

    // Without arrow, would need that/self/context
    const self = this;
    this.items.forEach(function(item) {
      console.log(self); // Need to capture this
    });
  }
};
```

## 21. What is the Temporal Dead Zone (TDZ)?

**Answer:** The TDZ is the time between entering scope and the declaration of a `let` or `const` variable, where accessing the variable throws a ReferenceError.

```javascript
{
  // TDZ starts for 'x'
  console.log(typeof x); // ReferenceError (not 'undefined'!)
  // TDZ continues

  let x = 10; // TDZ ends
  // x is now accessible
}

// Contrast with var
{
  console.log(typeof y); // 'undefined' (not error!)
  var y = 10;
}

// Important: typeof behavior
console.log(typeof notDeclared); // 'undefined'
console.log(typeof tdzVar);      // ReferenceError!
let tdzVar = 10;

// TDZ in default parameters
function test(a = b, b = 5) {
  return a;
}
test(); // ReferenceError: b is in TDZ when a's default is evaluated

// Correct order
function test(a = 5, b = a) {
  return b; // 5
}

// TDZ in class
class MyClass {
  constructor() {
    this.value = OtherClass; // ReferenceError if OtherClass is declared after
  }
}
const OtherClass = class {}; // Must be declared before constructor uses it
```

## 22. Explain memoization.

**Answer:** Memoization caches function results based on arguments to avoid recomputation.

```javascript
// Generic memoization
function memoize(fn) {
  const cache = new Map();
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      return cache.get(key);
    }
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

// Usage
const expensiveFunction = (n) => {
  console.log('Computing...');
  return n * 2;
};

const memoized = memoize(expensiveFunction);
memoized(5); // Computing... 10
memoized(5); // 10 (from cache)
memoized(10); // Computing... 20

// Practical: Fibonacci without memoization (exponential)
function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
}

// With memoization (linear)
const fibMemo = memoize(function(n) {
  if (n <= 1) return n;
  return fibMemo(n - 1) + fibMemo(n - 2);
});

// With cache embedded
function fibWithCache(n, cache = {}) {
  if (n in cache) return cache[n];
  if (n <= 1) return n;
  cache[n] = fibWithCache(n - 1, cache) + fibWithCache(n - 2, cache);
  return cache[n];
}
```

**When to memoize:**
- Expensive computations (heavy math, data transformation)
- Recursive functions (Fibonacci, factorial)
- API calls (with appropriate invalidation)
- React components (React.memo is component-level memoization)

## 23. What is the difference between `null` and `undefined`?

**Answer:**

```javascript
// undefined: variable declared but not assigned
let a;
console.log(a); // undefined

// null: intentional absence of any object value
const b = null;

typeof null;       // "object" (historical bug)
typeof undefined;  // "undefined"

null == undefined;  // true (loose equality)
null === undefined; // false (strict equality)

// When they appear:
// undefined:
let x;                          // Declared but not assigned
const obj = {}; obj.prop;       // Missing property
function f() {}; f();           // No return value
function f(a) {}; f();          // Missing argument
const [a] = [];                 // Array destructuring missing value

// null:
document.getElementById('nonexistent'); // DOM query with no result
JSON.parse('{"a": null}');              // JSON null
Object.create(null);                     // Prototype-less object
```

## 24. Explain `Symbol` and its use cases.

**Answer:** Symbol is a primitive type that creates unique, immutable identifiers.

```javascript
// Creating symbols
const sym1 = Symbol('description');
const sym2 = Symbol('description');
console.log(sym1 === sym2); // false (always unique)

// Symbol.for creates/retrieves global symbols
const globalSym1 = Symbol.for('app.uid');
const globalSym2 = Symbol.for('app.uid');
console.log(globalSym1 === globalSym2); // true

// Use cases:

// 1. Object property keys (avoid name collisions)
const LOGIN = Symbol('login');
const user = {
  name: 'Alice',
  [LOGIN]: () => 'Logged in'
};

// 2. Defining constants with unique values
const Colors = {
  RED: Symbol('red'),
  GREEN: Symbol('green'),
  BLUE: Symbol('blue')
};

// 3. Well-known symbols (metaprogramming)
// Symbol.iterator - make object iterable
const iterable = {
  *[Symbol.iterator]() { yield 1; yield 2; yield 3; }
};

// Symbol.toStringTag - custom toString representation
const CustomClass = {
  [Symbol.toStringTag]: 'CustomClass'
};
console.log(Object.prototype.toString.call(CustomClass)); // [object CustomClass]

// Symbol.hasInstance - custom instanceof behavior
class MyArray {
  static [Symbol.hasInstance](instance) {
    return Array.isArray(instance);
  }
}
console.log([] instanceof MyArray); // true

// 4. Private-like properties
const _private = Symbol('private');
class Demo {
  constructor() {
    this[_private] = 'secret';
  }
  getSecret() { return this[_private]; }
}
```

## 25. How do you handle errors in JavaScript?

**Answer:**

```javascript
// try/catch/finally
try {
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  console.error('Error:', error.message);
  // Different error types
  if (error instanceof TypeError) {
    // Handle type errors
  } else if (error instanceof RangeError) {
    // Handle range errors
  } else if (error instanceof ReferenceError) {
    // Handle reference errors
  } else {
    // Generic error handling
  }
} finally {
  // Always executes (cleanup)
  cleanup();
}

// Custom error classes
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}

class NetworkError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.name = 'NetworkError';
    this.statusCode = statusCode;
  }
}

function validateUser(user) {
  if (!user.name) throw new ValidationError('Name is required', 'name');
  if (!user.email) throw new ValidationError('Email is required', 'email');
}

// Async error handling
async function fetchData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) throw new NetworkError('Bad response', response.status);
    return await response.json();
  } catch (error) {
    if (error instanceof NetworkError) {
      showError(error.message);
    } else {
      logError(error);
      showGenericError();
    }
  }
}

// Global error handlers
window.onerror = (message, source, lineno, colno, error) => {
  console.error('Global error:', { message, source, lineno, colno, error });
};

window.addEventListener('unhandledrejection', (event) => {
  console.error('Unhandled promise rejection:', event.reason);
  event.preventDefault();
});
```

## 26. Explain `Promise.all`, `Promise.race`, `Promise.allSettled`, and `Promise.any`.

**Answer:**

```javascript
const p1 = Promise.resolve(1);
const p2 = new Promise(resolve => setTimeout(() => resolve(2), 1000));
const p3 = new Promise((_, reject) => setTimeout(() => reject('Error 3'), 500));
const p4 = Promise.resolve(4);

// Promise.all - short-circuits on first rejection
Promise.all([p1, p2, p4])
  .then(([r1, r2, r4]) => console.log('All:', r1, r2, r4))
  .catch(err => console.error('All failed:', err));

// Promise.allSettled - waits for all to complete (resolve OR reject)
Promise.allSettled([p1, p2, p3, p4])
  .then(results => {
    results.forEach(result => {
      if (result.status === 'fulfilled') {
        console.log('Fulfilled:', result.value);
      } else {
        console.error('Rejected:', result.reason);
      }
    });
  });

// Promise.race - resolves/rejects with first settled promise
Promise.race([p2, p3])
  .then(value => console.log('Race winner:', value))
  .catch(err => console.error('Race error:', err)); // p3 rejects first (500ms)

// Promise.any - resolves with first fulfilled, rejects only if all reject
Promise.any([p3, Promise.reject('Error 4')])
  .then(value => console.log('Any value:', value))
  .catch(err => console.error('Any error:', err));

// Custom: Promise with timeout
function promiseWithTimeout(promise, ms) {
  const timeout = new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Timeout')), ms)
  );
  return Promise.race([promise, timeout]);
}
```

## 27. How does `Object.create` differ from `new`?

**Answer:**

```javascript
// Using constructor + new
function Person(name) {
  this.name = name;
}
Person.prototype.greet = function() {
  return 'Hi, I am ' + this.name;
};

const p1 = new Person('Alice');
// What new does:
// 1. Creates a new empty object
// 2. Sets prototype to Person.prototype
// 3. Calls Person with 'this' = new object
// 4. Returns the object (unless constructor returns object)

// Using Object.create
const personProto = {
  greet() { return 'Hi, I am ' + this.name; }
};

function createPerson(name) {
  const obj = Object.create(personProto);
  obj.name = name;
  return obj;
}

const p2 = createPerson('Bob');

// Object.create(null) - creates object without prototype
const dict = Object.create(null);
dict.key = 'value';
console.log(dict.toString); // undefined (no prototype chain)
```

| Aspect | `new` | `Object.create` |
|--------|-------|-----------------|
| Constructor | Called automatically | You call it yourself |
| Prototype | Set to Constructor.prototype | Explicitly specified |
| Initialization | In constructor | Manual |
| Flexibility | Less (fixed constructor pattern) | More (any prototype, null) |

## 28. What is the `new` keyword doing internally?

**Answer:**

```javascript
// What 'new' does:
function myNew(Constructor, ...args) {
  // 1. Create a new empty object
  const obj = {};
  
  // 2. Set prototype to Constructor.prototype
  Object.setPrototypeOf(obj, Constructor.prototype);
  
  // 3. Call constructor with 'this' = obj
  const result = Constructor.apply(obj, args);
  
  // 4. Return the object (or result if it's an object)
  return (typeof result === 'object' && result !== null) || typeof result === 'function'
    ? result
    : obj;
}

// Usage
function Person(name) {
  this.name = name;
}

const p = myNew(Person, 'Alice');
console.log(p.name); // Alice
console.log(p instanceof Person); // true

// If constructor returns an object:
function SpecialPerson(name) {
  this.name = name;
  return { custom: 'object' }; // Overrides 'this'
}

const sp = new SpecialPerson('Bob');
console.log(sp.name); // undefined
console.log(sp.custom); // 'object'
console.log(sp instanceof SpecialPerson); // false
```

## 29. Explain the Set, Map, WeakSet, and WeakMap.

**Answer:**

```javascript
// Set - unique values
const set = new Set([1, 2, 3, 2, 1]);
console.log(set.size); // 3
set.add(4).add(5);
set.has(2); // true
set.delete(3);

// Iteration
for (const item of set) { }
set.forEach(value => { });

// Set operations
const a = new Set([1, 2, 3]);
const b = new Set([2, 3, 4]);

const union = new Set([...a, ...b]); // {1, 2, 3, 4}
const intersection = new Set([...a].filter(x => b.has(x))); // {2, 3}
const difference = new Set([...a].filter(x => !b.has(x))); // {1}

// Map - key-value pairs (any type as key)
const map = new Map();
map.set('name', 'Alice');
map.set(42, 'answer');
map.set({ id: 1 }, 'object key');

const obj = {};

// Map vs Object comparison
const map2 = new Map();
const obj2 = {};

map2.set('key', 'value');
obj2.key = 'value';

map2.size;        // 1 (built-in)
Object.keys(obj2).length; // Need to compute

map2.has('key');  // true
'key' in obj2;    // true

// WeakSet - only objects, no iteration, auto-GC
let user = { name: 'Alice' };
const weakSet = new WeakSet();
weakSet.add(user);
weakSet.has(user); // true
user = null; // user is GC'd, weakSet entry removed automatically

// WeakMap - only object keys, no iteration, auto-GC
let widget = document.getElementById('widget');
const weakMap = new WeakMap();
weakMap.set(widget, { id: 1, data: 'metadata' });
// When widget is removed from DOM and all refs are gone, entry is GC'd
```

**When to use WeakMap:**
- Storing private data associated with objects
- DOM element metadata (auto-cleaned when element removed)
- Preventing memory leaks in event listeners/observers

## 30. How does the Reflect API work?

**Answer:** Reflect provides methods for interceptable JavaScript operations, similar to Proxy traps.

```javascript
// Reflect vs traditional
const obj = {};

// Instead of:
obj.prop = 'value';
'prop' in obj;
delete obj.prop;

// Use Reflect:
Reflect.set(obj, 'prop', 'value');
Reflect.has(obj, 'prop'); // true
Reflect.deleteProperty(obj, 'prop');

// Reflect.get
const target = { a: 1, get b() { return this.a + 1; } };
Reflect.get(target, 'a'); // 1
Reflect.get(target, 'b'); // 2
Reflect.get(target, 'b', { a: 5 }); // 6 (receiver = custom 'this')

// Reflect.construct (alternative to new)
function Person(name) { this.name = name; }
const p = Reflect.construct(Person, ['Alice']);

// Reflect.defineProperty
Reflect.defineProperty(obj, 'key', {
  value: 42,
  writable: false,
  enumerable: true
});

// Reflect.apply (instead of Function.prototype.apply)
const numbers = [1, 2, 3];
Reflect.apply(Math.max, null, numbers); // 3

// Reflect.ownKeys - returns all own keys (strings + symbols)
Reflect.ownKeys(obj); // ['a', 'b', Symbol(c)]

// Proxy + Reflect (forward operations)
const proxy = new Proxy(target, {
  get(target, prop, receiver) {
    console.log('Getting ' + String(prop));
    return Reflect.get(target, prop, receiver);
  },
  set(target, prop, value, receiver) {
    console.log('Setting ' + String(prop) + ' = ' + value);
    return Reflect.set(target, prop, value, receiver);
  }
});
```

## 31. What are Proxies used for?

**Answer:** Proxy creates a wrapper that intercepts and customizes operations on an object.

```javascript
const target = { name: 'Alice', age: 30 };

const handler = {
  get(target, prop, receiver) {
    if (prop in target) {
      return Reflect.get(target, prop, receiver);
    }
    return 'Property "' + String(prop) + '" does not exist';
  },
  set(target, prop, value, receiver) {
    if (prop === 'age' && (typeof value !== 'number' || value < 0)) {
      throw new TypeError('Age must be a positive number');
    }
    return Reflect.set(target, prop, value, receiver);
  }
};

const proxy = new Proxy(target, handler);
console.log(proxy.name); // Alice
proxy.age = -5; // TypeError

// Use cases:

// 1. Validation
const validator = {
  set(target, prop, value) {
    if (prop === 'email' && !value.includes('@')) throw new Error('Invalid email');
    return Reflect.set(target, prop, value);
  }
};

// 2. Logging/Auditing
const logger = {
  get(target, prop, receiver) {
    console.log('[GET] ' + String(prop));
    return Reflect.get(target, prop, receiver);
  },
  set(target, prop, value, receiver) {
    console.log('[SET] ' + String(prop) + ' = ' + JSON.stringify(value));
    return Reflect.set(target, prop, value, receiver);
  }
};

// 3. Reactive systems (Vue 3 reactivity)
function reactive(obj) {
  return new Proxy(obj, {
    get(target, prop, receiver) {
      track(target, prop); // Dependency tracking
      return Reflect.get(target, prop, receiver);
    },
    set(target, prop, value, receiver) {
      const oldValue = Reflect.get(target, prop, receiver);
      const result = Reflect.set(target, prop, value, receiver);
      if (oldValue !== value) trigger(target, prop); // Notify watchers
      return result;
    }
  });
}
```

## 32. Explain the event propagation (bubbling vs capturing).

**Answer:**

```
Event flow: Capturing -> Target -> Bubbling
```

```javascript
// Bubbling (default) - event goes from target up to root
document.getElementById('child').addEventListener('click', () => {
  console.log('Child bubble');
});

document.getElementById('parent').addEventListener('click', () => {
  console.log('Parent bubble'); // Fires after child
});

document.addEventListener('click', () => {
  console.log('Document bubble'); // Fires last
});

// Capturing (third argument true)
document.getElementById('parent').addEventListener('click', () => {
  console.log('Parent capture'); // Fires first
}, true);

// Stopping propagation
child.addEventListener('click', (e) => {
  e.stopPropagation(); // Prevents further propagation
});

// Event delegation (using bubbling)
document.getElementById('list').addEventListener('click', (e) => {
  if (e.target.tagName === 'LI') {
    console.log('Clicked:', e.target.textContent);
  }
});
```

## 33. What is the difference between `let` and `var` in loops?

**Answer:**

```javascript
// var - function scoped, one binding for all iterations
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3 (all closures reference same i = 3)

// let - block scoped, new binding per iteration
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2 (each closure gets its own i)

// IIFE fix for var
for (var i = 0; i < 3; i++) {
  ((j) => {
    setTimeout(() => console.log(j), 100);
  })(i);
}
// Output: 0, 1, 2
```

## 34. How does `instanceof` work?

**Answer:** `instanceof` checks if an object's prototype chain contains a specific constructor's prototype.

```javascript
class Animal {}
class Dog extends Animal {}

const dog = new Dog();

console.log(dog instanceof Dog);    // true
console.log(dog instanceof Animal); // true (prototype chain)
console.log(dog instanceof Object); // true
console.log(dog instanceof Array);  // false

// Custom instanceof behavior with Symbol.hasInstance
class MyArray {
  static [Symbol.hasInstance](instance) {
    return Array.isArray(instance);
  }
}

console.log([] instanceof MyArray); // true

// Manual instanceof implementation
function myInstanceOf(obj, constructor) {
  let proto = Object.getPrototypeOf(obj);
  while (proto !== null) {
    if (proto === constructor.prototype) return true;
    proto = Object.getPrototypeOf(proto);
  }
  return false;
}

// Better type checking with Object.prototype.toString
function getType(value) {
  return Object.prototype.toString.call(value).slice(8, -1);
}

getType([]);     // "Array"
getType({});     // "Object"
getType(null);   // "Null"
getType('abc');  // "String"
getType(new Date()); // "Date"
```

## 35. What is `JSON.stringify` and `JSON.parse` limitations?

**Answer:**

```javascript
// JSON.parse - converts JSON string to object
const data = JSON.parse('{"name":"Alice","age":30}');

// With reviver
const parsed = JSON.parse('{"date":"2025-01-15T00:00:00.000Z"}', (key, value) => {
  if (key === 'date') return new Date(value);
  return value;
});

// JSON.stringify - converts object to JSON string
const obj = { name: 'Alice', age: 30 };
JSON.stringify(obj); // '{"name":"Alice","age":30}'

// Limitations - what gets lost/transformed:
const test = {
  undefined: undefined,      // Missing (key removed)
  function: () => {},        // Missing (key removed)
  symbol: Symbol('test'),    // Missing (key removed)
  nan: NaN,                  // Converted to null
  infinity: Infinity,        // Converted to null
  date: new Date(),          // Converted to string
  regex: /test/,             // Converted to {} or null
  error: new Error(),        // Converted to {}
  map: new Map([['a', 1]]),  // Converted to {}
  set: new Set([1, 2]),      // Converted to {}
  bigint: BigInt(1),         // TypeError
};

// Circular references -> TypeError
const circular = { name: 'Alice' };
circular.self = circular;
// JSON.stringify(circular); // TypeError: Converting circular structure to JSON

// Fix circular with custom serializer
function safeStringify(obj, space = 2) {
  const seen = new WeakSet();
  return JSON.stringify(obj, (key, value) => {
    if (typeof value === 'object' && value !== null) {
      if (seen.has(value)) return '[Circular]';
      seen.add(value);
    }
    return value;
  }, space);
}
```

## 36. Explain the concept of purity and side effects.

**Answer:**

```javascript
// Pure function:
// 1. Same input -> same output (deterministic)
// 2. No side effects (doesn't modify external state)

// Pure:
function add(a, b) {
  return a + b;
}

function toUpperCase(str) {
  return str.toUpperCase(); // Doesn't modify original string
}

// Impure:
let counter = 0;
function increment() {
  counter++; // Modifies external state
  return counter;
}

function random() {
  return Math.random(); // Not deterministic
}

function badSort(arr) {
  return arr.sort(); // Mutates the original array!
}

// Good sort (pure):
function goodSort(arr) {
  return [...arr].sort();
}

// Side effects include:
// - Modifying external variables / objects
// - DOM manipulation
// - Network requests / API calls
// - Console/logging
// - File system operations
// - Date.now() / Math.random()

// Functional programming benefits:
// - Easy to test (no setup)
// - Easy to reason about
// - Memoizable
// - Referentially transparent (can replace with value)
```

## 37. What are TypedArrays?

**Answer:** TypedArrays provide a way to work with raw binary data in memory-efficient buffers.

```javascript
// ArrayBuffer - fixed-length binary data buffer
const buffer = new ArrayBuffer(16); // 16 bytes
console.log(buffer.byteLength); // 16

// TypedArray views - interpret buffer as specific numeric types
const int32 = new Int32Array(buffer); // 4 entries (16/4 = 4)
const float64 = new Float64Array(buffer); // 2 entries (16/8 = 2)
const uint8 = new Uint8Array(buffer); // 16 entries (16/1 = 16)

// Creating TypedArrays directly
const arr = new Uint8Array([0, 255, 127, 64]);
arr[0]; // 0
arr[1]; // 255
arr[2]; // 127

// DataView - flexible view for mixed types
const view = new DataView(buffer);
view.setInt32(0, 42, true); // Write int32 at byte 0 (little-endian)
view.setFloat64(4, 3.14159, true); // Write float64 at byte 4
view.getInt32(0, true); // 42
view.getFloat64(4, true); // 3.14159

// Use cases:
// - Canvas pixel data (ImageData.data is Uint8ClampedArray)
// - Web Audio API (audio buffers)
// - File/network protocols (binary data parsing)
// - WebGL (vertex data, textures)
// - Crypto operations
// - Image processing
```

## 38. How does `structuredClone` work?

**Answer:** `structuredClone` (ES2023) deep-clones objects using the structured clone algorithm.

```javascript
const original = {
  name: 'Alice',
  scores: [100, 95, 87],
  address: { city: 'NYC' },
  date: new Date('2025-01-15'),
  map: new Map([['key', 'value']]),
  set: new Set([1, 2, 3])
};

const clone = structuredClone(original);

// Deep copy - modifying clone doesn't affect original
clone.address.city = 'LA';
console.log(original.address.city); // 'NYC'

// Types supported:
// - Objects, arrays, primitives
// - Date, RegExp, Map, Set
// - ArrayBuffer, TypedArray
// - Blob, File, ImageData
// - Error types

// Types NOT supported:
// - Functions
// - DOM elements
// - Symbol properties
// - WeakMap, WeakSet
// - Prototype chain (clones are plain objects)

// Handles circular references automatically
const circular = { name: 'Alice' };
circular.self = circular;
const cloned = structuredClone(circular); // Works!
```

## 39. How does the `export` and `import` syntax work in detail?

**Answer:**

```javascript
// --- Named exports ---
// lib/math.js
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export class Calculator {}

// Or export separately
const PI = 3.14159;
function add(a, b) { return a + b; }
export { PI, add };

// Rename exports
export { PI as MathPI, add as sum };

// --- Default export (one per module) ---
// lib/Button.js
export default class Button { }

// Or
class Button {}
export { Button as default };

// --- Importing ---
// app.js

// Named imports
import { PI, add, Calculator } from './lib/math.js';

// Rename imports
import { add as sum, PI as MathPI } from './lib/math.js';

// Import all as namespace
import * as Math from './lib/math.js';
Math.PI;
Math.add(1, 2);

// Default import
import Button from './lib/Button.js';

// Combined
import React, { useState, useEffect } from 'react';

// Side-effect import (runs module code, no bindings)
import './polyfills.js';

// Dynamic import (returns Promise)
import('./heavyModule.js')
  .then(module => {
    module.doSomething();
  });

// Re-export (aggregating submodules)
export { default as Button } from './Button.js';
export { Input, Select } from './FormElements.js';
export * from './utils.js';

// Import attributes
import data from './data.json' with { type: 'json' };
```

## 40. Common JavaScript coding interview questions.

**Answer:**

```javascript
// 1. Flatten a nested array
function flatten(arr) {
  return arr.reduce((acc, val) =>
    Array.isArray(val) ? acc.concat(flatten(val)) : acc.concat(val), []
  );
}
flatten([1, [2, [3, 4], 5], 6]); // [1, 2, 3, 4, 5, 6]

// 2. Implement Array.prototype.map
function myMap(arr, callback) {
  const result = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(callback(arr[i], i, arr));
  }
  return result;
}

// 3. Implement Array.prototype.reduce
function myReduce(arr, callback, initialValue) {
  let accumulator = initialValue !== undefined ? initialValue : arr[0];
  let startIndex = initialValue !== undefined ? 0 : 1;
  for (let i = startIndex; i < arr.length; i++) {
    accumulator = callback(accumulator, arr[i], i, arr);
  }
  return accumulator;
}

// 4. Implement Promise.all
function promiseAll(promises) {
  return new Promise((resolve, reject) => {
    const results = [];
    let completed = 0;
    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then(value => {
          results[index] = value;
          completed++;
          if (completed === promises.length) resolve(results);
        })
        .catch(reject);
    });
    if (promises.length === 0) resolve(results);
  });
}

// 5. Implement throttle
function throttle(fn, limit) {
  let inThrottle = false;
  return function(...args) {
    if (!inThrottle) {
      fn.apply(this, args);
      inThrottle = true;
      setTimeout(() => { inThrottle = false; }, limit);
    }
  };
}

// 6. Deep equal comparison
function deepEqual(a, b) {
  if (a === b) return true;
  if (a === null || b === null || typeof a !== 'object' || typeof b !== 'object') {
    return false;
  }
  const keysA = Object.keys(a);
  const keysB = Object.keys(b);
  if (keysA.length !== keysB.length) return false;
  return keysA.every(key => keysB.includes(key) && deepEqual(a[key], b[key]));
}

// 7. Memoize function
function memoize(fn) {
  const cache = new Map();
  return function(...args) {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

// 8. Once - ensure function is called only once
function once(fn) {
  let called = false;
  let result;
  return function(...args) {
    if (!called) {
      result = fn.apply(this, args);
      called = true;
    }
    return result;
  };
}
```
