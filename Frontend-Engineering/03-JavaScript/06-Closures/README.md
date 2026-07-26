# Closures in JavaScript

## What is a Closure?

A closure is a function that retains access to its outer (lexical) scope even after the outer function has finished executing.

```js
function outer(x) {
  // Inner function "closes over" x
  return function inner(y) {
    return x + y;
  };
}
const add5 = outer(5);
add5(3); // 8 — inner still has access to x=5
```

## Lexical Scoping

```js
const global = "global";

function outer() {
  const outerVar = "outer";

  function inner() {
    const innerVar = "inner";
    console.log(global, outerVar, innerVar); // all accessible
  }

  inner();
}
outer();
```

## Visualizing Closures

```
Outer Execution Context (outer)
┌──────────────────────────────┐
│ Scope Chain:                  │
│ ┌─────────┐                   │
│ │ outer   │  x = 5           │
│ │  AO     │                   │
│ └─────────┘                   │
│ ┌─────────┐                    │
│ │ Global  │                   │
│ │  VO     │                   │
│ └─────────┘                    │
│                              │
│ inner function ▼              │
│ [[Scopes]]: [outer, global]  │ ← closure created
│                              │
│ When outer returns:           │
│ inner's [[Scopes]] still      │
│ holds reference to outer's AO │ ← closure persists!
└──────────────────────────────┘
```

## Common Use Cases

### 1. Module Pattern (Data Privacy)
```js
const Counter = (() => {
  let count = 0;  // private variable

  return {
    increment: () => ++count,
    decrement: () => --count,
    reset: () => count = 0,
    getCount: () => count
  };
})();

Counter.increment(); // 1
Counter.increment(); // 2
Counter.getCount();  // 2
// count is NOT accessible: Counter.count → undefined
```

### 2. Currying
```js
const multiply = a => b => a * b;
const double = multiply(2);
const triple = multiply(3);
double(5);  // 10
triple(5);  // 15

// Real-world — configurable logger
const log = level => msg => console.log(`[${level}] ${msg}`);
const info = log("INFO");
const error = log("ERROR");
info("Server started");
error("Connection failed");
```

### 3. Memoization (Caching)
```js
function memoize(fn) {
  const cache = new Map();
  return arg => {
    if (cache.has(arg)) {
      console.log("from cache");
      return cache.get(arg);
    }
    const result = fn(arg);
    cache.set(arg, result);
    return result;
  };
}

const factorial = memoize(n =>
  n <= 1 ? 1 : n * factorial(n - 1)
);
factorial(5); // computes
factorial(5); // from cache
```

### 4. Event Handlers with Private State
```js
function setupButton(buttonId, label) {
  let clicks = 0;
  const button = document.getElementById(buttonId);
  button.textContent = label;

  button.addEventListener("click", () => {
    clicks++;
    button.textContent = `${label} (${clicks})`;
  });
}
setupButton("btn1", "Click Me");
// Each button has its own private clicks variable
```

### 5. Function Factory
```js
function createGreeting(greeting) {
  return (name) => `${greeting}, ${name}!`;
}

const sayHi = createGreeting("Hi");
const sayHello = createGreeting("Hello");
sayHi("Alice");   // "Hi, Alice!"
sayHello("Bob");  // "Hello, Bob!"
```

## Common Interview Question

```js
// Classic loop closure problem
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 3, 3, 3 (all reference same i)

// Fix 1: Use let (block scope)
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// Output: 0, 1, 2

// Fix 2: IIFE closure
for (var i = 0; i < 3; i++) {
  ((j) => setTimeout(() => console.log(j), 100))(i);
}
// Output: 0, 1, 2
```

## Memory Leak Risks

```js
// ⚠️ Accidental closure retaining large data
function createLeak() {
  const largeData = new Array(1000000).fill("data");
  return function() {
    console.log("closure still retains largeData");
  };
}
const leaky = createLeak();
// largeData cannot be GC'd because leaky holds a reference

// ✅ Nullify when done
function createSafe() {
  const largeData = new Array(1000000).fill("data");
  const result = () => console.log(largeData.length);
  // If we don't need largeData in closure:
  return () => console.log("no reference to large data");
}
```

## Summary

| Aspect | Description |
|--------|-------------|
| Definition | Function + its lexical scope bundled together |
| Creation | Created every time a function is defined inside another |
| Lifetime | Persists as long as references to the inner function exist |
| Memory | Closure variables are not GC'd until all references are released |
| Use cases | Module pattern, currying, memoization, event handlers, factories |
