# Modules in JavaScript

## ES Modules (ESM)

```js
// math.js — named exports
export const add = (a, b) => a + b;
export const subtract = (a, b) => a - b;
const PI = 3.14159;
export { PI };

// Default export
export default function multiply(a, b) {
  return a * b;
}

// app.js — imports
import multiply, { add, subtract, PI } from "./math.js";
import * as Math from "./math.js";

Math.add(1, 2); // 3
Math.default(3, 4); // 12
```

## Export Variations

```js
// Inline export
export const x = 1;
export function fn() {}
export class Class {}

// Export list
const a = 1;
const b = 2;
export { a, b };

// Renaming
export { a as alpha, b as beta };

// Re-export
export { add } from "./math.js";
export * from "./utils.js";
export { default as Calc } from "./calc.js";
```

## Import Variations

```js
// Default import
import something from "./module.js";

// Named import
import { named } from "./module.js";

// Mixed
import defaultExport, { named1, named2 } from "./module.js";

// Rename
import { longName as short } from "./module.js";

// All as namespace
import * as Module from "./module.js";
Module.named; // access

// Side effects only (import for side effects)
import "./polyfills.js";

// Type import (TypeScript)
import type { Type } from "./types.js";
```

## Module Scope

```js
// module.js
const privateVar = "only in this module"; // not exported
export const publicVar = "visible outside";

// All modules are in strict mode by default
// Top-level this is undefined (not window)
```

## Dynamic Imports

```js
// Static import — at top, always loaded
import { heavy } from "./heavy.js";

// Dynamic import — on demand, returns promise
button.addEventListener("click", async () => {
  const module = await import("./heavy.js");
  module.heavy();
});

// Conditional import
const locale = getUserLocale();
const i18n = await import(`./locales/${locale}.js`);

// Preload hint
const module = import("./important.js"); // starts loading early
// later:
const result = await module;
```

## Circular Dependencies

```js
// a.js
import { b } from "./b.js";
export const a = () => b();
// At module evaluation time, b is undefined (TDZ)

// b.js
import { a } from "./a.js";
export const b = () => a();
// Mutual imports → undefined values
// Solution: restructure or use dynamic imports
```

## CommonJS vs ES Modules

| Feature | CommonJS (require) | ES Modules (import) |
|---------|-------------------|---------------------|
| Syntax | `const m = require("./m")` | `import m from "./m"` |
| Export | `module.exports = {}` | `export default {}` |
| Loading | Synchronous | Asynchronous |
| Static analysis | No (dynamic) | Yes (static) |
| Tree shaking | No | Yes |
| Top-level `this` | `module.exports` | `undefined` |
| File extension | `.js` (default) | `.mjs` or `"type": "module"` |
| Dynamic | `require()` anywhere | `import()` (async) |
| Cyclic deps | Partial exports available | TDZ issues |
| Browser | Needs bundler | Native support |

```js
// CommonJS
const fs = require("fs");
module.exports = { readFile: fs.readFile };
exports.helper = () => {}; // alias for module.exports

// ES Module
import fs from "fs";
export const readFile = fs.readFile;
export default () => {};
```

## Module Resolution

```
/root
  ├── package.json     { "type": "module" }
  ├── src/
  │   ├── index.js     import { utils } from "./utils.js"
  │   └── utils.js
  └── node_modules/
      └── lodash/
          └── index.js

// Bare specifier (node_modules)
import _ from "lodash";

// Relative path
import { thing } from "../utils/thing.js";

// Absolute path
import "/lib/helpers.js"   // rare

// URL imports (browser ESM)
import { fn } from "https://cdn.example.com/module.js";
```

## Tree Shaking

```js
// utils.js
export function used() { return "used"; }
export function unused() { return "dead code"; }

// app.js
import { used } from "./utils.js";
// Bundle will exclude unused() — tree shaking

// Helps to have:
// - package.json "sideEffects": false
// - Pure functions only
// - No side-effect imports unless intentional
```

## Module Loading Diagram

```
HTML → <script type="module">

1. Parse & Register
   ├── Recursively fetch dependencies
   └── Build module graph

2. Instantiate
   └── Create module records, connect exports/imports

3. Evaluate
   └── Execute code in dependency order (DFS)
       (depth-first, parents wait for children)

Order: math.js → utils.js → app.js
```

## Best Practices

1. Prefer named exports over default exports (better refactoring, imports by exact name)
2. Keep modules small and focused (single responsibility)
3. Avoid circular dependencies
4. Use barrel files (`index.js`) for clean public APIs
5. Use dynamic imports for code splitting (lazy loading)
6. Always include file extensions in relative imports (`.js`, `.mjs`)
