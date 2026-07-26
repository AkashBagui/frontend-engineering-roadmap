# JavaScript Cheat Sheet

## Variables & Declarations

| Keyword | Scope | Hoisted | Redeclarable | Reassignable |
|---------|-------|---------|-------------|-------------|
| `var` | Function | Yes (`undefined`) | Yes | Yes |
| `let` | Block | Yes (TDZ) | No | Yes |
| `const` | Block | Yes (TDZ) | No | No |

## Data Types

```js
// Primitive (immutable)
typeof "hello"   // "string"
typeof 42        // "number"
typeof true      // "boolean"
typeof undefined // "undefined"
typeof null      // "object" (bug)
typeof Symbol()  // "symbol"
typeof 1n        // "bigint"

// Object (mutable)
typeof {}        // "object"
typeof []        // "object"
typeof function(){} // "function"
```

## Type Coercion

```js
String(123)       // "123"
Number("456")     // 456
Boolean(0)        // false
"5" - 2           // 3
"5" + 2           // "52"
+"42"             // 42
!!"hello"         // true
```

## Truthy / Falsy

**Falsy:** `false`, `0`, `""`, `null`, `undefined`, `NaN`  
**Truthy:** Everything else (including `"0"`, `[]`, `{}`)

## Operators

```js
// Arithmetic
+ - * / % ** ++ --
// Assignment
= += -= *= /= %= **= ??=
// Comparison
== != === !== > < >= <=
// Logical
&& || ??  // &&= ||= ??=
// Ternary
condition ? a : b
// Optional Chaining
obj?.prop  arr?.[i]  func?.()
// Nullish Coalescing
a ?? b  // returns b only if a is null/undefined
```

## Functions

```js
// Declaration (hoisted)
function add(a, b) { return a + b; }

// Expression (not hoisted)
const add = function(a, b) { return a + b; };

// Arrow
const add = (a, b) => a + b;
const square = x => x * x;

// Default params
function greet(name = "Guest") {}

// Rest params
function sum(...nums) {}

// IIFE
(function() { ... })();
(() => { ... })();
```

## Objects

```js
const obj = { a: 1, b: 2 };
obj.c = 3;
delete obj.a;
"b" in obj;           // true
Object.keys(obj);     // ["b","c"]
Object.values(obj);   // [2,3]
Object.entries(obj);  // [["b",2],["c",3]]
{ ...obj, d: 4 };     // spread
const { a, b } = obj; // destructuring
obj?.prop?.nested;    // optional chaining
```

## Arrays

```js
const arr = [1, 2, 3, 4, 5];
arr.length;           // 5
arr[0];               // 1
arr.push(6);          // add end   → [1,2,3,4,5,6]
arr.pop();            // remove end → 6
arr.unshift(0);       // add start
arr.shift();          // remove start
arr.slice(1, 3);      // [2,3]
arr.splice(1, 2);     // remove & return [2,3]
arr.includes(3);      // true
arr.indexOf(3);       // 2
arr.join("-");        // "1-2-3-4-5"

// Iteration
arr.forEach(x => console.log(x));
const doubled = arr.map(x => x * 2);
const evens = arr.filter(x => x % 2 === 0);
const sum = arr.reduce((a, b) => a + b, 0);
arr.find(x => x > 3);           // 4
arr.findIndex(x => x > 3);      // 3
arr.some(x => x > 3);           // true
arr.every(x => x > 0);          // true
arr.flat(); arr.flatMap(fn);
```

## Strings

```js
"hello".length;           // 5
"hello".toUpperCase();    // "HELLO"
"hello".includes("ell");  // true
"hello".slice(1, 4);      // "ell"
"hello".split("");        // ["h","e","l","l","o"]
"hello".replace("l","x"); // "hexlo"
"hello".replaceAll("l","x"); // "hexxo"
" hello ".trim();         // "hello"
`Template ${expr}`;       // template literal
```

## Promises & Async

```js
const p = new Promise((resolve, reject) => {
  resolve("done");
});
p.then(v => {}).catch(e => {}).finally(() => {});

Promise.all([p1, p2]);      // fails fast
Promise.allSettled([p1, p2]); // waits all
Promise.race([p1, p2]);     // first settles
Promise.any([p1, p2]);      // first fulfills

async function fetchData() {
  try {
    const res = await fetch(url);
    return await res.json();
  } catch (err) {
    console.error(err);
  }
}
```

## Classes

```js
class Animal {
  constructor(name) { this.name = name; }
  speak() { return `${this.name} speaks`; }
  static create(name) { return new Animal(name); }
}
class Dog extends Animal {
  constructor(name, breed) {
    super(name);
    this.breed = breed;
  }
  speak() { return `${super.speak()} loudly`; }
}
```

## ES6+ Features

```js
// Destructuring
const [a, b] = [1, 2];
const { x, y } = { x: 1, y: 2 };

// Spread
const copy = [...arr, 4, 5];
const merged = { ...obj1, ...obj2 };

// Map/Set
const map = new Map(); map.set(k, v);
const set = new Set(); set.add(1);

// Symbol
const sym = Symbol("desc");

// BigInt
const big = 9007199254740991n;

// Nullish coalescing
const val = input ?? "default";

// Optional chaining
const zip = user?.address?.zip;
```

## Error Handling

```js
try {
  throw new Error("Something went wrong");
} catch (err) {
  console.error(err.message);
} finally {
  cleanup();
}

// Custom error
class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}
```

## DOM Basics

```js
document.querySelector(".class");
document.getElementById("id");
element.textContent = "hello";
element.innerHTML = "<b>bold</b>";
element.addEventListener("click", handler);
element.classList.add("active");
element.style.color = "red";
document.createElement("div");
parent.appendChild(child);
```

## Useful Snippets

```js
// Debounce
function debounce(fn, ms) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), ms);
  };
}

// Throttle
function throttle(fn, ms) {
  let last = 0;
  return (...args) => {
    const now = Date.now();
    if (now - last >= ms) { last = now; fn(...args); }
  };
}

// Memoize
function memoize(fn) {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}

// Deep clone
const deepClone = structuredClone(obj);

// Sleep
const sleep = ms => new Promise(r => setTimeout(r, ms));
```
