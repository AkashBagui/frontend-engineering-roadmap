# TypeScript Interview Questions

## 1. What are the differences between `interface` and `type`?

**Answer:**

```typescript
// Interface - declaration merging, extends, implements
interface User {
  name: string;
  age: number;
}

// Declaration merging (interfaces with same name merge)
interface User {
  email: string;
}
// User now has: name, age, email

// Type alias - unions, intersections, primitives, mapped
type Status = 'active' | 'inactive' | 'pending';
type Point = { x: number; y: number };
type ID = string | number;

// Key differences:
// 1. Declaration merging: interface YES, type NO
// 2. Extends: interface extends, type uses intersections (&)
// 3. Computed properties: type supports, interface doesn't
// 4. Mapped types: type supports, interface doesn't

// Interface extending
interface Animal { name: string }
interface Dog extends Animal { breed: string }

// Type intersection
type Animal = { name: string };
type Dog = Animal & { breed: string };

// Best practice: Use interface for object shapes (public API), type for everything else
```

## 2. How do generics work in TypeScript?

**Answer:**

```typescript
// Basic generic function
function identity<T>(arg: T): T {
  return arg;
}

const result = identity<string>('hello'); // string
const inferred = identity(42); // number (type inference)

// Generic constraints
function getLength<T extends { length: number }>(arg: T): number {
  return arg.length;
}

getLength('hello'); // 5
getLength([1, 2, 3]); // 3
// getLength(42); // Error: number doesn't have length

// Generic interface
interface Repository<T> {
  getById(id: string): T;
  getAll(): T[];
  create(item: T): T;
  update(id: string, item: Partial<T>): T;
  delete(id: string): void;
}

// Generic class
class Stack<T> {
  private items: T[] = [];
  push(item: T): void { this.items.push(item); }
  pop(): T | undefined { return this.items.pop(); }
  peek(): T | undefined { return this.items[this.items.length - 1]; }
}

// Multiple type parameters
function pair<A, B>(a: A, b: B): [A, B] {
  return [a, b];
}

// Generic keyof constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}
```

## 3. Explain utility types.

**Answer:**

```typescript
interface User {
  name: string;
  age: number;
  email: string;
  role: 'admin' | 'user';
}

// Partial<T> - all properties optional
const partial: Partial<User> = { name: 'Alice' };

// Required<T> - all properties required
const required: Required<Partial<User>> = { name: 'Alice', age: 30, email: 'a@b.com', role: 'admin' };

// Readonly<T> - all properties readonly
const readonly: Readonly<User> = { name: 'Alice', age: 30, email: 'a@b.com', role: 'admin' };
// readonly.name = 'Bob'; // Error

// Pick<T, K> - select specific keys
const nameAndEmail: Pick<User, 'name' | 'email'> = { name: 'Alice', email: 'a@b.com' };

// Omit<T, K> - remove specific keys
const withoutEmail: Omit<User, 'email'> = { name: 'Alice', age: 30, role: 'admin' };

// Record<K, T> - object type with keys K and values T
const userMap: Record<string, User> = {
  'user-1': { name: 'Alice', age: 30, email: 'a@b.com', role: 'admin' }
};

// Exclude<T, U> - exclude members of U from T
type Status = Exclude<'active' | 'inactive' | 'pending', 'pending'>; // 'active' | 'inactive'

// Extract<T, U> - extract members of U from T
type ActiveStatus = Extract<'active' | 'inactive', 'active'>; // 'active'

// NonNullable<T> - remove null and undefined from T
type NonNull = NonNullable<string | null | undefined>; // string

// ReturnType<T> - get return type of function type
function greet() { return 'hello'; }
type GreetReturn = ReturnType<typeof greet>; // string

// Parameters<T> - get parameter types of function
type GreetParams = Parameters<typeof greet>; // []
```

## 4. What are advanced types in TypeScript?

**Answer:**

