# The `this` Keyword in JavaScript

## Binding Rules Summary

| Rule | Context | `this` Value |
|------|---------|-------------|
| Default | `fn()` (non-strict) | `window` / `globalThis` |
| Default | `fn()` (strict mode) | `undefined` |
| Implicit | `obj.fn()` | `obj` |
| Explicit | `fn.call(obj)` / `fn.apply(obj)` | `obj` |
| Hard Bind | `fn.bind(obj)` | `obj` (permanent) |
| `new` | `new Fn()` | new instance `{}` |
| Arrow | `() => {}` | Lexical (enclosing scope's `this`) |
| Event Handler | `element.onclick = fn` | `element` |
| Event Listener | `element.addEventListener('click', fn)` | `element` |

## Default Binding

```js
function showThis() {
  console.log(this);
}

showThis(); // window (or globalThis in Node)
// Strict mode: undefined
```

## Implicit Binding

```js
const user = {
  name: "Alice",
  greet() {
    console.log(`Hi, I'm ${this.name}`);
  }
};
user.greet(); // "Hi, I'm Alice"

// Watch out — losing implicit binding!
const greet = user.greet;
greet(); // "Hi, I'm undefined" — this is window/global
// because greet() is called with default binding
```

## Explicit Binding: `call`, `apply`, `bind`

```js
function introduce(greeting) {
  console.log(`${greeting}, I'm ${this.name}`);
}

const alice = { name: "Alice" };
const bob = { name: "Bob" };

introduce.call(alice, "Hello");   // "Hello, I'm Alice"
introduce.apply(bob, ["Hi"]);     // "Hi, I'm Bob"

const boundAlice = introduce.bind(alice);
boundAlice("Hey");                // "Hey, I'm Alice"
// .bind() returns a new function with permanent this
```

### call vs apply vs bind

```
call(thisArg, arg1, arg2, ...)   → invokes immediately
apply(thisArg, [args])           → invokes immediately
bind(thisArg, arg1, ...)         → returns new function
```

## Arrow Functions

```js
const user = {
  name: "Alice",
  regularFn: function() {
    console.log(this.name); // "Alice"
  },
  arrowFn: () => {
    console.log(this.name); // undefined — this is from outer scope
  },
  method() {
    const inner = () => {
      console.log(this.name); // "Alice" — lexical from method's this
    };
    inner();
  }
};

user.regularFn();
user.arrowFn();
user.method();
```

## `new` Binding

```js
function Person(name) {
  this.name = name;
  console.log(this); // new Person instance
}
const p = new Person("Alice");

// What `new` does:
// 1. Create new empty object {}
// 2. Link prototype (obj.__proto__ = Fn.prototype)
// 3. Bind this to new object
// 4. Return new object (if fn doesn't return object)
```

## Event Handlers

```js
button.addEventListener("click", function() {
  console.log(this); // button element
});

button.addEventListener("click", () => {
  console.log(this); // window (lexical from outer scope)
});

// To access element with arrow:
button.addEventListener("click", (e) => {
  console.log(e.currentTarget); // button element
});
```

## Common Pitfalls

### 1. Method reference loss
```js
class Counter {
  count = 0;
  increment() { this.count++; }
}
const c = new Counter();
setTimeout(c.increment, 100); // this is window — count stays 0

// Fixes:
setTimeout(() => c.increment(), 100);
setTimeout(c.increment.bind(c), 100);
```

### 2. Nested function
```js
const obj = {
  name: "Alice",
  greet() {
    function inner() { console.log(this.name); }
    inner(); // undefined — default binding
    // Fix: arrow function or .bind(this)
  }
};
```

### 3. Arrow as object method
```js
const obj = {
  name: "Alice",
  greet: () => console.log(this.name) // wrong — this is NOT obj
};
obj.greet(); // undefined
```

### 4. Callback `this`
```js
const obj = {
  name: "Alice",
  data: [1, 2, 3],
  process() {
    this.data.forEach(function(item) {
      console.log(this.name, item); // undefined, 1
    });
    // Fix: use arrow function
    this.data.forEach((item) => {
      console.log(this.name, item); // Alice, 1
    });
  }
};
```

## Priority Order

```
1. new binding          (highest)
2. explicit binding     (call/apply/bind)
3. implicit binding     (context object)
4. default binding      (lowest)

Arrow functions ignore all of the above — uses lexical scope.
```
