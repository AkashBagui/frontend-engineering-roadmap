# Async JavaScript

## Synchronous vs Asynchronous

```
Synchronous:
    ┌─────┐   ┌─────┐   ┌─────┐
    │task1│ → │task2│ → │task3│ → done
    └─────┘   └─────┘   └─────┘
    (each task blocks until complete)

Asynchronous:
    ┌─────┐   ┌─────┐
    │task1│ → │task2│ ──────────────→ done
    └─────┘   └─────┘                   │
              │                ┌────────┘
              │                ↓
              │           ┌──────────┐
              └──────────→│async task│ (runs in background)
                          └──────────┘
```

```js
// Synchronous — blocks
console.log("Start");
for (let i = 0; i < 1000000000; i++) {} // blocks
console.log("End");

// Asynchronous — non-blocking
console.log("Start");
setTimeout(() => console.log("Async"), 0);
console.log("End");
// Output: Start, End, Async
```

## Callbacks

```js
function fetchData(url, callback) {
  setTimeout(() => {
    callback(null, { data: "result" });
  }, 1000);
}

fetchData("/api/data", (err, result) => {
  if (err) return console.error(err);
  console.log(result.data);
});
```

## Callback Hell

```js
// Nested callbacks — hard to read and maintain
getUser(id, (err, user) => {
  if (err) handleError(err);
  else {
    getPosts(user.id, (err, posts) => {
      if (err) handleError(err);
      else {
        getComments(posts[0].id, (err, comments) => {
          if (err) handleError(err);
          else {
            // ... nested deeper
          }
        });
      }
    });
  }
});

// Solutions: Promises → async/await
```

## `setTimeout` & `setInterval`

```js
// Delayed execution
const timerId = setTimeout(() => {
  console.log("Executed after 2 seconds");
}, 2000);
clearTimeout(timerId); // cancel

// Repeated execution
const intervalId = setInterval(() => {
  console.log("Every 1 second");
}, 1000);
clearInterval(intervalId); // cancel

// Recursive setTimeout (more predictable than setInterval)
function tick() {
  console.log("tick");
  setTimeout(tick, 1000); // waits for execution + delay
}
```

## Event Loop Basics

```
Event Loop Flow:
                ┌──────────────────────┐
                │     Call Stack       │ ← JS execution
                │ (functions executing)│
                └──────────┬───────────┘
                           │ empty?
                ┌──────────┴───────────┐
                │  Microtask Queue     │ ← check first (Promises)
                │ (Promise callbacks)  │
                └──────────┬───────────┘
                           │ empty?
                ┌──────────┴───────────┐
                │  Task Queue          │ ← then check (setTimeout,
                │ (DOM events, timers) │     event handlers)
                └──────────┬───────────┘
                           │
                           └──→ push to call stack
```

## Async Patterns

```js
// 1. Callbacks (old)
function loadFile(path, cb) { ... }

// 2. Promises (ES6)
function loadFile(path) {
  return new Promise((resolve, reject) => { ... });
}
loadFile("/data.json")
  .then(data => process(data))
  .catch(err => console.error(err));

// 3. Async/Await (ES2017)
async function loadAndProcess(path) {
  try {
    const data = await loadFile(path);
    return process(data);
  } catch (err) {
    console.error(err);
  }
}

// 4. Async iteration (ES2018)
async function processAll(items) {
  for await (const item of items) {
    await process(item);
  }
}
```

## Timeline Diagram

```
Time →

setTimeout(cb, 0)   → [  Task Queue  ] → Execute cb
Promise.resolve().  → [Microtask Queue] → Execute then(cb)
  then(cb)

Order of execution:
1. Synchronous code
2. Microtasks (Promise callbacks, queueMicrotask)
3. Task queue (setTimeout, setInterval, DOM events)
4. Render (browser repaints)

Example:
console.log(1);
setTimeout(() => console.log(2), 0);
Promise.resolve().then(() => console.log(3));
console.log(4);
// Output: 1, 4, 3, 2
```

## Real-World Async Example

```js
// Loading user data with loading state
async function loadUserData(userId) {
  showLoader(true);

  try {
    const [user, posts, settings] = await Promise.all([
      fetch(`/api/users/${userId}`).then(r => r.json()),
      fetch(`/api/users/${userId}/posts`).then(r => r.json()),
      fetch(`/api/users/${userId}/settings`).then(r => r.json())
    ]);

    return { user, posts, settings };
  } catch (err) {
    showError(err.message);
    throw err;
  } finally {
    showLoader(false);
  }
}
```

## Common Mistakes

```js
// 1. Forgetting await
const data = fetch("/api"); // Promise, not response!

// 2. Sequential when parallel is possible
const a = await fetch("/a");
const b = await fetch("/b"); // waits for a to finish
// Use: const [a, b] = await Promise.all([fetch("/a"), fetch("/b")])

// 3. Not handling promise rejections
fetch("/api").then(r => r.json()); // unhandled rejection!

// 4. Mixing sync and async in loops
async function process(items) {
  items.forEach(async item => {
    await processItem(item); // fires all at once!
  });
}
// Use for...of with await
```