```typescript
// Union types
type Status = 'active' | 'inactive' | 'pending';
type ID = string | number;

// Intersection types
type A = { a: number };
type B = { b: string };
type C = A & B; // { a: number; b: string }

// Discriminated unions (tagged unions)
type Shape =
  | { kind: 'circle'; radius: number }
  | { kind: 'rectangle'; width: number; height: number }
  | { kind: 'triangle'; base: number; height: number };

function area(shape: Shape): number {
  switch (shape.kind) {
    case 'circle':
      return Math.PI * shape.radius ** 2;
    case 'rectangle':
      return shape.width * shape.height;
    case 'triangle':
      return (shape.base * shape.height) / 2;
  }
}

// Template literal types
type EventName = 'click' | 'hover' | 'scroll';
type EventHandler = `on${Capitalize<EventName>}`; // 'onClick' | 'onHover' | 'onScroll'

// Index types / index signatures
interface Dictionary<T> {
  [key: string]: T;
}

// Mapped types - transform properties
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

type Optional<T> = {
  [P in keyof T]?: T[P];
};
```

## 5. Explain type guards.

**Answer:**

```typescript
// typeof type guard
function process(value: string | number) {
  if (typeof value === 'string') {
    return value.toUpperCase(); // TypeScript knows value is string
  }
  return value.toFixed(2); // TypeScript knows value is number
}

// instanceof type guard
class Person { constructor(public name: string) {} }
class Company { constructor(public name: string, public employees: number) {} }

function print(entity: Person | Company) {
  if (entity instanceof Person) {
    console.log(person.name); // Person
  } else {
    console.log(company.name, company.employees); // Company
  }
}

// Custom type guard (value is Type)
interface Cat { meow(): void }
interface Dog { bark(): void }

function isCat(pet: Cat | Dog): pet is Cat {
  return (pet as Cat).meow !== undefined;
}

function handlePet(pet: Cat | Dog) {
  if (isCat(pet)) {
    pet.meow(); // TypeScript knows it's Cat
  } else {
    pet.bark();
  }
}

// in operator type guard
function move(animal: Fish | Bird) {
  if ('swim' in animal) {
    return animal.swim();
  }
  return animal.fly();
}

// Discriminated union type guard (most common)
type Response = 
  | { status: 'success'; data: unknown }
  | { status: 'error'; error: string };

function handleResponse(response: Response) {
  if (response.status === 'success') {
    console.log(response.data); // Known type
  } else {
    console.error(response.error);
  }
}
```

## 6. How do declaration files (.d.ts) work?

**Answer:**

```typescript
// Declaration files provide types for JavaScript code

// Global declaration
// globals.d.ts
declare const VERSION: string;
declare function log(message: string): void;

// Module declaration
// lodash.d.ts
declare module 'lodash' {
  export function cloneDeep<T>(value: T): T;
  export function debounce<T extends (...args: any[]) => any>(
    func: T, wait: number
  ): T;
}

// Ambient namespace declarations
declare namespace MyLib {
  function init(): void;
  const version: string;
}

// Augmenting existing modules
// augment.d.ts
import 'some-module';
declare module 'some-module' {
  export interface SomeType {
    newProperty: number;
  }
}

// Class type declarations
declare class MyClass {
  constructor(name: string);
  getName(): string;
}

// Extending window
declare global {
  interface Window {
    myGlobalConfig: { apiKey: string };
  }
}
// Now window.myGlobalConfig is typed

// Ambient module with wildcard
declare module '*.svg' {
  const content: string;
  export default content;
}

declare module '*.module.css' {
  const classes: { readonly [key: string]: string };
  export default classes;
}
```

## 7. What is the `infer` keyword?

**Answer:**

```typescript
// infer extracts type information from conditional types

// Extract return type
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

type Fn = (x: number) => string;
type Result = ReturnType<Fn>; // string

// Extract array element type
type ElementType<T> = T extends (infer U)[] ? U : never;
type Items = ElementType<string[]>; // string

// Extract promise value
type PromiseValue<T> = T extends Promise<infer V> ? V : never;
type Value = PromiseValue<Promise<number>>; // number

// Extract function parameters
type FirstParameter<T> = T extends (first: infer F, ...args: any[]) => any ? F : never;
type Param = FirstParameter<(x: number, y: string) => void>; // number

// Extract constructor parameters
type ConstructorParams<T> = T extends new (...args: infer P) => any ? P : never;
type Params = ConstructorParams<typeof Date>; // [value?: string | number | Date]

// Deep infer with recursion
type DeepPromiseValue<T> = T extends Promise<infer V> 
  ? DeepPromiseValue<V> 
  : T;
type Deep = DeepPromiseValue<Promise<Promise<string>>>; // string

// Practical: Extract from complex types
type UserAPI = {
  getUser: () => Promise<{ id: number; name: string }>;
  updateUser: (id: number, data: Partial<{ name: string }>) => Promise<void>;
};

type GetUserReturn = ReturnType<UserAPI['getUser']>; // Promise<{id: number; name: string}>
```

