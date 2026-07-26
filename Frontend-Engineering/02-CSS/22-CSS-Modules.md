# CSS Modules

## Overview

**CSS Modules** are CSS files where all class and animation names are **scoped locally by default**. They solve the global namespace problem in CSS by generating unique class names at build time.

## How CSS Modules Work

```css
/* styles.module.css */
.button {
  background: blue;
  color: white;
  padding: 0.5em 1em;
}

.primary {
  background: green;
}
```

The build tool transforms class names into unique identifiers:

```css
/* Compiled output */
._button_1a2b3 { background: blue; color: white; padding: 0.5em 1em; }
._primary_4c5d6 { background: green; }
```

```jsx
// React component
import styles from './styles.module.css';

function Button() {
  return <button className={`${styles.button} ${styles.primary}`}>Click</button>;
  // Renders: <button class="_button_1a2b3 _primary_4c5d6">Click</button>
}
```

## Setup

CSS Modules work **out of the box** with:

- **Vite** — files named `*.module.css` are automatically treated as CSS Modules
- **Next.js** — CSS Modules are built-in (`*.module.css`)
- **Create React App** — built-in support
- **Webpack** — `css-loader` with `modules: true`

### Vite

```js
// vite.config.js — works automatically for .module.css
export default { }
```

```css
/* Button.module.css — no special config needed */
```

### Next.js

```css
/* components/Button.module.css */
```

CSS Modules are supported by default — no config needed.

## Scoped Styles

CSS Modules generate unique class names to prevent collisions:

```css
/* Card.module.css */
.card { border: 1px solid #ddd; }
.title { font-size: 1.25rem; }
.body { color: #666; }
```

```jsx
import styles from './Card.module.css';

function Card() {
  return (
    <div className={styles.card}>
      <h2 className={styles.title}>Title</h2>
      <p className={styles.body}>Content</p>
    </div>
  );
}
```

No matter how many components use `.title`, each gets a unique hash.

## Composition

CSS Modules allow composing classes from other classes:

```css
/* styles.module.css */
.base {
  padding: 0.5em 1em;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.primary {
  composes: base;
  background: blue;
  color: white;
}

.secondary {
  composes: base;
  background: gray;
  color: black;
}
```

```jsx
import styles from './styles.module.css';

<button className={styles.primary}>Save</button>
<button className={styles.secondary}>Cancel</button>
```

### Composition from Other Files

```css
/* typography.module.css */
.heading { font-family: 'Inter', sans-serif; font-weight: 700; }

/* card.module.css */
@value typography from './typography.module.css';

.title {
  composes: heading from './typography.module.css';
  font-size: 1.5rem;
}
```

## Global Styles in CSS Modules

Sometimes you need global styles (e.g., resets, body styles):

```css
/* app.module.css */
:global(body) {
  margin: 0;
  font-family: sans-serif;
}

:global(.dark-mode) {
  --bg: black;
  --text: white;
}

.local-class {
  color: var(--text);
}
```

Or use a separate global CSS file:

```css
/* globals.css — not a module */
body { margin: 0; }
```

## CSS Modules with Preprocessors

CSS Modules work with Sass/SCSS:

```scss
// Card.module.scss
.card {
  border: 1px solid #ddd;

  &__title {
    font-size: 1.25rem;
  }
}
```

```jsx
import styles from './Card.module.scss';
```

## Conditional Classes

```jsx
// React
import styles from './Button.module.css';

function Button({ primary, disabled }) {
  return (
    <button
      className={[
        styles.button,
        primary && styles.primary,
        disabled && styles.disabled,
      ].filter(Boolean).join(' ')}
    />
  );
}
```

Using a helper (like `clsx`):

```bash
npm install clsx
```

```jsx
import clsx from 'clsx';
import styles from './Button.module.css';

<button className={clsx(styles.button, primary && styles.primary)} />
```

## CSS Modules in Different Frameworks

### React (Vite/Next.js)

```jsx
import styles from './Component.module.css';
```

### Vue

Vue uses `<style scoped>` which achieves a similar goal without CSS Modules:

```vue
<style scoped>
.button { background: blue; }
</style>
```

But you can use CSS Modules in Vue:

```vue
<style module>
.button { background: blue; }
</style>
<template>
  <button :class="$style.button">Click</button>
</template>
```

## Benefits of CSS Modules

| Benefit | Description |
|---------|-------------|
| **Scoped styles** | No global conflicts |
| **Explicit dependencies** | JS imports CSS — tree-shakable |
| **No runtime cost** | Compiled at build time |
| **Native CSS** | Use any CSS syntax/features |
| **Composability** | `composes` keyword |
| **Framework agnostic** | Works with React, Vue, Svelte, vanilla JS |
| **TypeScript support** | `*.module.css` type definitions available |

## Drawbacks

| Drawback | Explanation |
|----------|-------------|
| Bulky class names | Hashed names are not human-readable in DevTools |
| No dynamic styles | Can't change values at runtime (use CSS variables) |
| Setup required | Only works with bundler |
| CamelCase | `styles['my-class']` vs `styles.myClass` — dash-case requires bracket notation |
| Global styles | Still need a global stylesheet for resets/fonts |

## TypeScript Support

For TypeScript, you need type definitions:

```typescript
// globals.d.ts
declare module '*.module.css' {
  const classes: { readonly [key: string]: string };
  export default classes;
}
```

Or use a plugin:

```bash
npm install -D @types/css-modules
```

## CSS Modules vs Other Approaches

| Approach | Scope | Runtime Cost | Setup |
|----------|-------|-------------|-------|
| Plain CSS | Global | None | None |
| CSS Modules | File/Component | None | Bundler |
| CSS-in-JS | Component | Size + computation | Framework |
| Tailwind | Global | None (purged) | Bundler + config |
| Scoped (Vue) | Component | None | Vue compiler |

## Real-World Example

```css
/* components/ProductCard.module.css */
.card {
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  transition: transform 0.2s, box-shadow 0.2s;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.15);
}

.image {
  width: 100%;
  height: 200px;
  object-fit: cover;
}

.content {
  padding: 1rem;
}

.title {
  font-size: 1.25rem;
  font-weight: 600;
  margin: 0 0 0.5rem;
}

.price {
  font-size: 1.5rem;
  color: #007bff;
  font-weight: 700;
}
```

```jsx
import styles from './ProductCard.module.css';

export function ProductCard({ product }) {
  return (
    <div className={styles.card}>
      <img src={product.image} alt={product.name} className={styles.image} />
      <div className={styles.content}>
        <h3 className={styles.title}>{product.name}</h3>
        <p className={styles.price}>${product.price}</p>
      </div>
    </div>
  );
}
```
