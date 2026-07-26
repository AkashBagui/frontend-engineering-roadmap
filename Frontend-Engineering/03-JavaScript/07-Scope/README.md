# Scope in JavaScript

## Types of Scope

| Scope | Keyword | Where |
|-------|---------|-------|
| Global | `var`, `let`, `const` | Outside any function/block |
| Function | `var` | Inside a function |
| Block | `let`, `const` | Inside `{ }` (if, for, while, etc.) |
| Module | `export`/`import` | ES module scope |

## Global Scope

```js
// Variables declared outside any function
const appName = "MyApp";
var oldWay = "legacy";

function test() {
  console.log(appName); // accessible
}
```

## Function Scope

```js
function demo() {
  var functionScoped = "I'm inside function";
  let alsoFunctionScoped = "block-scoped but function-bound";
  const same = "same here";
}
console.log(functionScoped); // ReferenceError!
```

## Block Scope

```js
if (true) {
  var x = 1;      // NOT block scoped — function/global!
  let y = 2;      // block scoped
  const z = 3;    // block scoped
}
console.log(x); // 1 (ok, var ignores block)
console.log(y); // ReferenceError

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 0, 1, 2
}
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100); // 3, 3, 3
}
```

## Lexical Scope

Inner functions can access variables of their outer functions — determined by where the function is *defined*, not called.

```js
const global = "global";

function outer() {
  const outerVar = "outer";

  function inner() {
    const innerVar = "inner";
    console.log(innerVar);  // OK
    console.log(outerVar);  // OK — lexical scope
    console.log(global);    // OK
  }

  console.log(outerVar);    // OK
  console.log(innerVar);    // ReferenceError
  inner();
}
outer();
```

## Scope Chain Diagram

```
Scope Chain for inner():
                       
global ──────────────────────────────────────────────
│  outer()                                            │
│  ┌─────────────────────────────────────────────┐    │
│  │ inner()                                      │    │
│  │ ┌──────────────────────────────────────┐    │    │
│  │ │  innerVar: "inner"                    │    │    │
│  │ └──────────────────────────────────────┘    │    │
│  │  outerVar: "outer"                         │    │
│  │  (via [[Scopes]] reference)                │    │
│  └─────────────────────────────────────────────┘    │
│  globalVar: "global"                                │
└─────────────────────────────────────────────────────┘

Lookup order:
1. inner() local scope
2. outer() scope (via closure)
3. Global scope
4. Stop (ReferenceError if not found)
```

## Variable Shadowing

```js
let value = "global";

function outer() {
  let value = "outer";    // shadows global

  function inner() {
    let value = "inner";  // shadows outer
    console.log(value);   // "inner"
  }

  inner();
  console.log(value);     // "outer"
}

outer();
console.log(value);       // "global"
```

## Nested Scope Lookup

```
                    +-------------------------+
                    | Global Scope             |
                    | let x = 10               |
                    | function foo()           |
                    +-------------------------+
                              │
                    +-------------------------+
                    | foo Scope               |
                    | let x = 20  (shadows)    |
                    | function bar()           |
                    +-------------------------+
                              │
                    +-------------------------+
                    | bar Scope               |
                    | let x = 30  (shadows)    |
                    | console.log(x) → 30     |
                    +-------------------------+
```

## `var` vs `let` in Loops

```js
// var — one i for all iterations
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // 3, 3, 3
}

// let — new i for each iteration
for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 0); // 0, 1, 2
}
```

## Module Scope

```js
// module.js
const privateVar = "only in module";
export const publicVar = "exported";

// Global scope cannot access privateVar
// Modules have their own top-level scope
```

## Best Practices

1. Minimize global variables — use modules or IIFE
2. Prefer `const` by default, `let` when reassignment needed
3. Never use `var` in modern code
4. Be aware of shadowing — rename variables to avoid confusion
5. Use block scope `{ }` to isolate temporary variables