## 8. Explain conditional types.

**Answer:**

```typescript
// Basic conditional type
type IsString<T> = T extends string ? 'yes' : 'no';

type A = IsString<string>; // 'yes'
type B = IsString<number>; // 'no'

// Nested conditionals
type TypeName<T> =
  T extends string ? 'string' :
  T extends number ? 'number' :
  T extends boolean ? 'boolean' :
  T extends undefined ? 'undefined' :
  T extends null ? 'null' :
  T extends Function ? 'function' :
  'object';

type Name = TypeName<number>; // 'number'

// Distributive conditional types
// When T is a union, conditional distributes over each member
type ToArray<T> = T extends any ? T[] : never;
type StrNumArr = ToArray<string | number>; // string[] | number[] (NOT (string | number)[])

// Prevent distribution with tuple wrapper
type ToArrayNonDist<T> = [T] extends [any] ? T[] : never;
type Arr = ToArrayNonDist<string | number>; // (string | number)[]

// Filter types from union
type Filter<T, U> = T extends U ? never : T;
type WithoutStrings = Filter<string | number | boolean, string>; // number | boolean

// Extract types matching condition
type ExtractString<T> = T extends string ? T : never;
type StringsOnly = ExtractString<'a' | 1 | 'b' | true>; // 'a' | 'b'

// Practical: Function with conditional return
type AsyncReturn<T> = T extends Promise<infer U> ? U : T;

function wrapPromise<T>(value: T): Promise<AsyncReturn<T>> {
  return Promise.resolve(value as any);
}
```

## 9. How do mapped types work?

**Answer:**

```typescript
// Basic mapped type - transforms each property
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};

// Optional properties
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// Nullable properties
type Nullable<T> = {
  [P in keyof T]: T[P] | null;
};

// Mapping with value transformation
type Stringify<T> = {
  [P in keyof T]: string;
};

// Mapping with key remapping (TS 4.1+)
type Getters<T> = {
  [P in keyof T as `get${Capitalize<string & P>}`]: () => T[P];
};

interface Person { name: string; age: number; }
type PersonGetters = Getters<Person>;
// { getName: () => string; getAge: () => number; }

// Filter keys by type
type KeysOfType<T, U> = {
  [P in keyof T]: T[P] extends U ? P : never;
}[keyof T];

type StringKeys = KeysOfType<{ name: string; age: number; email: string }, string>;
// 'name' | 'email'

// Pick by value type
type PickByType<T, U> = {
  [P in keyof T as T[P] extends U ? P : never]: T[P];
};

type StringsOnly = PickByType<{ name: string; age: number; email: string }, string>;
// { name: string; email: string; }

// Homomorphic mapped types (preserve modifiers)
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

// Practical: Form state type
type FormState<T> = {
  [P in keyof T]: { value: T[P]; error?: string; touched: boolean };
};
```

## 10. What's the difference between `any`, `unknown`, `never`, and `void`?

**Answer:**

```typescript
// any - opt-out of type checking (avoid when possible)
let anything: any = 42;
anything = 'string';
anything.toUpperCase(); // No error at compile time (may fail at runtime)
anything.nonexistent(); // No error

// unknown - type-safe any (must narrow before use)
let unknown: unknown = 42;
unknown = 'string';
// unknown.toUpperCase(); // Error: Object is of type 'unknown'

// Must narrow:
if (typeof unknown === 'string') {
  unknown.toUpperCase(); // OK
}

// never - represents values that never occur
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {}
}

// Exhaustive check with never
type Shape = 'circle' | 'square';
function getArea(shape: Shape): number {
  switch (shape) {
    case 'circle': return 1;
    case 'square': return 2;
    default:
      const exhaustive: never = shape; // Error if new shape added
      return exhaustive;
  }
}

// void - function returns no meaningful value
function log(message: string): void {
  console.log(message);
  // Can return undefined
}
```

| Type | Can be assigned to anything? | Can anything be assigned to it? | Type checking |
|------|------------------------------|--------------------------------|---------------|
| any | Yes | Yes | None |
| unknown | No | Yes | Must narrow |
| never | Yes | No | Bottom type |
| void | No | undefined/null | Safe |

