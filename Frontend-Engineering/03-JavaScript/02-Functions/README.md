# Functions in JavaScript

## Function Declarations vs Expressions

```js
// Declaration — hoisted (can be called before definition)
function greet(name) {
  return `Hello, ${name}!`;
}

// Expression — not hoisted
const greet = function(name) {
  return `Hello, ${name}!`;
};
```

```
Hoisting Behavior:
        Execution Context

  ┌─────────────────────────┐
  │ Creation Phase          │
  │ ┌─────────────────┐    │
  │ │ greet: function  │ ← hoisted (declaration)
  │ │ greet2: undefined│ ← hoisted (expression)
  │ └─────────────────┘    │
  │ Execution Phase        │
  │ greet() → OK           │
  │ greet2() → TypeError   │
  └─────────────────────────┘
```

## Arrow Functions

```js
// Compact syntax
const add = (a, b) => a + b;
const square = x => x * x;
const noArgs = () => 42;

// Block body — needs explicit return
const sum = (a, b) => {
  const result = a + b;
  return result;
};

// Returning object literal — wrap in ()
const createUser = (name, age) => ({ name, age });

// Key differences from regular functions:
// 1. No own `this` (lexical from enclosing scope)
// 2. No `arguments` object
// 3. Cannot be used as constructor (no `new`)
// 4. Cannot be used as generator
```

## Parameters & Arguments

```js
function logParams(a, b = 10, ...rest) {
  console.log(a, b, rest);
  console.log(arguments); // array-like (not in arrow functions)
}
logParams(1, 2, 3, 4, 5); // 1, 2, [3,4,5]
```

### Default Parameters (ES6)
```js
function multiply(a, b = 1) {
  return a * b;
}
multiply(5);    // 5
multiply(5, 2); // 10
```

### Rest Parameters
```js
function sum(...nums) {
  return nums.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4); // 10
```

### Spread in Function Calls
```js
const arr = [1, 2, 3];
Math.max(...arr); // 3
```

## IIFE (Immediately Invoked Function Expression)

```js
// Classic
(function() {
  var privateVar = "secret";
  console.log("Executed immediately");
})();

// Arrow version
(() => {
  console.log("IIFE with arrow");
})();

// With parameters
((msg) => {
  console.log(msg);
})("Hello");

// Module pattern (returning object)
const Counter = (() => {
  let count = 0;
  return {
    increment: () => ++count,
    decrement: () => --count,
    getCount: () => count
  };
})();
```

## Callbacks

```js
function fetchData(callback) {
  setTimeout(() => {
    callback(null, { id: 1, name: "Data" });
  }, 1000);
}

fetchData((err, data) => {
  if (err) return console.error(err);
  console.log(data.name);
});
```

```
Visual: Callback Flow

  fetchData(callback)
      │
      ├──→ async operation
      │       │
      │       └──→ on complete
      │               │
      └──→ callback(err, data)
                      │
                      └──→ handle result
```

## Higher-Order Functions

Functions that take functions as arguments or return functions:

```js
// Takes a function (callback)
function repeat(n, action) {
  for (let i = 0; i < n; i++) action(i);
}
repeat(3, console.log); // 0, 1, 2

// Returns a function
function createMultiplier(factor) {
  return (x) => x * factor;
}
const double = createMultiplier(2);
double(5); // 10

// Built-in HOF: map, filter, reduce
[1, 2, 3].map(x => x * 2); // [2, 4, 6]
```

## Pure Functions vs Impure

```js
// Pure — same input → same output, no side effects
const add = (a, b) => a + b;

// Impure — modifies external state
let counter = 0;
const increment = () => counter++;
```

## Function Composition

```js
const compose = (f, g) => x => f(g(x));
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);

const toUpper = s => s.toUpperCase();
const exclaim = s => `${s}!`;
const shout = compose(exclaim, toUpper);
shout("hello"); // "HELLO!"
```
