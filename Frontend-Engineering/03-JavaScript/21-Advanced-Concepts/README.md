# Advanced JavaScript Concepts

## Currying

Transforming a function so it takes arguments one at a time:

```js
// Manual currying
const add = a => b => c => a + b + c;
add(1)(2)(3); // 6

// Generic curry function
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) {
      return fn(...args);
    }
    return (...more) => curried(...args, ...more);
  };
}

const sum = curry((a, b, c) => a + b + c);
sum(1, 2, 3); // 6
sum(1)(2, 3); // 6
sum(1)(2)(3); // 6

// Real-world: configurable validation
const validate = curry((schema, data) => schema.validate(data));
const validateUser = validate(userSchema);
validateUser({ name: "Alice" });
```

## Memoization

Caching function results to avoid recomputation:

```js
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

// Fibonacci
const fib = memoize(n => {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
});

fib(100); // fast — memoized

// LRU memoize (limited cache size)
function memoizeLRU(fn, maxSize = 10) {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) {
      const val = cache.get(key);
      cache.delete(key);
      cache.set(key, val); // move to end
      return val;
    }
    const result = fn(...args);
    if (cache.size >= maxSize) {
      cache.delete(cache.keys().next().value); // evict oldest
    }
    cache.set(key, result);
    return result;
  };
}
```

## Debouncing

Ensures a function is called after a quiet period:

```js
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}

// Usage: search input
const searchInput = document.getElementById("search");
const handleSearch = debounce(async (e) => {
  const results = await fetch(`/api/search?q=${e.target.value}`);
  displayResults(results);
}, 300);
searchInput.addEventListener("input", handleSearch);

// Leading edge debounce (fires immediately, then waits)
function debounceLeading(fn, delay) {
  let timer;
  return (...args) => {
    const shouldCall = !timer;
    clearTimeout(timer);
    timer = setTimeout(() => timer = null, delay);
    if (shouldCall) fn(...args);
  };
}
```

## Throttling

Ensures a function is called at most once per interval:

```js
function throttle(fn, limit) {
  let lastCall = 0;
  return (...args) => {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      fn(...args);
    }
  };
}

// Usage: scroll handler
window.addEventListener("scroll", throttle(() => {
  checkInfiniteScroll();
}, 200));

// Throttle with trailing edge
function throttleTrailing(fn, limit) {
  let timer;
  return (...args) => {
    if (!timer) {
      timer = setTimeout(() => {
        timer = null;
        fn(...args);
      }, limit);
    }
  };
}
```

## Polyfills

Implementing modern features in older environments:

```js
// Array.prototype.map polyfill
if (!Array.prototype.map) {
  Array.prototype.map = function(callback, thisArg) {
    if (this == null) throw new TypeError("this is null");
    const result = new Array(this.length);
    for (let i = 0; i < this.length; i++) {
      if (i in this) {
        result[i] = callback.call(thisArg, this[i], i, this);
      }
    }
    return result;
  };
}

// Object.assign polyfill
if (!Object.assign) {
  Object.assign = function(target, ...sources) {
    for (const source of sources) {
      for (const key in source) {
        if (Object.prototype.hasOwnProperty.call(source, key)) {
          target[key] = source[key];
        }
      }
    }
    return target;
  };
}

// Array.flat polyfill
function flat(arr, depth = 1) {
  if (depth === 0) return arr;
  return arr.reduce((acc, val) =>
    acc.concat(Array.isArray(val) ? flat(val, depth - 1) : val), []);
}
```

## Transpilation

Converting modern JS to backwards-compatible code (Babel):

```js
// Input (ES2020)
const user = {
  name: "Alice",
  address: { city: "NYC" }
};
const city = user?.address?.city ?? "Unknown";

// Output (ES5 after Babel)
"use strict";
var _user$address;
var user = { name: "Alice", address: { city: "NYC" } };
var city = (_user$address = user.address) !== null && _user$address !== void 0
  ? _user$address.city
  : "Unknown";
```

## Web Workers

Run JavaScript in a separate thread:

```js
// main.js
const worker = new Worker("worker.js");
worker.postMessage({ limit: 1000000000 });
worker.onmessage = (e) => {
  console.log("Result:", e.data);
};
worker.onerror = (e) => console.error("Worker error:", e);

// Terminate
worker.terminate();

// worker.js
self.onmessage = (e) => {
  const { limit } = e.data;
  // Heavy computation
  let sum = 0;
  for (let i = 0; i < limit; i++) sum += i;
  self.postMessage(sum);
};
```