## 11. Explain `keyof` and `typeof` operators.

**Answer:**

```typescript
// keyof - gets union of property keys
interface User {
  name: string;
  age: number;
  email: string;
}

type UserKeys = keyof User; // 'name' | 'age' | 'email'

// Practical use
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user: User = { name: 'Alice', age: 30, email: 'a@b.com' };
getProperty(user, 'name'); // string
// getProperty(user, 'address'); // Error

// typeof - gets type from value
const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
  retries: 3
};

type ConfigType = typeof config;
// { apiUrl: string; timeout: number; retries: number; }

// Combined: keyof typeof
type ConfigKeys = keyof typeof config; // 'apiUrl' | 'timeout' | 'retries'

// Practical: Enum-like pattern
const Colors = {
  Red: '#ff0000',
  Green: '#00ff00',
  Blue: '#0000ff'
} as const;

type Color = keyof typeof Colors; // 'Red' | 'Green' | 'Blue'
type HexColor = typeof Colors[Color]; // '#ff0000' | '#00ff00' | '#0000ff'

// typeof with class
class Point {
  constructor(public x: number, public y: number) {}
}

type PointType = typeof Point; // typeof Point (constructor type)
type InstanceType = Point; // Point (instance type)
```

## 12. How does `as const` work?

**Answer:**

```typescript
// Without as const - widened types
const colors = {
  red: '#FF0000',
  green: '#00FF00'
};
// typeof colors: { red: string; green: string; }

// With as const - literal types (readonly)
const colorsConst = {
  red: '#FF0000',
  green: '#00FF00'
} as const;
// typeof colorsConst: { readonly red: "#FF0000"; readonly green: "#00FF00"; }

// Array with as const
const roles = ['admin', 'user', 'guest'] as const;
// typeof roles: readonly ["admin", "user", "guest"]
type Role = typeof roles[number]; // 'admin' | 'user' | 'guest'

// Nested objects
const defaultConfig = {
  theme: {
    mode: 'dark' as const,
    colors: ['#000', '#fff'] as const
  },
  features: {
    analytics: true,
    chat: false
  }
} as const;

// As const makes all deeply readonly with literal types

// Practical: Enum alternative
export const Routes = {
  HOME: '/',
  ABOUT: '/about',
  CONTACT: '/contact'
} as const;

export type Route = typeof Routes[keyof typeof Routes]; // '/' | '/about' | '/contact'

// Usage
function navigate(route: Route) {}
navigate('/about'); // OK
// navigate('/other'); // Error
```

## 13. Explain class access modifiers.

**Answer:**

```typescript
class Animal {
  // public (default) - accessible everywhere
  public name: string;

  // private - only accessible within this class
  private age: number;

  // protected - accessible within class and subclasses
  protected species: string;

  // readonly - can only be assigned in constructor
  readonly id: string;

  constructor(name: string, age: number, species: string) {
    this.name = name;
    this.age = age;
    this.species = species;
    this.id = Math.random().toString();
  }

  public introduce(): string {
    return `I am ${this.name}, a ${this.species}`;
  }

  private calculateAge(): number {
    return this.age * 7; // Private method
  }

  protected getAge(): string {
    return `${this.name} is ${this.age} years old`;
  }
}

class Dog extends Animal {
  constructor(name: string, age: number) {
    super(name, age, 'Canine');
    this.species; // OK (protected)
    // this.age // Error (private)
    this.getAge(); // OK (protected)
  }
}

const dog = new Dog('Rex', 3);
dog.name; // OK (public)
// dog.age // Error (private)
// dog.species // Error (protected)
// dog.getAge() // Error (protected)

// Parameter property shorthand
class Person {
  constructor(
    public name: string,       // Creates and assigns this.name
    private age: number,       // Creates and assigns this.age (private)
    protected email?: string   // Creates optional protected property
  ) {}
}
```

## 14. What are decorators in TypeScript?

**Answer:**

