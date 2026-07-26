# JavaScript Interview Questions & Answers

## 1. What is the difference between `var`, `let`, and `const`?

**Answer:** `var` is function-scoped, hoisted as `undefined`, and can be redeclared. `let` and `const` are block-scoped, hoisted with temporal dead zone, and cannot be redeclared. `const` additionally cannot be reassigned (but object properties can mutate).

## 2. Explain event delegation.

**Answer:** Event delegation uses event bubbling — attach a single listener to a parent element to handle events from multiple children via `event.target`. Improves performance and handles dynamically added elements.

```js
document.querySelector("#list").addEventListener("click", (e) => {
  if (e.target.tagName === "LI") console.log(e.target.textContent);
});
```

## 3. What is the event loop?

**Answer:** The event loop continuously checks the call stack and task queues. When the call stack is empty, it pushes the first callback from the microtask queue (Promise callbacks, queueMicrotask), then the task queue (setTimeout, DOM events). This enables non-blocking async behavior in a single-threaded language.

## 4. Explain `this` binding rules.

**Answer:**
| Rule | Example |
|------|---------|
| Default | `foo()` → `window` (or `undefined` in strict mode) |
| Implicit | `obj.foo()` → `obj` |
| Explicit | `foo.call(obj)`, `foo.apply(obj)`, `foo.bind(obj)` |
| `new` | `new Foo()` → new instance |
| Arrow | Lexical `this` from enclosing scope |

## 5. How does prototypal inheritance work?

**Answer:** Every object has an internal `[[Prototype]]` link (accessed via `__proto__` or `Object.getPrototypeOf`). Property lookup traverses the prototype chain until found or `null` is reached. Constructor functions set `prototype` for instances.

```js
function Animal(name) { this.name = name; }
Animal.prototype.speak = function() { return this.name; };
const dog = new Animal("Rex"); // dog.__proto__ === Animal.prototype
```

## 6. Explain closures with a real example.

**Answer:** A closure is a function that retains access to its outer lexical scope even after the outer function has returned. Common uses: data privacy, module pattern, currying.

```js
function counter() {
  let count = 0;
  return () => ++count;
}
const c = counter();
c(); // 1, c(); // 2 // count is private
```

## 7. What is the difference between `==` and `===`?

**Answer:** `==` compares values after type coercion. `===` compares both value and type without coercion. Always prefer `===` unless explicitly needing coercion.

## 8. Explain `map`, `filter`, and `reduce`.

**Answer:**
- `map` transforms each element → new array of same length
- `filter` selects elements matching a condition → subset
- `reduce` accumulates values → single result

```js
[1,2,3].map(x => x * 2);         // [2,4,6]
[1,2,3,4].filter(x => x % 2);    // [1,3]
[1,2,3].reduce((a,b) => a+b, 0); // 6
```

## 9. What is a Promise and its states?

**Answer:** A Promise represents an eventual completion. States: `pending` → `fulfilled` (resolved) or `rejected`. `.then()` handles fulfillment, `.catch()` handles rejection, `.finally()` runs regardless.

## 10. Explain async/await vs Promises.

**Answer:** `async/await` is syntactic sugar over Promises, making async code read like synchronous code. Use `try/catch` for error handling instead of `.catch()`. `await` can only be used inside `async` functions (except top-level await in modules).

## 11. What is the Temporal Dead Zone (TDZ)?

**Answer:** The period between entering a block scope and the actual declaration of a `let`/`const` variable. Accessing the variable during TDZ throws `ReferenceError`. `var` does not have TDZ.

## 12. Explain `call`, `apply`, and `bind`.

