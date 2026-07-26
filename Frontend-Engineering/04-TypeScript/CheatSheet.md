# TypeScript Cheat Sheet

## Primitive Types

| Type | Example |
|---|---|
| `string` | `'hello'`, `"world"`, `` `template` `` |
| `number` | `42`, `3.14`, `0xff`, `1e3` |
| `boolean` | `true`, `false` |
| `null` | `null` |
| `undefined` | `undefined` |
| `symbol` | `Symbol('id')` |
| `bigint` | `100n`, `BigInt(100)` |

## Type Annotations

```typescript
let x: string = 'hello';
function fn(a: number, b: number): number { return a + b; }
const obj: { name: string } = { name: 'Alice' };
```

## Object Types

```typescript
{ x: number; y: number }
{ name: string; age?: number }
{ readonly id: number }
{ [key: string]: unknown }
```

## Array / Tuple

```typescript
number[]              // array of numbers
Array<string>         // array of strings
readonly number[]     // readonly array
[string, number]      // tuple of [string, number]
[string, ...number[]] // tuple with rest
```

## Special Types

| Type | Description |
|---|---|
| `any` | Opt-out of type checking (avoid) |
| `unknown` | Type-safe `any` (check before use) |
| `never` | Never occurs (exhaustive checks, thrown errors) |
| `void` | Return value ignored/absent |

## Function Types

```typescript
(a: string) => number
(a: string, b?: number) => void
{ (x: number): string; description: string }  // call signature
new (s: string) => SomeClass                   // constructor signature
```

## Interface

```typescript
interface User {
  name: string;
  age?: number;
  readonly id: number;
}

interface Admin extends User {
  role: 'admin';
}
```

## Type Aliases

```typescript
type Point = { x: number; y: number };
type ID = string | number;
type ReadonlyUser = { readonly [K in keyof User]: User[K] };
type Box<T> = { value: T };
```

## Union & Intersection

```typescript
string | number       // either
A & B                 // both
```

## Literal Types

```typescript
type Direction = 'up' | 'down';
type Dice = 1 | 2 | 3 | 4 | 5 | 6;
type True = true;
```

## Key Operators

| Operator | Description | Example |
|---|---|---|
| `keyof T` | Keys of T | `keyof User` → `'name' \| 'age'` |
| `typeof x` | Type of value | `typeof user` → `User` |
| `T[K]` | Indexed access | `User['name']` → `string` |
| `T[K][]` | Array of indexed | `User[keyof User]` → `string \| number` |
| `as const` | Literal inference | `{ x: 3 } as const` → `{ readonly x: 3 }` |
| `satisfies T` | Verify shape | `x satisfies Record<string, string>` |

## Utility Types

| Utility | Description | Example |
|---|---|---|
| `Partial<T>` | All optional | `Partial<User>` |
| `Required<T>` | All required | `Required<Config>` |
| `Readonly<T>` | All readonly | `Readonly<Point>` |
| `Pick<T, K>` | Pick keys | `Pick<User, 'id' \| 'name'>` |
| `Omit<T, K>` | Omit keys | `Omit<User, 'password'>` |
| `Record<K, T>` | K→T object | `Record<'a' \| 'b', number>` |
| `Exclude<T, U>` | Remove from union | `Exclude<string \| number, string>` |
| `Extract<T, U>` | Keep from union | `Extract<string \| number, string>` |
| `NonNullable<T>` | Remove null/undefined | `NonNullable<string \| null>` |
| `ReturnType<T>` | Return type of fn | `ReturnType<typeof fn>` |
| `Parameters<T>` | Params tuple | `Parameters<typeof fn>` |
| `Awaited<T>` | Unwrap Promise | `Awaited<Promise<string>>` |
| `ThisParameterType<T>` | This type of fn | `ThisParameterType<typeof fn>` |

## Generic Constraints

```typescript
<T extends { length: number }>    // constrained
<T, K extends keyof T>            // keyof constraint
<T extends new (...args: any[]) => any>  // constructable
```

## Conditional Types

```typescript
T extends U ? X : Y
T extends Array<infer U> ? U : T   // with infer
```

## Mapped Types

```typescript
{ [K in keyof T]: T[K] }
{ [K in keyof T]?: T[K] }         // optional
{ readonly [K in keyof T]: T[K] } // readonly
{ [K in keyof T as `get${K}`]: T[K] } // key remapping
```

## Class

```typescript
class MyClass {
  private x: number;
  constructor(public name: string) {}
  abstract method(): void;
}
```

## Access Modifiers

| Modifier | Scope |
|---|---|
| `public` | Anywhere (default) |
| `protected` | Class + subclasses |
| `private` | Only this class |
| `readonly` | Assignment only in declaration/constructor |

## Compiler Options (tsconfig.json)

| Option | Effect |
|---|---|
| `strict: true` | All strict checks |
| `target: "ES2020"` | JS output version |
| `module: "ESNext"` | Module system |
| `outDir: "./dist"` | Output directory |
| `rootDir: "./src"` | Source directory |
| `declaration: true` | Generate .d.ts |
| `sourceMap: true` | Generate .js.map |
| `noUnusedLocals: true` | Error on unused vars |
| `noUnusedParameters: true` | Error on unused params |
| `noUncheckedIndexedAccess: true` | Index access includes undefined |
| `exactOptionalPropertyTypes: true` | Strict optional property handling |
| `esModuleInterop: true` | Better CJS/ESM interop |
| `skipLibCheck: true` | Skip .d.ts type checking (faster) |

## TSConfig Quick Start

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "outDir": "./dist",
    "rootDir": "./src"
  },
  "include": ["src"]
}
```

## Control Flow

```typescript
typeof x === 'string'           // type guard
x instanceof Date               // instanceof guard
'prop' in obj                   // in guard
x === null                      // null check
x != null                       // not null/undefined
x ?? defaultValue               // nullish coalescing
x?.prop                         // optional chaining
```

## Declaration Files

```typescript
declare module 'foo' { export function bar(): void; }
declare global { interface Window { myVar: string; } }
```

## Type vs Interface — When to Use

| Use Case | Prefer |
|---|---|
| Object shapes | interface |
| Unifying two types | type union (`\|`) |
| Extending in 3rd party | interface (merging) |
| Computed/mapped types | type |
| Tuples | type (or interface) |
| Primitives | type |
| Public API / library | interface |

## Common Patterns

```typescript
// Branded types
type UserId = string & { __brand: 'UserId' };

// Discriminated union
type Result<T> =
  | { ok: true; value: T }
  | { ok: false; error: string };

// Async wrapper
type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: string };
```

## CLI Commands

```bash
tsc                   # compile
tsc --watch           # watch mode
tsc --noEmit          # type check only
tsc --project tsconfig.json  # specific config
tsc --init            # create tsconfig.json
npx ts-node file.ts   # run directly
```