```typescript
// Decorators are functions that modify classes, methods, properties

// Class decorator
function Logger(constructor: Function) {
  console.log(`Class ${constructor.name} created`);
}

@Logger
class User {
  constructor(public name: string) {}
}

// Method decorator
function LogMethod(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  descriptor.value = function(...args: any[]) {
    console.log(`Calling ${key} with`, args);
    return original.apply(this, args);
  };
  return descriptor;
}

class Calculator {
  @LogMethod
  add(a: number, b: number): number {
    return a + b;
  }
}

// Property decorator
function DefaultValue(value: string) {
  return function(target: any, key: string) {
    Object.defineProperty(target, key, {
      value,
      writable: true
    });
  };
}

class Config {
  @DefaultValue('localhost')
  host: string;
}

// Accessor decorator (getter/setter)
function Configurable(value: boolean) {
  return function(target: any, key: string, descriptor: PropertyDescriptor) {
    descriptor.configurable = value;
  };
}

// Parameter decorator (mainly for DI)
function Inject(target: any, key: string, index: number) {
  console.log(`Parameter at index ${index} in method ${key}`);
}
```

## 15. Explain the `satisfies` operator.

**Answer:**

```typescript
// satisfies checks type compatibility without widening the type

// Without satisfies (widens to base type)
const palette = {
  red: [255, 0, 0],
  green: '#00ff00',
  blue: [0, 0, 255]
};
// Type: { red: number[]; green: string; blue: number[]; }

// With satisfies (preserves literal types)
const palette2 = {
  red: [255, 0, 0],
  green: '#00ff00',
  blue: [0, 0, 255]
} satisfies Record<string, string | number[]>;

// Type: { red: [number, number, number]; green: string; blue: [number, number, number]; }

palette2.red.map(x => x); // OK - knows it's an array
// palette2.nonExistent // Error - satisfies checks for extra properties

// Another example
type Color = string | { r: number; g: number; b: number };

const color1 = 'red' satisfies Color; // Type: 'red' (not string)
const color2 = { r: 255, g: 0, b: 0 } satisfies Color; // Type: { r: number; g: number; b: number }

// Practical: API responses
type ApiResponse = { success: boolean; data: unknown };

const response = {
  success: true,
  data: { id: 1, name: 'Alice' }
} satisfies ApiResponse;

// response.data.name // Error: data is unknown
// Need to cast or use type guard

// const with satisfies for type narrowing
type Dog = { type: 'dog'; bark: () => void };
type Cat = { type: 'cat'; meow: () => void };
type Pet = Dog | Cat;

const pet = {
  type: 'dog',
  bark: () => console.log('woof')
} satisfies Pet; // Type still inferred as Dog
```

## 16. How does module augmentation work?

**Answer:**

```typescript
// Adding types to existing modules

// Augmenting express
// express-augment.d.ts
import 'express';

declare module 'express' {
  interface Request {
    user?: {
      id: string;
      name: string;
      role: 'admin' | 'user';
    };
  }
}

// Now usage in project:
// app.get('/profile', (req, res) => {
//   req.user.name; // Works! Type-safe
// });

// Augmenting a library
import 'some-ui-library';

declare module 'some-ui-library' {
  interface ButtonProps {
    // Adding a new prop to an existing component
    loading?: boolean;
  }
}

// Global augmentation
declare global {
  interface String {
    capitalize(): string;
  }
}

// Implementation in .ts file
String.prototype.capitalize = function() {
  return this.charAt(0).toUpperCase() + this.slice(1);
};

// Augmenting enums
declare enum Direction {
  Up = 'UP',
  Down = 'DOWN'
}

// Merger augmentation (for interfaces)
interface ComponentBase {
  id: string;
}

declare module './Component' {
  interface ComponentBase {
    className?: string;
  }
}
```

## 17. What is type erasure in TypeScript?

**Answer:**

```typescript
// TypeScript types are removed at compile time - no runtime impact

// Type annotations are erased
function add(a: number, b: number): number {
  return a + b;
}
// Compiles to:
// function add(a, b) {
//   return a + b;
// }

// Interfaces are completely removed
interface User {
  name: string;
  age: number;
}
// Compiles to nothing

// Enums have runtime representation
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}
// Compiles to:
// var Direction;
// (function (Direction) {
//   Direction[Direction["Up"] = 0] = "Up";
//   // ...
// })(Direction || (Direction = {}));

// Const enums are inlined (no runtime code)
const enum Color {
  Red, Green, Blue
}
let c = Color.Red;
// Compiles to:
// let c = 0;

// Type assertions are erased
const value = someValue as string;
// Compiles to:
// const value = someValue;

// Type-only imports are erased with isolatedModules
import type { User } from './types';
// import statement is removed if all imports are type-only

// Decorators are a notable exception - they have runtime behavior
// when experimentalDecorators is enabled
```

