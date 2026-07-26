# TypeScript Interview Questions

## Beginner

### Q1: What is TypeScript and why use it?

**TypeScript is a statically typed superset of JavaScript** that compiles to plain JS. Benefits include:
- Catches type-related bugs at compile time
- Better IDE support (autocomplete, refactoring)
- Self-documenting code via types
- Safer refactoring at scale
- Improved team collaboration

### Q2: Explain the difference between `interface` and `type`.

| Feature | interface | type |
|---|---|---|
| Object shape | ✅ | ✅ |
| Union types | ❌ | ✅ |
| Declaration merging | ✅ | ❌ |
| Mapped/conditional types | ❌ | ✅ |
| Performance | Slightly faster | Slightly slower |

**Rule of thumb:** Prefer `interface` for public API/object shapes, `type` for unions, intersections, and complex types.

### Q3: What is `strict: true` in tsconfig?

It enables a suite of strict type-checking options:
- `strictNullChecks` — null/undefined are not assignable to other types
- `noImplicitAny` — error when TS can't infer a type
- `strictFunctionTypes` — stronger function variance checks
- `strictBindCallApply` — properly typed bind/call/apply
- `strictPropertyInitialization` — class properties must be initialized
- `noImplicitThis` — this must have a type

### Q4: What are the differences between `any` and `unknown`?

```typescript
let a: any = 5;
a.toUpperCase(); // ✅ no error (but might crash at runtime)

let u: unknown = 5;
u.toUpperCase(); // ❌ Object is of type 'unknown'

// unknown requires type narrowing
if (typeof u === 'string') {
  u.toUpperCase(); // ✅ safe
}
```

**`any` opts out of type checking entirely. `unknown` is the type-safe counterpart that forces a type check before use.**

### Q5: What is a tuple in TypeScript?

A **tuple** is a fixed-length array where each position has a specific type:

```typescript
type Pair = [string, number];
const pair: Pair = ['age', 30];
// pair[0] is string, pair[1] is number
```

## Intermediate

### Q6: Explain generics with an example.

Generics allow writing reusable code that works with multiple types:

```typescript
function first<T>(arr: T[]): T | undefined {
  return arr[0];
}

first([1, 2, 3]);    // T = number
first(['a', 'b']);   // T = string
```

### Q7: What are discriminated unions?

A discriminated union uses a common property (the **discriminant**) to distinguish between union members:

```typescript
type Shape =
  | { kind: 'circle'; radius: number }
  | { kind: 'square'; side: number }
  | { kind: 'triangle'; base: number; height: number };

function area(s: Shape): number {
  switch (s.kind) {
    case 'circle': return Math.PI * s.radius ** 2;
    case 'square': return s.side ** 2;
    case 'triangle': return (s.base * s.height) / 2;
  }
}
```

### Q8: What is the `never` type used for?

`never` represents values that **never occur**:
- Functions that always throw
- Infinite loops
- Exhaustive type checking in switch statements

```typescript
function fail(msg: string): never {
  throw new Error(msg);
}

// Exhaustive check
function assertNever(x: never): never {
  throw new Error(`Unexpected: ${x}`);
}
```

### Q9: Explain type narrowing with examples.

TypeScript narrows types within control flow:

```typescript
function process(value: string | number | null) {
  if (value === null) return;          // narrows to string | number
  if (typeof value === 'string') {
    value.toUpperCase();               // narrows to string
  } else {
    value.toFixed(2);                  // narrows to number
  }
}
```

### Q10: What are mapped types?

Mapped types transform existing types property by property:

```typescript
type Readonly<T> = { readonly [K in keyof T]: T[K] };
type Optional<T> = { [K in keyof T]?: T[K] };

// With key remapping
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};
```

### Q11: What is the `satisfies` operator?

`Satisfies` checks a value against a type **without** widening the type:

```typescript
const palette = {
  red: '#ff0000',
  green: '#00ff00',
} satisfies Record<string, string>;

// palette.red is literal '#ff0000', not widened to string
```

## Advanced

### Q12: Explain conditional types and the `infer` keyword.

Conditional types select types based on conditions:

```typescript
type IsString<T> = T extends string ? true : false;
type A = IsString<'hello'>;  // true
type B = IsString<42>;       // false

// infer extracts types from within conditionals
type ElementType<T> = T extends (infer U)[] ? U : T;
type E1 = ElementType<number[]>;  // number
type E2 = ElementType<string>;    // string

type Unwrap<T> = T extends Promise<infer U> ? U : T;
type U1 = Unwrap<Promise<string>>; // string
```

### Q13: How does TypeScript handle module augmentation?

Module augmentation adds new types to existing modules:

```typescript
// types.d.ts
import 'express';

declare module 'express' {
  interface Request {
    user?: { id: number; name: string };
  }
}

// Usage
app.get('/', (req, res) => {
  req.user?.name; // ✅ typed
});
```

### Q14: What are assertion functions?

Functions that narrow types by throwing if a condition is false:

```typescript
function assertIsString(value: unknown): asserts value is string {
  if (typeof value !== 'string') {
    throw new Error('Not a string');
  }
}

function process(x: unknown) {
  assertIsString(x);
  x.toUpperCase(); // ✅ narrowed to string
}
```

### Q15: Explain branded types and why they're useful.

Branded types simulate **nominal typing** in TypeScript's structural type system:

