# Objects in JavaScript

## Object Literals

```js
const user = {
  name: "Alice",
  age: 30,
  greet() { return `Hi, I'm ${this.name}`; }
};
user.greet(); // "Hi, I'm Alice"
```

## Computed Properties

```js
const key = "email";
const user = {
  name: "Bob",
  [key]: "bob@example.com",       // dynamic key
  [`prefix_${key}`]: "value"      // template key
};
```

## Property Shorthand

```js
const name = "Alice";
const age = 30;
const user = { name, age }; // { name: "Alice", age: 30 }
```

## Object Destructuring

```js
const user = { name: "Alice", age: 30, city: "NYC" };

const { name, age } = user;
const { name: userName, ...rest } = user; // rename + rest
const { city = "Unknown" } = user;        // default value

// Nested destructuring
const person = { address: { street: "123 Main" } };
const { address: { street } } = person;

// Function parameter destructuring
function printUser({ name, age }) {
  console.log(`${name}, ${age}`);
}
```

## Spread Operator on Objects

```js
const defaults = { theme: "light", lang: "en" };
const userPrefs = { theme: "dark" };
const config = { ...defaults, ...userPrefs };
// { theme: "dark", lang: "en" }

// Immutable update
const updated = { ...user, age: 31 };

// Clone
const clone = { ...original };
// Shallow — nested objects still share reference
```

## Object Methods

```js
const obj = { a: 1, b: 2, c: 3 };

Object.keys(obj);       // ["a", "b", "c"]
Object.values(obj);     // [1, 2, 3]
Object.entries(obj);    // [["a", 1], ["b", 2], ["c", 3]]
Object.fromEntries([["a", 1]]); // { a: 1 }

Object.assign(target, source); // merge
const merged = Object.assign({}, defaults, userPrefs);

Object.freeze(obj);     // makes immutable (shallow)
Object.seal(obj);       // prevents adding/removing props
Object.is(a, b);        // Same-value equality (handles NaN)
```

## Optional Chaining (?.)

```js
const user = { address: { zip: "10001" } };
user?.address?.zip;         // "10001"
user?.billing?.card;        // undefined (no error!)
user?.getProfile?.();       // safe method call
users?.[0]?.name;           // safe array access
```

## Nullish Coalescing (??)

```js
// Returns RHS only if LHS is null or undefined (not falsy)
const name = input ?? "default";
// vs OR: input || "default" also catches '', 0, false

const count = 0;
count ?? 10;   // 0 (0 is not null/undefined)
count || 10;   // 10 (0 is falsy)
```

## Property Descriptors

```js
const obj = {};
Object.defineProperty(obj, "readonly", {
  value: 42,
  writable: false,  // can't change
  enumerable: false, // hidden from Object.keys
  configurable: false // can't delete or redefine
});

Object.getOwnPropertyDescriptor(obj, "readonly");
// { value: 42, writable: false, enumerable: false, configurable: false }
```

## Object Comparison

```js
const a = { x: 1 };
const b = { x: 1 };
const c = a;

a === b; // false (different references)
a === c; // true (same reference)

// Deep comparison (custom or JSON trick — limited)
JSON.stringify(a) === JSON.stringify(b); // true (but unreliable)
```

## Common Patterns

```js
// Default options
function createWidget(options = {}) {
  const defaults = { color: "red", size: 100 };
  const config = { ...defaults, ...options };
}

// Map objects as dictionaries
const colors = {
  red: "#ff0000",
  green: "#00ff00",
  blue: "#0000ff"
};
colors["red"]; // "#ff0000"
"purple" in colors; // false
```
