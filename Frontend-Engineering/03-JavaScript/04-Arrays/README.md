# Arrays in JavaScript

## Array Methods — Complete Reference

| Method | Returns | Mutates? | Description |
|--------|---------|----------|-------------|
| `push(v)` | new length | Yes | Add to end |
| `pop()` | removed item | Yes | Remove from end |
| `unshift(v)` | new length | Yes | Add to start |
| `shift()` | removed item | Yes | Remove from start |
| `splice(i, n, ...items)` | removed items | Yes | Add/remove at index |
| `slice(s, e)` | new array | No | Subarray copy |
| `concat(arr)` | new array | No | Merge arrays |
| `indexOf(v)` | index / -1 | No | Find first index |
| `includes(v)` | boolean | No | Check existence |
| `find(fn)` | value / undefined | No | First match |
| `findIndex(fn)` | index / -1 | No | First match index |
| `filter(fn)` | new array | No | Keep matches |
| `map(fn)` | new array | No | Transform |
| `reduce(fn, init)` | accumulated | No | Reduce to value |
| `forEach(fn)` | undefined | No | Iterate (side effects) |
| `some(fn)` | boolean | No | Any match? |
| `every(fn)` | boolean | No | All match? |
| `sort(fn)` | same array | Yes | Sort in place |
| `reverse()` | same array | Yes | Reverse in place |
| `join(sep)` | string | No | Join elements |
| `flat(depth)` | new array | No | Flatten nested |
| `flatMap(fn)` | new array | No | Map then flat(1) |
| `fill(v, s, e)` | same array | Yes | Fill with value |
| `copyWithin(t, s, e)` | same array | Yes | Copy within |
| `toReversed()` | new array | No | Immutable reverse |
| `toSorted(fn)` | new array | No | Immutable sort |
| `toSpliced(s, n, ...items)` | new array | No | Immutable splice |

## Performance Comparison (approximate for n=10,000)

| Operation | Time | Notes |
|-----------|------|-------|
| `push` | O(1) | Fastest addition |
| `pop` | O(1) | Fastest removal |
| `unshift` | O(n) | Slow — shifts all elements |
| `shift` | O(n) | Slow — shifts all elements |
| `indexOf` | O(n) | Use Set for O(1) lookup |
| `includes` | O(n) | Use Set for O(1) lookup |
| `map` | O(n) | Creates new array |
| `filter` | O(n) | Creates new array |
| `sort` | O(n log n) | TimSort algorithm |

## Real-World Data Transformations

### 1. Transform API Response
```js
const users = [
  { id: 1, name: "Alice", age: 30, active: true },
  { id: 2, name: "Bob", age: 25, active: false },
  { id: 3, name: "Charlie", age: 35, active: true }
];

// Get active user names, sorted by age
users
  .filter(u => u.active)
  .sort((a, b) => a.age - b.age)
  .map(u => u.name);
// ["Alice", "Charlie"]
```

### 2. Grouping Data
```js
const orders = [
  { product: "A", qty: 2, price: 10 },
  { product: "B", qty: 1, price: 20 },
  { product: "A", qty: 3, price: 10 }
];

const grouped = orders.reduce((acc, item) => {
  const key = item.product;
  if (!acc[key]) acc[key] = { product: key, totalQty: 0, totalRevenue: 0 };
  acc[key].totalQty += item.qty;
  acc[key].totalRevenue += item.qty * item.price;
  return acc;
}, {});
// { A: { product: "A", totalQty: 5, totalRevenue: 50 },
//   B: { product: "B", totalQty: 1, totalRevenue: 20 } }
```

### 3. Lookup Map from Array
```js
const users = [{ id: 1, name: "Alice" }, { id: 2, name: "Bob" }];
const userMap = new Map(users.map(u => [u.id, u]));
userMap.get(1); // { id: 1, name: "Alice" }
```

### 4. Chunk Array
```js
function chunk(arr, size) {
  return arr.reduce((acc, _, i) =>
    i % size === 0 ? [...acc, arr.slice(i, i + size)] : acc, []);
}
chunk([1,2,3,4,5,6], 2); // [[1,2],[3,4],[5,6]]
```

### 5. Remove Duplicates by Key
```js
const items = [{ id: 1 }, { id: 2 }, { id: 1 }];
const unique = items.filter(
  (item, i, arr) => arr.findIndex(t => t.id === item.id) === i
);
```

### 6. Flatten Nested Data
```js
const departments = [
  { name: "Eng", employees: [{ name: "A" }, { name: "B" }] },
  { name: "Sales", employees: [{ name: "C" }] }
];
const allEmployees = departments.flatMap(d => d.employees);
// [{ name: "A" }, { name: "B" }, { name: "C" }]
```

## Common Pitfalls

```js
// 1. Sorting numbers without compare function
[1, 20, 3].sort(); // [1, 20, 3] — lexicographic!
[1, 20, 3].sort((a,b) => a - b); // [1, 3, 20] ✓

// 2. forEach doesn't return
const result = [1,2,3].forEach(x => x * 2); // undefined
const result = [1,2,3].map(x => x * 2); // [2,4,6] ✓

// 3. Modifying array while iterating
const arr = [1,2,3];
arr.forEach((v, i) => { if (v === 1) arr.splice(i, 1); });
// [2, 3] — BUT index shift causes issues

// 4. Sparse arrays
const sparse = [1, , 3];
sparse.map(x => x * 2); // [2, empty, 6]
```
