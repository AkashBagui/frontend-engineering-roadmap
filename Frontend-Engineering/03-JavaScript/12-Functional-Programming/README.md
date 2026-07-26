# Functional Programming in JavaScript

## Core Principles

| Principle | Description |
|-----------|-------------|
| **Pure Functions** | Same input → same output, no side effects |
| **Immutability** | Data is never mutated, always copied |
| **Higher-Order Functions** | Functions that take/return functions |
| **Function Composition** | Combine small functions to build complex ones |
| **Declarative** | What to do, not how to do it |

## Pure Functions

```js
// Pure — deterministic, no side effects
function add(a, b) { return a + b; }
function toUpper(str) { return str.toUpperCase(); }

// Impure — side effects
let tax = 0.1;
function addTax(price) { return price * tax; } // depends on external
function log(value) { console.log(value); }    // side effect (I/O)
function random(max) { return Math.random() * max; } // not deterministic
```

## Immutability

```js
// ❌ Mutating
const user = { name: "Alice", age: 30 };
user.age = 31;

// ✅ Immutable update
const updated = { ...user, age: 31 };

// ❌ Mutating array
const nums = [1, 2, 3];
nums.push(4);

// ✅ Immutable
const appended = [...nums, 4];
const mapped = nums.map(x => x * 2);
const filtered = nums.filter(x => x > 1);

// For nested objects — use structuredClone or immer
const newState = structuredClone(state);
newState.user.profile.age = 31;
```

## Higher-Order Functions

```js
// Takes a function
function withLogging(fn) {
  return (...args) => {
    console.log(`Calling with ${args}`);
    const result = fn(...args);
    console.log(`Result: ${result}`);
    return result;
  };
}

const addWithLog = withLogging((a, b) => a + b);
addWithLog(2, 3); // logs: Calling with 2,3 / Result: 5
```

## Currying

```js
// Transform f(a, b, c) → f(a)(b)(c)
const add = a => b => a + b;
const add5 = add(5);
add5(3); // 8

// Generic curry
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) return fn(...args);
    return (...more) => curried(...args, ...more);
  };
}

const curriedAdd = curry((a, b, c) => a + b + c);
curriedAdd(1)(2)(3); // 6
curriedAdd(1, 2)(3); // 6
```

## Function Composition

```js
const compose = (...fns) => x => fns.reduceRight((v, f) => f(v), x);
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);

const toUpper = s => s.toUpperCase();
const exclaim = s => `${s}!`;
const repeat = s => `${s} ${s}`;

const shout = compose(exclaim, toUpper, repeat);
shout("hello"); // "HELLO! HELLO!" — right to left

const shoutPipe = pipe(repeat, toUpper, exclaim);
shoutPipe("hello"); // "HELLO HELLO!" — left to right
```

## Functors

A functor implements a `map` method that applies a function to the wrapped value:

```js
// Array is a functor
[1, 2, 3].map(x => x * 2); // [2, 4, 6]

// Implement a simple functor
const Box = x => ({
  map: f => Box(f(x)),
  fold: f => f(x),
  toString: () => `Box(${x})`
});

const result = Box(5)
  .map(x => x * 2)
  .map(x => x + 1)
  .fold(x => x);
// result = 11
```

## Monads Basics

A monad is a functor with `flatMap` (also called `chain` or `bind`):

```js
// Maybe monad (handles null/undefined)
const Maybe = value => ({
  map: f => value == null ? Maybe(null) : Maybe(f(value)),
  flatMap: f => value == null ? Maybe(null) : f(value),
  getOrElse: defaultVal => value ?? defaultVal
});

const result = Maybe(5)
  .map(x => x * 2)
  .flatMap(x => x > 5 ? Maybe(x) : Maybe(null))
  .getOrElse(0);
// result = 10

const nothing = Maybe(null)
  .map(x => x * 2)   // skipped
  .flatMap(x => ...) // skipped
  .getOrElse(0);
// result = 0
```

## Real-World FP Patterns

### Data Transform Pipeline
```js
const processUsers = pipe(
  filter(u => u.active),
  map(u => ({ ...u, fullName: `${u.first} ${u.last}` })),
  sortBy(u => u.age),
  groupBy(u => u.department)
);
```

### Either Monad (Error Handling)
```js
const Right = value => ({
  map: f => Right(f(value)),
  flatMap: f => f(value),
  fold: (_, g) => g(value),
  isRight: true
});

const Left = value => ({
  map: _ => Left(value),
  flatMap: _ => Left(value),
  fold: (f, _) => f(value),
  isRight: false
});

const parseJSON = str => {
  try { return Right(JSON.parse(str)); }
  catch (e) { return Left(e); }
};

const result = parseJSON('{"name":"Alice"}')
  .map(obj => obj.name)
  .fold(
    err => `Error: ${err.message}`,
    name => `Hello ${name}`
  );
// "Hello Alice"
```

## FP vs OOP

| Aspect | FP | OOP |
|--------|----|-----|
| Data & Behavior | Separate (data → functions) | Combined (objects with methods) |
| State | Immutable | Mutable |
| Composition | Function composition | Class inheritance / mixins |
| Side Effects | Isolated (controlled) | Allowed in methods |
| Loops | Recursion, map/filter/reduce | for/while |
| Focus | What to compute | Who does what |