## 18. Explain `Pick`, `Omit`, `Extract`, `Exclude` differences.

**Answer:**

```typescript
interface User {
  name: string;
  age: number;
  email: string;
  password: string;
  role: 'admin' | 'user' | 'guest';
}

// Pick - select specific keys from a type
type PublicProfile = Pick<User, 'name' | 'email'>;
// { name: string; email: string; }

// Omit - remove specific keys from a type
type UserWithoutPassword = Omit<User, 'password'>;
// { name: string; age: number; email: string; role: 'admin' | 'user' | 'guest'; }

// Omit multiple keys
type UserCredentials = Omit<User, 'name' | 'email' | 'role'>;
// { age: number; password: string; }

// Exclude - exclude members from a union
type Roles = 'admin' | 'user' | 'guest';
type AdminOnly = Exclude<Roles, 'user' | 'guest'>; // 'admin'

// Extract - extract matching members from a union
type NumbersOrStrings = string | number | boolean;
type StrAndNum = Extract<NumbersOrStrings, string | number>; // string | number

// Practical differences:
// Pick/Omit work on object types (interfaces)
// Exclude/Extract work on union types

// Pick with union of keys
type UserMeta = Pick<User, 'name' | 'role'>;
// { name: string; role: 'admin' | 'user' | 'guest'; }

// Omit with expression
type Updatable = Omit<User, 'role'>;
// Everything except role

// Chaining
type SafeUser = Omit<Pick<User, 'name' | 'email' | 'age'>, 'email'>;
// { name: string; age: number; }
```

## 19. What are branded types?

**Answer:**

```typescript
// Branded types create nominal typing (distinct types from same structure)

// Basic brand
type Brand<T, B> = T & { __brand: B };

type UserId = Brand<string, 'UserId'>;
type ProductId = Brand<string, 'ProductId'>;

function getUser(id: UserId): void {}
function getProduct(id: ProductId): void {}

const userId = 'abc' as UserId;
const productId = 'xyz' as ProductId;

getUser(userId); // OK
// getUser(productId); // Error - Type 'ProductId' not assignable to 'UserId'
// getUser('abc'); // Error - string not assignable to UserId

// With classes
class UserIdClass {
  private __brand: void;
  constructor(public value: string) {}
}

class ProductIdClass {
  private __brand: void;
  constructor(public value: string) {}
}

function processUser(id: UserIdClass) {}
function processProduct(id: ProductIdClass) {}

// With opaque types
type Opaque<T, K extends string> = T & { readonly __opaque__: K };

type Email = Opaque<string, 'Email'>;
type Phone = Opaque<string, 'Phone'>;

function sendEmail(email: Email): void {}
function sendSMS(phone: Phone): void {}

sendEmail('test@test.com' as Email); // Need cast
// sendEmail('+1234567890' as Phone); // Error

// Practical: Currency amounts
type USD = Brand<number, 'USD'>;
type EUR = Brand<number, 'EUR'>;

function addUSD(a: USD, b: USD): USD {
  return (a + b) as USD;
}

const dollars = 100 as USD;
const euros = 200 as EUR;
// addUSD(dollars, euros); // Error - type mismatch
```

## 20. How do you type `fetch` API responses?

**Answer:**

```typescript
// Generic fetch wrapper
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
}

interface User {
  id: number;
  name: string;
  email: string;
}

async function fetchApi<T>(url: string, options?: RequestInit): Promise<T> {
  const response = await fetch(url, options);
  
  if (!response.ok) {
    throw new ApiError(response.status, await response.text());
  }
  
  return response.json();
}

class ApiError extends Error {
  constructor(
    public statusCode: number,
    message: string
  ) {
    super(message);
    this.name = 'ApiError';
  }
}

// Typed usage
async function getUser(id: number): Promise<User> {
  return fetchApi<User>(`/api/users/${id}`);
}

// With validation (Zod)
import { z } from 'zod';

const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email()
});

type ValidatedUser = z.infer<typeof UserSchema>;

async function fetchValidatedUser(id: number): Promise<ValidatedUser> {
  const data = await fetchApi<unknown>(`/api/users/${id}`);
  return UserSchema.parse(data);
}

// Generic API client
class ApiClient {
  private baseUrl: string;
  
  constructor(baseUrl: string) {
    this.baseUrl = baseUrl;
  }
  
  async get<T>(path: string): Promise<T> {
    const response = await fetch(`${this.baseUrl}${path}`);
    return response.json();
  }
  
  async post<T, B>(path: string, body: B): Promise<T> {
    const response = await fetch(`${this.baseUrl}${path}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body)
    });
    return response.json();
  }
}
```

## 21. Explain covariance and contravariance.

**Answer:**

```typescript
// Covariance - preserves ordering of types
// Contravariance - reverses ordering

