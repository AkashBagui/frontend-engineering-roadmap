# Garbage Collection in JavaScript

## What is Garbage Collection?

JavaScript's engine automatically frees memory that is no longer reachable. The developer doesn't need to manually free memory (unlike C/C++).

## Mark-and-Sweep Algorithm

```
Phase 1: MARK
┌─────────┐     ┌─────────┐     ┌─────────┐
│  Root   │────→│  Obj A  │────→│  Obj B  │
│(global) │     └─────────┘     └─────────┘
└─────────┘           │
    │                 │
    │                 ▼
    │           ┌─────────┐
    └──────────→│  Obj C  │
                └─────────┘

Phase 2: SWEEP
Objects NOT reachable from roots are collected.

Final:
┌─────────┐     ┌─────────┐
│  Root   │────→│  Obj A  │
└─────────┘     └─────────┘
                     │
                     ▼
               ┌─────────┐
               │  Obj B  │
               └─────────┘
```

```js
// How it works:
// 1. Start from roots (global, call stack, locals)
// 2. Follow all references — mark every object found
// 3. Unmarked objects are unreachable → collect

let obj1 = { data: 1 };
let obj2 = { data: 2 };
obj1.ref = obj2;

// Both reachable from global (obj1 → obj2)

obj1 = null; // obj1 is now unreachable
// But obj2 is still reachable? NO — obj2 was only reachable via obj1.ref
// obj2 is also collected!
```

## Reference Counting (Legacy)

```
Count = number of references pointing to an object
When count reaches 0 → collect

Problem: Circular references
let a = {};
let b = {};
a.ref = b;
b.ref = a;
// Both have count = 1, never collected!
// This is why modern engines use mark-and-sweep
```

## V8's Orinoco (Modern V8 GC)

V8 uses a **generational** approach with multiple algorithms:

```
┌──────────────────────────────────────────┐
│              V8 Heap                      │
│                                           │
│  ┌───────────────────┐                    │
│  │  Young Generation │ ← Scavenger (minor)│
│  │  (new objects)    │   fast collection  │
│  │                   │                    │
│  │  ┌───────────┐    │     ┌──────────┐   │
│  │  │ From-space│    │────→│ To-space │   │
│  │  └───────────┘    │     └──────────┘   │
│  └─────────┬─────────┘                    │
│            │ survive                       │
│            ▼                               │
│  ┌───────────────────┐                    │
│  │  Old Generation   │ ← Mark-Sweep-Compact│
│  │  (survived 2+ GC) │   (major GC)       │
│  └───────────────────┘                     │
│                                           │
│  ┌───────────────────┐                    │
│  │  Large Object     │                    │
│  │  Space            │                    │
│  └───────────────────┘                    │
└──────────────────────────────────────────┘
```

### Generational Collection

| Generation | Size | GC Frequency | Algorithm |
|-----------|------|-------------|-----------|
| Young (Nursery) | Small (~8MB) | Frequent (~every second) | Scavenger (semi-space copy) |
| Old | Large | Infrequent | Mark-Sweep-Compact |

**How it works:**
1. New objects allocated in young generation (From-space)
2. Minor GC: copy surviving objects to To-space
3. Survive 2+ minor GC cycles → promoted to old generation
4. Major GC: mark live objects, sweep dead ones, compact if needed

## Memory Leak Detection

### Chrome DevTools
```js
// Check for detached DOM nodes
// Memory → Heap Snapshot → Search "detached"
// Detached nodes are still referenced from JS but not in DOM

// Performance monitor
// Watch JS heap size — continuous growth indicates leak
```

### Node.js
```sh
# Generate heap dump
node --heapsnapshot-signal=SIGUSR2 app.js
kill -USR2 <pid>  # triggers heap snapshot

# Use clinic.js for analysis
npx clinic heapprofiler -- node server.js
```

## WeakMap & WeakSet

Unlike regular Map/Set, WeakMap/WeakSet hold **weak references** — they don't prevent GC of the key object.

```js
let user = { name: "Alice" };

const weakMap = new WeakMap();
weakMap.set(user, "metadata");

user = null; // user object can be GC'd
// weakMap entry is automatically removed!

// vs regular Map — prevents GC
const map = new Map();
map.set(user, "metadata"); // user stays in memory!

// Use cases:
// 1. Caching without preventing GC
// 2. Private data attached to DOM elements
// 3. Object metadata

// WeakSet — same concept
const visited = new WeakSet();
visited.add(document.getElementById("page1"));
// When element removed from DOM, WeakSet entry auto-cleaned
```

## WeakRef (ES2021)

```js
// Create weak reference to an object
let user = { name: "Alice" };
const ref = new WeakRef(user);

// Get the object, undefined if collected
console.log(ref.deref()?.name); // "Alice" (or undefined if GC'd)

user = null;
// Later — ref.deref() might be undefined
// Use with caution — non-deterministic!
```

## FinalizationRegistry (ES2021)

```js
// Callback when object is GC'd
const registry = new FinalizationRegistry((heldValue) => {
  console.log(`${heldValue} was garbage collected`);
});

let user = { name: "Alice" };
registry.register(user, "User: Alice");
// When user is GC'd, callback fires with "User: Alice"

// Clean up registration
registry.unregister(user);
```

## GC Limitations & Caveats

```js
// 1. GC is non-deterministic — don't rely on timing
// 2. GC pauses execution (though modern engines minimize)
// 3. WeakRef can't be used for critical cleanup
// 4. Circular references are handled by mark-and-sweep

// Creating garbage in loops
function bad() {
  const arr = [];
  for (let i = 0; i < 10000; i++) {
    arr.push(new Array(1000)); // creates garbage
  }
  return arr;
}
// arr goes out of scope after call — GC collects
```

## GC Best Practices

1. **Nullify references** when done: `largeObj = null`
2. **Avoid retaining** unnecessary closures
3. **Use WeakMap** for caches and metadata
4. **Reuse objects** in hot paths instead of creating new ones
5. **Profile before optimizing** — premature optimization is the root of all evil
6. **Be aware of object pools** for frequently created objects
