# Object-Oriented Programming (OOP) in JavaScript

## Classes

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hello, I'm ${this.name}`;
  }
}

const alice = new Person("Alice", 30);
alice.greet(); // "Hello, I'm Alice"
```

## Class Fields (ES2022)

```js
class Counter {
  // Public field
  count = 0;

  // Private field (#)
  #secret = "hidden";

  // Static field
  static instances = 0;

  constructor() {
    Counter.instances++;
  }

  increment() { this.count++; }

  getSecret() { return this.#secret; }

  static getCount() { return Counter.instances; }
}

const c = new Counter();
c.count;        // 0 — public
c.#secret;      // SyntaxError: private field
Counter.instances; // 1
```

## Getters & Setters

```js
class User {
  #firstName;
  #lastName;

  constructor(first, last) {
    this.#firstName = first;
    this.#lastName = last;
  }

  get fullName() {
    return `${this.#firstName} ${this.#lastName}`;
  }

  set fullName(val) {
    [this.#firstName, this.#lastName] = val.split(" ");
  }

  get initials() {
    return `${this.#firstName[0]}.${this.#lastName[0]}.`;
  }
}

const user = new User("John", "Doe");
user.fullName; // "John Doe"
user.fullName = "Jane Smith";
user.initials; // "J.S."
```

## Static Methods & Properties

```js
class MathUtils {
  static PI = 3.14159;

  static square(x) { return x * x; }
  static cube(x) { return x * x * x; }

  static fromRadius(r) {
    return new Circle(r);
  }
}

MathUtils.square(5); // 25
MathUtils.PI;        // 3.14159
```

## Inheritance (`extends` / `super`)

```js
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // must call super before using this
    this.breed = breed;
  }

  speak() {
    return `${super.speak()} — Woof!`;
  }

  fetch() {
    return `${this.name} is fetching`;
  }
}

const dog = new Dog("Rex", "German Shepherd");
dog.speak();    // "Rex makes a sound — Woof!"
dog.fetch();    // "Rex is fetching"
dog instanceof Dog;     // true
dog instanceof Animal;  // true
```

## Inheritance Diagram

```
┌───────────────────────┐
│     Animal            │
│───────────────────────│
│ constructor(name)     │
│ speak()               │
└───────────┬───────────┘
            │ extends
┌───────────┴───────────┐
│     Dog               │
│───────────────────────│
│ constructor(name,     │
│   breed)              │
│ speak() ← overrides   │
│ fetch()               │
└───────────────────────┘
```

## Mixins (Composition)

```js
// Mixins are classes without full inheritance
const Flyable = {
  fly() { return `${this.name} is flying`; }
};

const Swimmable = {
  swim() { return `${this.name} is swimming`; }
};

class Bird {
  constructor(name) { this.name = name; }
}
class Duck {
  constructor(name) { this.name = name; }
}

// Mix in behaviors
Object.assign(Bird.prototype, Flyable);
Object.assign(Duck.prototype, Flyable, Swimmable);

const duck = new Duck("Donald");
duck.fly();  // "Donald is flying"
duck.swim(); // "Donald is swimming"
```

## Composition vs Inheritance

| Aspect | Inheritance | Composition |
|--------|------------|-------------|
| Relationship | "is-a" | "has-a" |
| Coupling | Tight (parent-child) | Loose |
| Reuse | Through extension | Through delegation |
| Flexibility | Limited (single parent) | High (mix behaviors) |
| Testing | Harder (parent dependency) | Easier (isolated) |

```js
// Prefer composition over inheritance:
const createDog = (name) => ({
  name,
  ...animalBehavior(name),
  ...dogBehavior(name)
});
```

## Private Fields vs Closure Privacy

```js
// Private fields (ES2022)
class WithPrivate {
  #secret = "hidden";
  reveal() { return this.#secret; }
}

// Closure-based privacy (older pattern)
function WithClosure(secret) {
  return {
    reveal() { return secret; }
  };
}

// Private fields are truly private (cannot be accessed externally)
// Closure-based — can be inspected with devtools
```

## Abstract Classes (Simulated)

```js
// JavaScript doesn't have native abstract classes
class AbstractShape {
  constructor() {
    if (new.target === AbstractShape) {
      throw new Error("Cannot instantiate abstract class");
    }
  }

  area() {
    throw new Error("Subclass must implement area()");
  }
}

class Circle extends AbstractShape {
  constructor(r) { super(); this.r = r; }
  area() { return Math.PI * this.r ** 2; }
}
```