// Covariance in arrays (TypeScript arrays are covariant)
type Parent = { name: string };
type Child = { name: string; age: number };

const childArr: Child[] = [{ name: 'Alice', age: 10 }];
const parentArr: Parent[] = childArr; // OK (covariant)
// This is type-safe because we only read from arrays

// But writing is unsafe:
// parentArr.push({ name: 'Bob' }); // OK at compile, but childArr now has non-Child
// TypeScript allows this for practical reasons

// Function parameter types are contravariant
type Handler = (value: string | number) => void;

// Contravariance: a function that accepts 'string | number' can be assigned
// to a handler that expects 'string' or 'number'
const stringHandler: (value: string) => void = (v) => v.toUpperCase();
const numberHandler: (value: number) => void = (v) => v.toFixed();

// But NOT the other way:
// const badHandler: Handler = stringHandler; // Error if strict mode
// Because stringHandler would fail on number input

// Function return types are covariant
type Producer = () => string | number;
type StringProducer = () => string;
type NumberProducer = () => number;

const producer: Producer = (): string | number => Math.random() > 0.5 ? 'hi' : 42;

// Covariant: more specific return can be assigned
const sp: StringProducer = () => 'hello';
const np: NumberProducer = () => 42;
const p1: Producer = sp; // OK
const p2: Producer = np; // OK

// Method parameter bivariance
// With methods (not function properties), TypeScript is bivariant by default
interface Comparer {
  compare(a: number, b: number): number;
}

// OK because it's a method (bivariance allowed)
const comparer: Comparer = {
  compare(a: any, b: any) { return a - b; }
};
```

## 22. What is the `noUncheckedIndexedAccess` flag?

**Answer:**

```typescript
// When enabled, accessing array/object with index returns T | undefined

// Without flag - assumes element exists
interface StringMap {
  [key: string]: string;
}

const map: StringMap = {};
map['hello'].toUpperCase(); // Runtime error but no compile error

// With noUncheckedIndexedAccess
interface SafeStringMap {
  [key: string]: string;
}

const safeMap: SafeStringMap = {};
// safeMap['hello'].toUpperCase(); // Error: Object is possibly 'undefined'

// Must check or use non-null assertion
const value = safeMap['hello'];
if (value) {
  value.toUpperCase(); // OK - narrowed
}

safeMap['hello']!.toUpperCase(); // Non-null assertion (use sparingly)

// Arrays also affected
const arr: string[] = ['a', 'b', 'c'];
const first = arr[0]; // Type: string | undefined (with flag)

// Fixed with destructuring
const [first2, ...rest] = ['a', 'b', 'c'];
// first2: string (non-undefined because of destructuring)

// Practical approach: use Record for simple maps
const record: Record<string, string> = {};
record['hello']?.toUpperCase(); // Optional chaining

// Or use Map for type safety
const betterMap = new Map<string, string>();
betterMap.get('hello')?.toUpperCase(); // Always returns T | undefined
```

## 23. How do you type React hooks and components?

**Answer:**

```typescript
// useState with type inference
const [count, setCount] = useState(0); // number
const [name, setName] = useState(''); // string
const [user, setUser] = useState<User | null>(null); // Union
const [items, setItems] = useState<string[]>([]); // Array

// useReducer
interface State { count: number; }
type Action = { type: 'increment' } | { type: 'decrement' } | { type: 'reset'; payload: number };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    case 'reset': return { count: action.payload };
  }
}

// useRef
const inputRef = useRef<HTMLInputElement>(null); // Mutable
const intervalRef = useRef<ReturnType<typeof setInterval>>(); // Mutable

// useCallback/useMemo
const memoizedCallback = useCallback((id: number) => {
  doSomething(id);
}, [deps]);

const memoizedValue = useMemo(() => {
  return expensiveComputation(a, b);
}, [a, b]);

