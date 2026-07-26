# TypeScript Best Practices

## 1. Always Enable Strict Mode

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

`strict: true` enables all strict checks:
- `strictNullChecks` — eliminates the billion-dollar mistake
- `noImplicitAny` — ensures types are explicit where needed
- `strictFunctionTypes` — sounder function type checking
- `strictBindCallApply` — properly typed `bind`/`call`/`apply`
- `strictPropertyInitialization` — class properties must be initialized
- `noImplicitThis` — `this` must have a type
- `alwaysStrict` — emit `"use strict"`

**Without strict mode, TypeScript loses most of its value.**

## 2. Prefer Interfaces for Object Shapes

```typescript
// ✅ Prefer interfaces for objects
interface User {
  name: string;
  age: number;
}

// Use type for:
// ✅ Unions
type Status = 'active' | 'inactive';

// ✅ Intersections
type AdminWithRole = User & { role: string };

// ✅ Mapped types
type ReadonlyUser = { readonly [K in keyof User]: User[K] };

// ✅ Conditional types
type IsString<T> = T extends string ? true : false;

// ✅ Tuples
type Pair = [string, number];
```

**Why?** Interfaces benefit from:
- Declaration merging (augmenting 3rd-party types)
- Better performance (cached by compiler)
- Clearer error messages
- More idiomatic for class `implements`

## 3. Avoid `any` — Use `unknown` Instead

```typescript
// ❌ Avoid: loses all type safety
function process(input: any) {
  return input.name; // no error, even if input has no name
}

// ✅ Use unknown: forces type check
function process(input: unknown) {
  if (typeof input === 'object' && input !== null && 'name' in input) {
    return (input as { name: string }).name;
  }
  throw new Error('Invalid input');
}

// Or use a proper type guard
function hasName(value: unknown): value is { name: string } {
  return typeof value === 'object' && value !== null && 'name' in value;
}
```

**Exceptions:** When gradually migrating JS to TS, `any` can be a temporary bridge.

## 4. Naming Conventions

```typescript
// ✅ PascalCase for types, interfaces, enums, classes
interface UserProfile {}
type ApiResponse<T> = {};
enum Direction {}
class UserService {}

// ✅ camelCase for variables, functions, methods
const userName = 'Alice';
function getUserById(id: number) {}

// ✅ UPPER_CASE for constants (runtime, not type-level)
const MAX_RETRIES = 3;
const API_BASE_URL = 'https://api.example.com';

// ❌ Avoid Hungarian notation
interface IUser {} // ❌
type TUser {}     // ❌

// ❌ Avoid abbreviations (unless universally understood)
type UsrPrfl = {};    // ❌
type UserProfile = {}; // ✅
```

## 5. Function Patterns

```typescript
// ✅ Use explicit return types for public functions
function calculateTotal(items: Item[]): number {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// ✅ Use overloads for complex parameter combinations
function format(input: string): string;
function format(input: number): string;
function format(input: string | number): string {
  return input.toString();
}

// ✅ Prefer default params over || in the body
function createUser(name: string, role: string = 'user') {} // ✅
function createUser(name: string, role?: string) {          // ❌
  const finalRole = role || 'user';
}
```

## 6. Null Safety

```typescript
// ✅ Use optional chaining
const city = user?.address?.city;

// ✅ Use nullish coalescing
const timeout = config.timeout ?? 3000;
// vs: const timeout = config.timeout || 3000; // catches 0, '' too

// ✅ Use strict equality (===) over ==
if (value === null) {}  // ✅
if (value == null) {}    // ❌ catches undefined too, but confusing

// ✅ Handle null in function returns
function findUser(id: number): User | null {
  const user = users.find(u => u.id === id);
  return user ?? null;
}
```

## 7. Type Inference — Let It Work

```typescript
// ✅ Let TS infer what it can
const name = 'Alice';          // 'Alice' (literal) — no need to annotate
let count = 0;                 // number
const items = [1, 2, 3];       // number[]

// ✅ Infer return types (unless function is public API)
function add(a: number, b: number) {
  return a + b; // inferred number
}

// ✅ Use contextual typing
[1, 2, 3].forEach((num) => {
  console.log(num.toFixed(2)); // num is number from context
});

// ❌ Redundant annotations
const x: number = 5;  // unnecessary
```