**Answer:** All three explicitly set `this`. `call(fn, arg1, arg2)` — args individually. `apply(fn, [args])` — args as array. `bind(fn)` returns a new function with bound `this` (doesn't invoke).

## 13. What are arrow functions and how do they differ from regular functions?

**Answer:** Arrow functions have shorter syntax (`() => expr`), don't have their own `this` (lexical), cannot be used as constructors, have no `arguments` object, and cannot be used as methods if `this` needs to refer to the object.

## 14. Explain the module pattern.

**Answer:** Using closures to create private and public members (also called the revealing module pattern):

```js
const Module = (() => {
  let privateVar = 0;
  const privateFn = () => privateVar++;
  return { publicFn: () => privateFn() };
})();
```

## 15. What is memoization?

**Answer:** Caching function results based on arguments to avoid recomputation for repeated calls with the same inputs. Useful for expensive recursive computations (e.g., Fibonacci).

## 16. What is currying?

**Answer:** Transforming a function that takes multiple arguments into a sequence of functions each taking a single argument: `f(a, b, c)` → `f(a)(b)(c)`.

```js
const add = a => b => a + b;
const add5 = add(5);
add5(3); // 8
```

## 17. Explain debouncing and throttling.

**Answer:** Debouncing delays execution until after a specified quiet period. Throttling ensures execution at most once per specified interval. Debounce for search inputs, throttle for scroll handlers.

## 18. What are Symbols and BigInt?

**Answer:** `Symbol` creates unique, immutable identifiers, often used as object keys to avoid name collisions. `BigInt` represents integers beyond `Number.MAX_SAFE_INTEGER` (2^53 - 1), created with `n` suffix or `BigInt()`.

## 19. Explain Map, Set, WeakMap, WeakSet.

**Answer:**
- `Map`: key-value pairs with any type as key, maintains insertion order
- `Set`: unique values collection
- `WeakMap`: keys must be objects, held weakly (no GC prevention)
- `WeakSet`: object-only set, held weakly

## 20. What is the difference between `null` and `undefined`?

**Answer:** `undefined` means a variable has been declared but not assigned a value. `null` is an intentional assignment representing "no value". `typeof null` returns `"object"` (historical bug).

## 21. Explain shallow copy vs deep copy.

**Answer:** Shallow copy (spread `{...obj}`, `Object.assign`) copies only top-level properties; nested objects share references. Deep copy (`structuredClone()`, `JSON.parse(JSON.stringify(obj))`, or libraries) recursively copies all levels.

## 22. What is the spread operator used for?

**Answer:** Spread (`...`) expands iterables (arrays, strings, objects) into individual elements. Used for copying, merging, function arguments, and converting iterables to arrays.

## 23. Explain generator functions and `yield`.

**Answer:** Generators (`function*`) can pause execution with `yield` and resume later, maintaining state between calls. Returns an iterator with `.next()`. Useful for lazy evaluation, infinite sequences.

```js
function* idGen() { let i = 0; while (true) yield i++; }
const gen = idGen();
gen.next().value; // 0
```

## 24. What is `Proxy` used for?

**Answer:** `Proxy` creates a wrapper around an object to intercept and redefine operations (get, set, deleteProperty, etc.). Used for validation, logging, reactivity (Vue 3), and access control.

## 25. Explain error handling best practices.

**Answer:** Use `try/catch` for synchronous and async operations. Create custom error classes. Don't swallow errors (empty catch). Always clean up in `finally`. Use `Error.cause` for error chaining. Catch at boundary points.

## 26. What is tree shaking?

**Answer:** Tree shaking is dead code elimination during bundling. ES module static structure enables bundlers (Webpack, Rollup) to remove unused exports. Side-effect-free packages optimize this with `"sideEffects": false` in package.json.

## 27. Explain CommonJS vs ES Modules.

| Feature | CommonJS | ES Modules |
|---------|----------|-----------|
| Syntax | `require()` / `module.exports` | `import` / `export` |
| Loading | Synchronous | Asynchronous |
| Static analysis | Dynamic | Static (enables tree shaking) |
| Top-level `this` | `module.exports` | `undefined` |
| File extension | `.js` (default) | `.mjs` or `"type": "module"` |

## 28. What is the difference between `forEach` and `map`?

**Answer:** `forEach` iterates over elements (returns `undefined`), used for side effects. `map` creates a new array with transformed elements (returns a new array of same length). Use `map` for transformations, `forEach` for side effects.

## 29. Explain `Promise.all` vs `Promise.allSettled` vs `Promise.race` vs `Promise.any`.

**Answer:** 
- `Promise.all`: rejects immediately if any promise rejects (short-circuits)
- `Promise.allSettled`: waits for all to settle (fulfilled/rejected), returns results
- `Promise.race`: settles with the first promise that settles (resolve or reject)
- `Promise.any`: settles with the first fulfilled promise; rejects if all reject (AggregateError)

## 30. How does JavaScript handle asynchronous operations?

**Answer:** JavaScript is single-threaded with a call stack. Async operations (setTimeout, fetch, DOM events) are handled by Web APIs (browser) or libuv (Node.js). Their callbacks go to task/microtask queues. The event loop moves them to the call stack when empty.

## 31. Explain the concept of "hoisting".

**Answer:** Variable and function declarations are moved to the top of their scope during compilation. `var` is hoisted with `undefined`. `let`/`const` are hoisted but not initialized (TDZ). Function declarations are fully hoisted.

## 32. What are tagged template literals?

**Answer:** A function called with a template literal:

```js
function tag(strings, ...values) {
  return strings[0] + values.map((v, i) => v + strings[i+1]).join("");
}
tag`Hello ${name}!`; // "Hello John!"
```

## 33. Explain `Object.create` and `Object.assign`.

**Answer:** `Object.create(proto)` creates a new object with the specified prototype. `Object.assign(target, ...sources)` copies enumerable own properties to target (shallow merge).

## 34. How do you deep freeze an object?

**Answer:** Recursively call `Object.freeze()` on all nested objects. Or use libraries like Immutable.js. Plain `Object.freeze()` only freezes top-level properties.

## 35. Explain the `fetch` API.

**Answer:** `fetch(url, options)` returns a Promise resolving to a Response object. Response methods include `.json()`, `.text()`, `.blob()`. It only rejects on network error (not on HTTP errors like 404). Handle HTTP errors by checking `response.ok`.

```js
const res = await fetch("/api/data");
if (!res.ok) throw new Error(res.statusText);
const data = await res.json();
```