// Typing component props
interface ButtonProps {
  variant: 'primary' | 'secondary';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  children: React.ReactNode;
  onClick: (event: React.MouseEvent<HTMLButtonElement>) => void;
}

function Button({ variant, size = 'md', disabled, children, onClick }: ButtonProps) {
  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      disabled={disabled}
      onClick={onClick}
    >
      {children}
    </button>
  );
}

// ForwardRef with generics
const FancyInput = React.forwardRef<HTMLInputElement, { label: string }>(
  (props, ref) => {
    return (
      <div>
        <label>{props.label}</label>
        <input ref={ref} />
      </div>
    );
  }
);

// Custom hook typing
function useLocalStorage<T>(key: string, initialValue: T): [T, (value: T) => void] {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = (value: T) => {
    setStoredValue(value);
    window.localStorage.setItem(key, JSON.stringify(value));
  };

  return [storedValue, setValue];
}

// Generic component
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return <ul>{items.map(renderItem)}</ul>;
}

// Usage: <List items={users} renderItem={(user) => <li>{user.name}</li>} />
```

## 24. How do you implement a type-safe event emitter?

**Answer:**

```typescript
// Type-safe Event Emitter
type EventMap = {
  userLoggedIn: { userId: string; timestamp: number };
  userLoggedOut: { userId: string };
  profileUpdated: { userId: string; changes: Record<string, unknown> };
  error: { message: string; code?: number };
};

class TypedEventEmitter<T extends Record<string, unknown>> {
  private handlers = new Map<keyof T, Set<(...args: any[]) => void>>();

  on<K extends keyof T>(event: K, handler: (data: T[K]) => void): void {
    if (!this.handlers.has(event)) {
      this.handlers.set(event, new Set());
    }
    this.handlers.get(event)!.add(handler);
  }

  off<K extends keyof T>(event: K, handler: (data: T[K]) => void): void {
    this.handlers.get(event)?.delete(handler);
  }

  emit<K extends keyof T>(event: K, data: T[K]): void {
    this.handlers.get(event)?.forEach(handler => handler(data));
  }

  once<K extends keyof T>(event: K, handler: (data: T[K]) => void): void {
    const wrapper = (data: T[K]) => {
      handler(data);
      this.off(event, wrapper);
    };
    this.on(event, wrapper);
  }
}

// Usage
const emitter = new TypedEventEmitter<EventMap>();

emitter.on('userLoggedIn', (data) => {
  console.log(`User ${data.userId} logged in at ${data.timestamp}`);
  // data.userId (string), data.timestamp (number)
});

emitter.emit('userLoggedIn', { userId: '123', timestamp: Date.now() });
// emitter.emit('userLoggedIn', { userId: '123' }); // Error: missing timestamp
```

## 25. Explain the `in` operator narrowing for types.

**Answer:**

```typescript
// The in operator narrows types by checking if a property exists

interface Admin {
  id: string;
  role: 'admin';
  permissions: string[];
}

interface User {
  id: string;
  role: 'user';
  department: string;
}

type Person = Admin | User;

function handlePerson(person: Person) {
  // Using 'in' operator
  if ('permissions' in person) {
    // TypeScript narrows to Admin
    console.log(person.permissions);
    // person.permissions: string[]
    // person.role: 'admin'
  } else {
    // TypeScript narrows to User
    console.log(person.department);
    // person.department: string
    // person.role: 'user'
  }
}

// With discriminated union
type Shape = 
  | { kind: 'circle'; radius: number }
  | { kind: 'rectangle'; width: number; height: number }
  | { kind: 'triangle'; base: number; height: number };

function getArea(shape: Shape) {
  if ('radius' in shape) {
    return Math.PI * shape.radius ** 2;
  }
  // shape is narrowed to rectangle | triangle
  if ('width' in shape) {
    return shape.width * shape.height;
  }
  return (shape.base * shape.height) / 2;
}

// 'in' vs discriminated union
// Discriminated union (kind): better for exhaustive checking
// 'in': better when no discriminator or checking presence

// Narrowing with optional properties
interface WithMeta {
  meta?: { version: number; author: string };
}

function process(obj: WithMeta) {
  if ('meta' in obj) {
    // obj.meta is { version: number; author: string }
    // Note: still optional, need ! or optional chaining
  }
}
```
