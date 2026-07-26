# Memory Management in JavaScript

## Stack vs Heap

```
┌─────────────────────────────────────────────────┐
│  Stack (Primitives, references)                  │
│  ┌──────────────────────┐                        │
│  │  frame: main()       │                        │
│  │    a: 42             │  ← primitive value     │
│  │    user: ref─────┐   │  ← reference to heap   │
│  ├──────────────────────┤                        │
│  │  frame: foo()        │                        │
│  │    x: "hello"        │                        │
│  └──────────────────────┘                        │
├─────────────────────────────────────────────────┤
│  Heap (Objects, functions, closures)             │
│  ┌──────────────────────────────┐                │
│  │  user object:                │                │
│  │  ┌────────────────┐         │                │
│  │  │ name: "Alice"  │         │                │
│  │  │ age: 30        │         │                │
│  │  │ address: ref──→│──┐      │                │
│  │  └────────────────┘  │      │                │
│  │                       │      │                │
│  │  ┌───────────────────┘      │                │
│  │  ↓                          │                │
│  │  address object:            │                │
│  │  ┌────────────────┐         │                │
│  │  │ city: "NYC"    │         │                │
│  │  └────────────────┘         │                │
│  └──────────────────────────────┘                │
└─────────────────────────────────────────────────┘
```

```js
// Stack — primitives (stored directly)
let a = 42;        // value in stack
let b = a;         // copy of value
b = 10;            // a is still 42

// Heap — objects (reference stored on stack, object data on heap)
const obj1 = { x: 1 }; // reference in stack → heap
const obj2 = obj1;     // copy of reference
obj2.x = 2;            // obj1.x is also 2 (same heap object)
```

## Reference Types

```js
// Primitive types (stack)
// number, string, boolean, undefined, null, symbol, bigint

// Reference types (heap)
// Object, Array, Function, Date, Map, Set, etc.

// Comparison
42 === 42;              // true — value comparison
{ a: 1 } === { a: 1 };  // false — reference comparison

// String is primitive but behaves like object
const s = "hello";
s.length; // 5 — temporarily wrapped in String object
```

## Reachability

JavaScript's memory management is based on **reachability**. Values are reachable if:
- They're in the root (global variables, call stack, current function locals)
- They're reachable from a root via references

```js
// Reachable from root (global)
let user = { name: "Alice" }; // reachable

user = null; // object is no longer reachable → eligible for GC

// Islands of reachability
function demo() {
  const a = { data: 1 };
  const b = { data: 2, ref: a }; // a is reachable from b
  return b;
}
const result = demo();
// After demo() returns:
// result → b → a (both reachable via result)
```

## Memory Life Cycle

```
1. Allocate          2. Use             3. Release
   │                    │                  │
   ▼                    ▼                  ▼
let obj = {}       obj.name = "A"      obj = null
new Object()       obj.say()           (becomes unreachable)
                  read/write           GC collects it
```

## Common Memory Leaks

### 1. Global Variables
```js
// ❌ Accidental globals
function leak() {
  leaked = "I'm global now"; // no declaration — becomes window property
}
// In strict mode: ReferenceError

// ✓ Use let/const always
```

### 2. Forgotten Closures
```js
// ❌ Closure retains large data
function createLeak() {
  const largeData = new Array(1000000).fill("x");
  return function log() {
    console.log("Hello"); // doesn't even use largeData!
    // But closure keeps reference to entire outer scope
  };
}
const leaky = createLeak(); // largeData cannot be GC'd

// ✓ Nullify unused variables
function createFixed() {
  const largeData = new Array(1000000).fill("x");
  const result = function log() { console.log("Hello"); };
  largeData = null; // release explicitly
  return result;
}
```

### 3. Forgotten Event Listeners
```js
// ❌ Listener prevents GC of element
const button = document.getElementById("btn");
button.addEventListener("click", handler);
button.remove(); // button removed from DOM
// But handler still references button (closure) → not GC'd

// ✓ Always remove listeners
button.removeEventListener("click", handler);

// ✓ or use once option
button.addEventListener("click", handler, { once: true });
```

### 4. Detached DOM References
```js
// ❌ Retaining DOM references
const elements = [];
document.querySelectorAll(".item").forEach(el => {
  elements.push(el);
});
// Even after removing from DOM, elements array keeps references

// ✓ Clear references when done
elements.length = 0;
```

### 5. Timer Callbacks
```js
// ❌ setInterval never cleared
setInterval(() => {
  // references external data
  checkData();
}, 1000);

// ✓ Clear when no longer needed
const timer = setInterval(() => checkData(), 1000);
clearInterval(timer);
```

## Detecting Leaks

### Chrome DevTools
1. **Performance tab**: Record memory allocation over time
2. **Memory tab**: Take heap snapshots and compare
3. **Timeline**: Watch for sawtooth patterns (GC) or flat growth (leak)

### Node.js
```sh
node --inspect app.js  # open chrome://inspect
```

```js
// programmatic
const used = process.memoryUsage();
console.log(`Heap: ${Math.round(used.heapUsed / 1024 / 1024)} MB`);
```

## Best Practices

1. Use `const`/`let` — avoid accidental globals
2. Clean up event listeners, timers, observers
3. Nullify references when done with large data
4. Avoid closures referencing unnecessary data
5. Use `WeakMap`/`WeakSet` for caches to allow GC
6. Profile with DevTools during development
7. Detach DOM references after removing elements