## Service Workers

Network proxy that runs in the background:

```js
// sw.js — register
if ("serviceWorker" in navigator) {
  navigator.serviceWorker.register("/sw.js")
    .then(reg => console.log("SW registered"));
}

// sw.js — intercept requests
self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open("v1").then(cache => {
      return cache.addAll(["/", "/styles.css", "/app.js"]);
    })
  );
});

self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then(cached => {
      return cached || fetch(event.request);
    })
  );
});
```

## Proxy

Intercept and customize operations on objects:

```js
const handler = {
  get(target, prop) {
    if (prop in target) return target[prop];
    return `Property "${prop}" does not exist`;
  },
  set(target, prop, value) {
    if (prop === "age" && (typeof value !== "number" || value < 0)) {
      throw new TypeError("Age must be a positive number");
    }
    target[prop] = value;
    return true;
  },
  deleteProperty(target, prop) {
    if (prop === "id") throw new Error("Cannot delete id");
    delete target[prop];
    return true;
  }
};

const user = new Proxy({ id: 1, name: "Alice" }, handler);
user.name; // "Alice"
user.unknown; // "Property 'unknown' does not exist"
user.age = 25; // OK
user.age = -5; // TypeError
delete user.id; // Error
```

## Reflect

Provides methods for interceptable JavaScript operations:

```js
const obj = { a: 1, b: 2 };

Reflect.get(obj, "a");        // 1
Reflect.set(obj, "c", 3);     // true
Reflect.has(obj, "a");        // true
Reflect.deleteProperty(obj, "b"); // true
Reflect.ownKeys(obj);         // ["a", "c"]
Reflect.defineProperty(obj, "d", { value: 4 });

// Used with Proxy for default behavior
const proxy = new Proxy(obj, {
  get(target, prop, receiver) {
    console.log(`Getting ${prop}`);
    return Reflect.get(target, prop, receiver);
  }
});
```

## Generators & Iterators

```js
// Generator function
function* counter() {
  let i = 0;
  while (true) {
    yield i++;
  }
}

const gen = counter();
gen.next(); // { value: 0, done: false }
gen.next(); // { value: 1, done: false }
gen.return(); // { value: undefined, done: true }

// Generator for iteration
function* range(start, end, step = 1) {
  for (let i = start; i <= end; i += step) {
    yield i;
  }
}
[...range(1, 5)]; // [1, 2, 3, 4, 5]

// Two-way communication
function* interactive() {
  const name = yield "What is your name?";
  const age = yield `Hello ${name}, how old are you?`;
  return `${name} is ${age} years old`;
}
const it = interactive();
it.next();             // { value: "What is your name?" }
it.next("Alice");      // { value: "Hello Alice, how old are you?" }
it.next(30);           // { value: "Alice is 30 years old", done: true }
```

## Symbol.iterator

Making objects iterable:

```js
const range = {
  start: 1,
  end: 5,
  [Symbol.iterator]() {
    let current = this.start;
    const end = this.end;
    return {
      next() {
        if (current <= end) {
          return { value: current++, done: false };
        }
        return { value: undefined, done: true };
      }
    };
  }
};

for (const n of range) console.log(n); // 1, 2, 3, 4, 5
[...range]; // [1, 2, 3, 4, 5]
```

## Real-World Use Cases

### 1. Rate Limiter for API Calls
```js
function rateLimit(fn, maxCalls, perMs) {
  const calls = [];
  return (...args) => {
    const now = Date.now();
    calls.push(now);
    const recent = calls.filter(t => now - t < perMs);
    calls.length = recent.length; // truncate
    if (calls.length > maxCalls) {
      throw new Error("Rate limit exceeded");
    }
    return fn(...args);
  };
}
```

### 2. Retry with Exponential Backoff
```js
async function retry(fn, maxRetries = 3) {
  for (let i = 0; i <= maxRetries; i++) {
    try {
      return await fn();
    } catch (err) {
      if (i === maxRetries) throw err;
      await new Promise(r => setTimeout(r, Math.pow(2, i) * 1000));
    }
  }
}
```

### 3. Singleton with Proxy
```js
function singleton(className) {
  let instance;
  return new Proxy(className, {
    construct(target, args) {
      if (!instance) instance = new target(...args);
      return instance;
    }
  });
}

class Database {}
const DB = singleton(Database);
new DB() === new DB(); // true
```
