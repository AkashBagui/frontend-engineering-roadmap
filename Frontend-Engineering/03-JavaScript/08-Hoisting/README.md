# Hoisting in JavaScript

## What is Hoisting?

During the creation phase of the execution context, JavaScript moves variable and function *declarations* to the top of their scope. The assignment stays in place.

```
Code you write:                    What JS sees:

console.log(x);                    var x;              ← hoisted
var x = 5;                         console.log(x);     // undefined
                                   x = 5;
```

## `var` Hoisting

```js
console.log(a);   // undefined (NOT ReferenceError!)
var a = 10;
console.log(a);   // 10

// Equivalent to:
var a;
console.log(a);   // undefined
a = 10;
console.log(a);   // 10
```

## `let` and `const` Hoisting (Temporal Dead Zone)

```js
console.log(b);   // ReferenceError: Cannot access before initialization
let b = 20;
```

`let` and `const` are hoisted but **not initialized**. They enter the Temporal Dead Zone (TDZ) from the start of the block until the declaration is reached.

```
Block start
│
│   TDZ for b ← accessing b throws ReferenceError
│
│   let b = 20; ← TDZ ends here
│
│   b is now initialized
```

## Temporal Dead Zone (TDZ)

```js
{
  // TDZ starts for x, y, z
  console.log(x); // ReferenceError
  console.log(y); // ReferenceError
  console.log(z); // ReferenceError

  let x = 1;
  const y = 2;
  // z is still in TDZ
  let z = 3;
  // TDZ ends for all
}
```

## Function Hoisting

```js
// Function declarations are fully hoisted
sayHi(); // "Hi!" — works!

function sayHi() {
  console.log("Hi!");
}
```

```js
// Function expressions are NOT hoisted (just the var)
sayBye(); // TypeError: sayBye is not a function

var sayBye = function() {
  console.log("Bye!");
};
// Equivalent to:
var sayBye; // hoisted as undefined
sayBye();   // undefined is not callable
sayBye = function() { ... };
```

## Hoisting Comparison Table

| Declaration | Hoisted | Initialized | TDZ |
|-------------|---------|-------------|-----|
| `var x = 1` | Yes | `undefined` | No |
| `let x = 1` | Yes | No | Yes |
| `const x = 1` | Yes | No | Yes |
| `function f() {}` | Yes (full body) | Function | No |
| `const f = () => {}` | No (just `const`) | No | Yes |

## Detailed Examples

### 1. var hoisting in function scope
```js
function demo() {
  console.log(x); // undefined
  if (true) {
    var x = 10;
  }
  console.log(x); // 10 — var is function scoped
}
```

### 2. let in block prevents access
```js
function demo() {
  console.log(x); // ReferenceError (TDZ)
  let x = 10;
}
```

### 3. Hoisting order: functions before variables
```js
console.log(typeof foo); // "function" — hoisted first

var foo = "string";

function foo() {
  return "function";
}

console.log(typeof foo); // "string" — reassigned
```

### 4. Class hoisting
```js
const obj = new MyClass(); // ReferenceError: TDZ
class MyClass {}
```
Classes are hoisted (TDZ) — they behave like `let`/`const`.

### 5. Multiple var declarations
```js
var x = 1;
var x = 2; // Redeclaration OK (var)
let y = 1;
let y = 2; // SyntaxError: Identifier 'y' has already been declared
```

## Common Interview Questions

```js
// Question 1
var x = 1;
function test() {
  console.log(x); // undefined (local var is hoisted)
  var x = 2;
}
test();

// Question 2
function foo() {
  return bar();
  function bar() { return 1; }
  var bar = function() { return 2; }; // ignored (hoisted before)
}
foo(); // 1

// Question 3
console.log(a);   // undefined
var a = 1;
console.log(a);   // 1
(function() {
  console.log(a); // undefined (hoisted locally)
  var a = 2;
  console.log(a); // 2
})();
console.log(a);   // 1
```