## 8. Project Organization

```typescript
src/
├── types/                 # Shared type definitions
│   ├── index.ts           # Re-exports
│   ├── api.ts             # API response types
│   ├── models.ts          # Domain models (User, Product, etc.)
│   └── common.ts          # Shared utility types
├── utils/
│   └── validation.ts
├── services/
│   └── userService.ts
├── components/
│   └── UserCard.tsx       # React components
└── index.ts               # Entry point
```

```json
// tsconfig.json — recommended settings
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "exactOptionalPropertyTypes": true,
    "noUncheckedIndexedAccess": true
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

## 9. Working with Third-Party Libraries

```bash
# Always install type definitions
npm install --save-dev @types/lodash
npm install --save-dev @types/react
npm install --save-dev @types/express

# Check if the library has built-in types
# Look for "types" or "typings" in node_modules/pkg/package.json
```

```typescript
// ✅ Use import, not require
import _ from 'lodash';

// ✅ Prefer modern ES module syntax
import express from 'express';
// NOT: const express = require('express');

// ✅ Use satisfies for complex type checks
const config = {
  apiUrl: 'https://api.example.com',
  timeout: 5000,
} satisfies Record<string, unknown>;
```

## 10. Error Handling

```typescript
// ✅ Always type catch clauses with unknown
try {
  // risky code
} catch (error: unknown) {
  if (error instanceof Error) {
    console.log(error.message);
  } else if (typeof error === 'string') {
    console.log(error);
  }
}

// ✅ Create custom error types
class AppError extends Error {
  constructor(
    message: string,
    public code: number,
    public details?: Record<string, unknown>
  ) {
    super(message);
    this.name = 'AppError';
  }
}

// ✅ Use discriminated unions for error states
type Result<T> =
  | { success: true; data: T }
  | { success: false; error: AppError };
```

## 11. Generics Best Practices

```typescript
// ✅ Use descriptive type parameter names
function transform<TInput, TOutput>(input: TInput, fn: (i: TInput) => TOutput): TOutput {
  return fn(input);
}

// ❌ Avoid single-letter names (except in trivial cases)
function map<T, U>(arr: T[], fn: (x: T) => U): U[]; // OK — convention

// ✅ Constrain generics where needed
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

// ❌ Avoid over-engineering
function identity<T, U, V>(a: T, b: U, c: V): [T, U, V]; // unnecessary complexity
```

## 12. Type Testing

```typescript
// Use the satisfies operator to verify types (TS 4.9+)
const palette = {
  red: '#ff0000',
  green: '#00ff00',
  blue: '#0000ff',
} satisfies Record<string, string>;

// Type-level tests (compile-time)
type _AssertEqual<T, U> = [T] extends [U] ? ([U] extends [T] ? true : never) : never;

type _Test1 = _AssertEqual<ReturnType<typeof add>, number>; // ✅ compiles
type _Test2 = _AssertEqual<Partial<{ a: number }>, { a?: number }>; // ✅ compiles
```

## 13. Rules of Thumb

```
✅ DO:
  - Enable strict: true
  - Prefer interfaces for objects
  - Use unknown instead of any
  - Let inference work for you
  - Write explicit return types on public APIs
  - Use branded/opaque types for IDs
  - Prefer discriminated unions
  - Use satisfies for complex checks

❌ DON'T:
  - Use any (except migration)
  - Suppress with @ts-ignore (use @ts-expect-error)
  - Use Hungarian prefixes (IUser, TType)
  - Over-annotate what TS can infer
  - Use type assertions (as) when not needed
  - Ignore compiler errors
  - Export types that should be internal
```

## tsconfig Checklist

```json
{
  "compilerOptions": {
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "exactOptionalPropertyTypes": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  }
}
```

**Next:** [CheatSheet.md](CheatSheet.md) — Quick Reference
