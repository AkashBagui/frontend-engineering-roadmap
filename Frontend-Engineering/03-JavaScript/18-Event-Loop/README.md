# Event Loop in JavaScript

## The Event Loop Model

```
                         ┌──────────────────────────────────────┐
                         │            Call Stack                │
                         │   (LIFO — function execution)        │
                         └────────────┬──────────┬─────────────┘
                                      │          │
                                      │  empty?  │
                                      │          │
                         ┌────────────┴──────────┴─────────────┐
                         │        Microtask Queue               │
                         │  (Promise.then/catch/finally,        │
                         │   queueMicrotask,                    │
                         │   MutationObserver)                  │
                         └────────────┬─────────────────────────┘
                                      │
                                      │  empty?
                                      │
                         ┌────────────┴─────────────────────────┐
                         │        Task Queue (Macrotask)         │
                         │  (setTimeout, setInterval,            │
                         │   setImmediate, I/O, DOM events)      │
                         └────────────┬─────────────────────────┘
                                      │
                                      │
                         ┌────────────┴─────────────────────────┐
                         │        Render Queue (Browser)         │
                         │  (requestAnimationFrame, style recalc,│
                         │   layout, paint)                     │
                         └────────────┬─────────────────────────┘
                                      │
                                      └────→ Push to Call Stack
```

## Event Loop Phases (Node.js)

```
   ┌───────────────────────────┐
┌─>│          timers           │ ← setTimeout, setInterval callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │ ← I/O callbacks deferred
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │ ← internal
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │         poll              │ ← I/O events, incoming connections
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │         check             │ ← setImmediate callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │    close callbacks        │ ← close events (socket.on('close'))
│  └─────────────┬─────────────┘
│                │
└────────────────┘
```

## Microtasks vs Macrotasks

| Feature | Microtasks | Macrotasks (Tasks) |
|---------|-----------|-------------------|
| Examples | Promise.then, queueMicrotask, MutationObserver | setTimeout, setInterval, I/O, DOM events |
| Priority | Higher — executed after each macrotask | Lower |
| Queue behavior | Emptied completely before next render | One per event loop iteration |
| Blocking rendering | Can block (microtask infinite loop) | Less likely to block |

## `setTimeout(0)` — Defer execution

```js
console.log("1");
setTimeout(() => console.log("2"), 0);
console.log("3");
// Output: 1, 3, 2

// setTimeout(fn, 0) doesn't mean "execute immediately"
// It means "add to task queue, execute after current call stack is empty"
```

## async/await in Event Loop

```js
async function foo() {
  console.log("A");
  await bar();
  console.log("C"); // continuation — behaves like .then callback
}

async function bar() {
  console.log("B");
  return Promise.resolve();
}

foo();
console.log("D");

// Output: A, B, D, C
// Why? await pauses foo, rest of foo becomes a microtask
```

## Detailed Execution Example

```js
console.log("Start"); // 1. sync

setTimeout(() => {
  console.log("Timeout"); // 5. macrotask
}, 0);

Promise.resolve()
  .then(() => console.log("Promise 1")) // 3. microtask
  .then(() => console.log("Promise 2")); // 4. microtask

queueMicrotask(() => console.log("Microtask")); // same as Promise

console.log("End"); // 2. sync

// Output:
// Start
// End
// Promise 1
// Promise 2
// Microtask
// Timeout
```

## Race Conditions with Event Loop

```js
// Problem: synchronous heavy work blocks rendering
button.addEventListener("click", () => {
  heavyComputation(); // blocks UI for 2 seconds
});

// Solution: chunk work or use Web Workers
function processInChunks(data) {
  let i = 0;
  function chunk() {
    while (i < data.length && i < 1000) {
      process(data[i]);
      i++;
    }
    if (i < data.length) {
      setTimeout(chunk, 0); // yield to UI
    }
  }
  chunk();
}
```

## Browser vs Node.js Event Loop

| Aspect | Browser | Node.js |
|--------|---------|---------|
| Render queue | Yes (requestAnimationFrame, paint) | No |
| `setImmediate` | Not available | Available (check phase) |
| `process.nextTick` | Not available | Microtask (before other callbacks) |
| I/O | Web APIs (fetch, setTimeout) | libuv (file system, network) |
| `requestAnimationFrame` | Before paint | Not available |

## Visualization

```
Time = 0ms    1ms     2ms     3ms     4ms     5ms
│             │       │       │       │       │
├─sync─┤                                                                                                              
      ├─micro─┤                                                                                                       
             ├─micro─┤                                                                                                
                    ├─macro (timer) ─┤                                                                                
                                      ├─render────────┤
```

## Common Interview Questions

```js
// Question 1: Order of execution?
console.log(1);
setTimeout(() => console.log(2), 0);
Promise.resolve().then(() => console.log(3));
console.log(4);
// Answer: 1, 4, 3, 2

// Question 2: Multiple microtasks
Promise.resolve()
  .then(() => console.log(1))
  .then(() => console.log(2));
Promise.resolve()
  .then(() => console.log(3));
// Answer: 1, 3, 2 (each .then adds to microtask queue)

// Question 3: Starving the event loop
function loop() {
  Promise.resolve().then(loop); // microtask recursion
}
loop();
// setTimeout never runs! Microtasks starve the task queue.
```
