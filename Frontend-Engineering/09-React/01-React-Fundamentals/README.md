# React Fundamentals

## What is React?

React is a **JavaScript library** for building user interfaces, developed by Meta (Facebook). It lets you compose complex UIs from small, isolated pieces of code called **components**.

React is often described as the **V in MVC** — it handles the view layer, leaving data and logic management to your choice of tools and libraries.

## Declarative vs Imperative

The fundamental shift React introduces is moving from **imperative** to **declarative** programming.

| Imperative (Vanilla JS) | Declarative (React) |
|------------------------|--------------------|
| You describe *how* to do something | You describe *what* you want |
| `document.getElementById('root').innerHTML = '<h1>Hello</h1>'` | `<h1>Hello</h1>` |
| Manual DOM manipulation | React handles DOM updates |
| Step-by-step instructions | State-driven UI descriptions |

```jsx
// Imperative: tell the browser exactly what to do step by step
const header = document.createElement('h1');
header.textContent = 'Hello';
header.className = 'greeting';
document.getElementById('root').appendChild(header);

// Declarative: describe the desired outcome
function Greeting({ name }) {
  return <h1 className="greeting">Hello, {name}!</h1>;
}
// React figures out how to render it efficiently
```

## Virtual DOM

The Virtual DOM (VDOM) is a lightweight JavaScript object tree that mirrors the real DOM. When state changes, React creates a new VDOM tree and compares it with the previous one — a process called **reconciliation** — to determine the minimal set of DOM mutations needed.

### How it works

```
┌─────────────────────────────────────────────────────────────────────┐
│                          State Change                               │
│                              │                                      │
│                              ▼                                      │
│                    ┌─────────────────┐                              │
│                    │  New VDOM Tree  │                              │
│                    └────────┬────────┘                              │
│                             │                                       │
│                             ▼                                       │
│                    ┌─────────────────┐                              │
│                    │  Diff (Diffing) │◄──────── Old VDOM Tree       │
│                    └────────┬────────┘                              │
│                             │                                       │
│                             ▼                                       │
│                    ┌─────────────────┐                              │
│                    │  Batch Updates  │                              │
│                    └────────┬────────┘                              │
│                             │                                       │
│                             ▼                                       │
│                    ┌─────────────────┐                              │
│                    │  Real DOM Patch │                              │
│                    └─────────────────┘                              │
└─────────────────────────────────────────────────────────────────────┘
```

**Why is this faster?** Direct DOM manipulation is expensive (triggering layout recalculations, repaints). The VDOM diffing operates entirely in JavaScript memory, batches the changes, and applies them in a single DOM operation.

## JSX Syntax

JSX is a **syntax extension** for JavaScript that looks like HTML. It is not valid JavaScript — tools like Babel or esbuild transpile it to `React.createElement()` calls.

```jsx
// JSX
const element = <h1 className="title">Hello</h1>;

// Transpiled to
const element = React.createElement('h1', { className: 'title' }, 'Hello');
```

### JSX Rules

- **Single root element** — use `<></>` fragments for multiple siblings
- **Close all tags** — self-closing: `<img />`, regular: `<div></div>`
- **Use `{}` for JavaScript expressions** — variables, functions, ternaries
- **`className` not `class`** — because `class` is a reserved word in JS
- **CamelCase properties** — `onClick`, `onMouseOver`, `tabIndex`
- **Inline styles as objects** — `style={{ color: 'red', backgroundColor: 'blue' }}`

## Create-React-App vs Vite

| Feature | CRA (Create React App) | Vite |
|---------|----------------------|------|
| Dev server start | ~15–30s | < 1s (native ESM) |
| HMR (Hot Reload) | Full app reload on changes | Fast, module-level HMR |
| Build tool | Webpack | Rollup (esbuild for deps) |
| Configuration | Zero config but hard to customize | Zero config, easier to extend |
| TypeScript | Supported | First-class support |
| Bundle size | Larger (Webpack runtime) | Smaller |
| Status | **Deprecated / maintenance mode** | **Recommended** |

### Starting a new project

```bash
# Use Vite (recommended)
npm create vite@latest my-app -- --template react
npm create vite@latest my-app -- --template react-ts

# CRA (legacy)
npx create-react-app my-app
```

## React DevTools

React DevTools is a browser extension (Chrome/Firefox/Edge) for inspecting React component trees.

### Key Features

- **Components tab** — browse the component tree, view props, state, hooks
- **Profiler tab** — record and analyze render performance
- **Source** — jump to component source in your editor
- **Search** — find components by name

### Using the Profiler

1. Open DevTools → Profiler tab
2. Click the record button (🔴)
3. Interact with your app
4. Stop recording
5. View flamegraph — each colored bar is a render, width = time

**Pro tip:** Look for components that render unnecessarily (same props, no state change) — these are optimization targets for `React.memo`.
