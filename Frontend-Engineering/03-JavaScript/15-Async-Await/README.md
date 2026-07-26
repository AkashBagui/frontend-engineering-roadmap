# Async/Await

## Basic Syntax

```js
async function fetchData() {
  const response = await fetch("/api/data");
  const data = await response.json();
  return data;
}

// Arrow function
const fetchData = async () => {
  return await fetch("/api/data").then(r => r.json());
};
```

An `async` function always returns a Promise. `await` pauses execution until the Promise settles.

## Error Handling

```js
async function getData() {
  try {
    const res = await fetch("/api/data");
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return await res.json();
  } catch (err) {
    console.error("Failed:", err.message);
    throw err; // re-throw if caller needs to handle
  }
}

// Multiple await — single try/catch
async function loadDashboard() {
  try {
    const user = await fetchUser();
    const posts = await fetchPosts(user.id);
    const comments = await fetchComments(posts[0].id);
    return { user, posts, comments };
  } catch (err) {
    console.error("Dashboard load failed:", err);
    showErrorPage();
  }
}
```

## Comparison: Promises vs Async/Await

```js
// Promise chain
function getData() {
  return fetch("/api/user")
    .then(res => res.json())
    .then(user => fetch(`/api/posts/${user.id}`))
    .then(res => res.json())
    .catch(err => console.error(err));
}

// Async/await (same logic, cleaner)
async function getData() {
  try {
    const res = await fetch("/api/user");
    const user = await res.json();
    const postsRes = await fetch(`/api/posts/${user.id}`);
    return await postsRes.json();
  } catch (err) {
    console.error(err);
  }
}
```

## Async Patterns

### Sequential
```js
async function sequential() {
  const a = await taskA();  // waits for A
  const b = await taskB();  // then B
  const c = await taskC();  // then C
  return { a, b, c };
}
```

### Parallel
```js
async function parallel() {
  const [a, b, c] = await Promise.all([
    taskA(),
    taskB(),
    taskC()
  ]);
  return { a, b, c };
}
```

### Sequential + Parallel Mix
```js
async function mixed() {
  const user = await fetchUser();        // sequential first

  const [posts, followers, settings] = await Promise.all([
    fetchPosts(user.id),                 // parallel after
    fetchFollowers(user.id),
    fetchSettings(user.id)
  ]);

  return { user, posts, followers, settings };
}
```

### Dynamic Parallel (items array)
```js
async function processAll(items) {
  const promises = items.map(async item => {
    const result = await process(item);
    return transform(result);
  });
  return Promise.all(promises);
}
```

## Error Handling Strategies

```js
// Strategy 1: Fail fast
async function loadAll() {
  const data = await fetch("/critical"); // fail → entire operation fails
  const extra = await fetch("/optional");
  return { data, extra };
}

// Strategy 2: Partial failure with defaults
async function loadWithFallback() {
  const [main, optional] = await Promise.allSettled([
    fetch("/critical").then(r => r.json()),
    fetch("/optional").then(r => r.json())
  ]);

  return {
    main: main.status === "fulfilled" ? main.value : null,
    optional: optional.status === "fulfilled" ? optional.value : []
  };
}

// Strategy 3: Per-item error handling
async function processItems(items) {
  const results = [];
  for (const item of items) {
    try {
      results.push(await process(item));
    } catch (err) {
      results.push({ error: err, item });
    }
  }
  return results;
}
```

## Async Iteration (`for-await-of`)

```js
// Async generator
async function* fetchPages(url, totalPages) {
  for (let i = 1; i <= totalPages; i++) {
    const res = await fetch(`${url}?page=${i}`);
    yield res.json();
  }
}

// Consume with for-await-of
async function getAllPages() {
  const results = [];
  for await (const page of fetchPages("/api/items", 5)) {
    results.push(...page);
  }
  return results;
}
```

## Top-Level Await (ES2022)

```js
// In modules — works at top level without async function
const response = await fetch("/api/config");
const config = await response.json();

export default config;
// Module execution waits for the await

// Dynamic imports
const module = await import(`./lang-${locale}.js`);
```

## Comparison Table

| Aspect | Promises | Async/Await |
|--------|----------|-------------|
| Syntax | Method chaining | Looks synchronous |
| Error handling | `.catch()` | `try/catch` |
| Debugging | Hard to step through | Step over like sync code |
| Conditional logic | Complex chaining | Natural `if`/`else` |
| Loops | `Promise.all` + map | `for`/`for-await-of` |
| Composing sequential | `.then()` chain | Multiple `await` |
| Parallel | `Promise.all` | `Promise.all` + `await` |
| Return value | Promise (always) | Promise (always) |

## Common Mistakes

```js
// ❌ Forgetting await
async function load() {
  const data = fetch("/api"); // Promise, not data!
}

// ❌ Sequential when parallel is better
const a = await fetch("/a");  // waits for a
const b = await fetch("/b");  // then b
// ✓ Use: Promise.all

// ❌ Try/catch wrapping each await
async function bad() {
  try { const a = await fnA(); } catch {}
  try { const b = await fnB(); } catch {}
}
// Better: one try/catch around all, or per-item

// ❌ Returning await (unnecessary)
async fn() { return await value; }
// Just: async fn() { return value; }
// Promise automatically unwraps
```