```typescript
type Brand<T, B> = T & { __brand: B };
type UserId = Brand<number, 'UserId'>;
type PostId = Brand<number, 'PostId'>;

function getUser(id: UserId) {}
function getPost(id: PostId) {}

getUser(1 as UserId);  // ✅
getPost(1 as PostId);  // ✅
// getUser(1 as PostId); ❌ — prevents ID confusion
```

### Q16: What is declaration merging and how is it used?

Declaration merging is when TypeScript combines multiple declarations with the same name:

```typescript
interface Box {
  height: number;
  width: number;
}
interface Box {
  scale: number;
}
// Result: { height: number; width: number; scale: number }

// Practical: augmenting global Window
interface Window {
  myApp: { version: string };
}
```

### Q17: How does `keyof` and indexed access types work?

```typescript
interface User { name: string; age: number; email: string; }

type UserKeys = keyof User;  // 'name' | 'age' | 'email'
type NameType = User['name']; // string

// Generic version
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// Indexed access with union
type UserValueTypes = User[keyof User]; // string | number
```

### Q18: Explain template literal types (TS 4.1+).

Template literal types create string types through template expressions:

```typescript
type EventName = `on${Capitalize<string>}`;
type ClickHandler = `onClick`; // ✅
type ChangeHandler = `onChange`; // ✅

// With mapped types
type EventHandlers<T extends string> = {
  [K in T as `on${Capitalize<K>}`]: () => void;
};

type ButtonEvents = EventHandlers<'click' | 'hover'>;
// { onClick: () => void; onHover: () => void; }
```

### Q19: What is the difference between `void` and `undefined` in TypeScript?

```typescript
// void — return value is ignored
const fn1: () => void = () => 42;  // ✅ void accepts anything

// undefined — must return exactly undefined
const fn2: () => undefined = () => 42;  // ❌
const fn3: () => undefined = () => undefined;  // ✅

// void is a "return type" concept
// undefined is an actual value
```

### Q20: How do you create a type that extracts all function property keys from an object?

```typescript
type FunctionKeys<T> = {
  [K in keyof T]: T[K] extends (...args: unknown[]) => unknown ? K : never;
}[keyof T];

interface User {
  name: string;
  greet(): void;
  calculate(x: number): number;
}

type FnKeys = FunctionKeys<User>; // 'greet' | 'calculate'
```

### Q21: What is the `exactOptionalPropertyTypes` option and why use it?

When enabled, optional properties can't have `undefined` assigned to them:

```typescript
// With exactOptionalPropertyTypes: true
interface Config {
  url: string;
  timeout?: number;
}

const config: Config = { url: '/api', timeout: undefined };
// ❌ timeout?: number means it can be absent, but not explicitly undefined

const config2: Config = { url: '/api' }; // ✅
const config3: Config = { url: '/api', timeout: 5000 }; // ✅
```

### Q22: Compare `Partial<T>` with `Pick<T, K>` and `Omit<T, K>`.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
}

type PartialUser = Partial<Pick<User, 'name' | 'email'>>;
// { name?: string; email?: string }

type SafeUser = Omit<User, 'password'>;
// { id: number; name: string; email: string }

type UserUpdate = Partial<Omit<User, 'id'>>;
// { name?: string; email?: string; password?: string }
```

### Q23: How do you implement a type-safe event emitter?

```typescript
type EventMap = {
  click: { x: number; y: number };
  focus: undefined;
  keydown: { key: string; ctrlKey: boolean };
};

class TypedEmitter {
  private listeners: Map<keyof EventMap, Set<Function>> = new Map();

  on<K extends keyof EventMap>(event: K, callback: (data: EventMap[K]) => void) {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, new Set());
    }
    this.listeners.get(event)!.add(callback);
  }

  emit<K extends keyof EventMap>(event: K, data: EventMap[K]) {
    this.listeners.get(event)?.forEach(cb => cb(data));
  }
}
```

### Q24: What are variance marks (in/out) and when would you use them?

Variance marks control generic subtyping:

```typescript
// Covariant — Producer<T> is subtype of Producer<U> if T extends U
interface Producer<out T> {
  produce(): T;
}

// Contravariant — Consumer<T> is subtype of Consumer<U> if U extends T
interface Consumer<in T> {
  consume(value: T): void;
}

// Invariant (default) — neither direction works
interface Container<T> {
  get(): T;
  set(value: T): void;
}
```

### Q25: How would you type a Redux reducer?

```typescript
type Action =
  | { type: 'INCREMENT'; payload: number }
  | { type: 'DECREMENT'; payload: number }
  | { type: 'RESET' };

type State = { count: number };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'INCREMENT':
      return { count: state.count + action.payload };
    case 'DECREMENT':
      return { count: state.count - action.payload };
    case 'RESET':
      return { count: 0 };
    default:
      return state;
  }
}
```

## Quick Reference — Answer Patterns

| Topic | Key points |
|---|---|
| **`any` vs `unknown`** | `any` = no checks; `unknown` = must narrow first |
| **`interface` vs `type`** | Interface: merging, extends; Type: unions, mapped |
| **Generics** | `<T>` → reusable, type-safe code |
| **Discriminated union** | Single literal field to distinguish branches |
| **Conditional types** | `T extends U ? X : Y` |
| **Mapped types** | `[K in keyof T]: ...` |
| **Branded types** | `T & { __brand: B }` for nominal typing |
| **`satisfies`** | Check shape without widening |
