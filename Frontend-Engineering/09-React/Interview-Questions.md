# React Interview Questions

## Core React

### 1. What is React? How does it differ from other JS frameworks?

React is a **UI library** for building component-based user interfaces. Unlike Angular (full framework) or Vue (progressive framework), React is unopinionated about routing, state management, and data fetching — you compose these from the ecosystem. React uses a **virtual DOM** for efficient updates and a **declarative** programming model.

### 2. Explain the Virtual DOM and how it works.

The Virtual DOM is a lightweight JS object tree representing the real DOM. When state changes:
1. A new VDOM tree is created
2. React diffs the new tree against the previous one (reconciliation)
3. Only the minimal set of real DOM mutations are applied

This batching avoids expensive direct DOM operations.

### 3. What is JSX? Why can't browsers read it directly?

JSX is a syntax extension that looks like HTML in JavaScript. Browsers can't read it because it's not valid JS. Tools like Babel or esbuild **transpile** JSX into `React.createElement()` calls during the build step.

### 4. What is the difference between declarative and imperative programming?

- **Imperative**: "How" — `document.getElementById('root').appendChild(element)`
- **Declarative**: "What" — `<App />` — React handles the DOM operations

React is declarative: you describe what the UI should look like for a given state.

### 5. What are the core principles of React?

1. **Declarative** — describe UI as a function of state
2. **Component-Based** — encapsulated, reusable pieces
3. **Unidirectional Data Flow** — data flows down via props, events flow up
4. **Immutable State** — state is never mutated directly
5. **Virtual DOM** — efficient UI updates

## Components & Props

### 6. Functional vs Class components — which should you use and why?

**Functional components** with hooks are the modern standard. They are simpler, less boilerplate, easier to test, and support all React features. Class components are legacy — you mostly see them in older codebases and Error Boundaries (though hooks + `componentDidCatch` alternatives exist).

### 7. What is a Pure Component? How does React.memo work?

`React.memo` is a higher-order component that memoizes the rendered output. It performs a **shallow comparison** of props. If props haven't changed, React skips re-rendering the component.

```jsx
const Memoized = React.memo(MyComponent, arePropsEqual?);
```

### 8. Explain the children prop and its use cases.

`children` is a special prop representing anything passed between opening and closing tags:

```jsx
<Card><p>Content</p></Card>
// Card receives props.children = <p>Content</p>
```

Used for layout components, wrappers, and composition patterns.

### 9. What is prop drilling and how do you solve it?

Prop drilling is passing data through multiple intermediate components that don't need it. Solutions:
- **Context API** for truly global data (theme, auth, locale)
- **Component composition** (lifting content up)
- **State management libraries** (Zustand, Redux) for complex state

### 10. Explain the concept of lifting state up.

When multiple components need to share the same state, move the state to their closest common ancestor and pass it down via props, with callbacks for updates.

## State & Lifecycle

### 11. What is the difference between state and props?

| Props | State |
|-------|-------|
| Passed from parent | Local to component |
| Immutable | Mutable (via setter) |
| Read-only | Read/Write |
| Triggers re-render when changed | Triggers re-render when updated |

### 12. How does useState work? What happens when you call setState?

`useState(initial)` returns `[value, setValue]`. When `setValue(newValue)` is called:
1. React schedules a re-render
2. The new state is stored
3. React re-renders the component with the new state value
4. React reconciles the VDOM and applies DOM updates

### 13. Why is state immutability important in React?

React uses **shallow comparison** (`===`) to detect changes. If you mutate an object/array directly, React can't detect the change. Always return new references:

```jsx
// Wrong: setUser(user.name = 'Bob')
// Correct: setUser({ ...user, name: 'Bob' })
```

### 14. What is the difference between controlled and uncontrolled components?

| Controlled | Uncontrolled |
|-----------|-------------|
| State managed by React | State managed by DOM |
| `value` + `onChange` | `ref` to access value |
| Predictable, single source of truth | Less code, but harder to validate |

### 15. Explain the component lifecycle in React with hooks.

**Mounting:** constructor → render → componentDidMount (`useEffect(() => {}, [])`)
**Updating:** render → componentDidUpdate (`useEffect(() => {}, [deps])`)
**Unmounting:** componentWillUnmount (`useEffect(() => { return cleanup }, [])`)
**Error:** componentDidCatch (error boundary)

## Hooks

### 16. What are the Rules of Hooks?

1. Only call hooks at the **top level** of a React function (not in loops, conditions, or nested functions)
2. Only call hooks from **React function components** or **custom hooks**

Why? React relies on the **order of hook calls** between renders to maintain state across re-renders.

### 17. How does useEffect differ from useLayoutEffect?

- `useEffect` fires **asynchronously** after the browser paints (non-blocking)
- `useLayoutEffect` fires **synchronously** after DOM mutations but before the browser paints

Use `useLayoutEffect` when you need to read layout (`getBoundingClientRect`) or make visual changes that the user should see immediately. Prefer `useEffect` for everything else.

### 18. What is the cleanup function in useEffect? When is it called?

The function returned from the effect callback:

```jsx
useEffect(() => {
  const sub = source.subscribe();
  return () => sub.unsubscribe(); // cleanup
}, []);
```

Called on:
- Unmount (always)
- Before re-running the effect (when deps change)

Used for subscriptions, timers, event listeners, aborting fetch requests.

### 19. Explain useMemo and useCallback. When should you use them?

`useMemo` memoizes a **value**: `useMemo(() => expensive(a, b), [a, b])`
`useCallback` memoizes a **function**: `useCallback(() => doSomething(a, b), [a, b])`

Use them when:
- The computation is expensive (useMemo)
- Passing callbacks to memoized children (useCallback)
- They stabilize reference equality for dependency arrays

**Don't** overuse them — they have memory overhead and add complexity.

### 20. What is useRef used for?

`useRef(initial)` returns a mutable object `{ current: initial }` that persists across renders without causing re-renders when changed.

Common uses:
1. Accessing DOM elements: `<div ref={ref}>`
2. Storing mutable values (previous state, interval IDs, flags)
3. Keeping references to non-React entities

### 21. How does useReducer work? When to use it over useState?

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
function reducer(state, action) {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    default: return state;
  }
}
```

Use `useReducer` when:
- State logic is complex (multiple sub-values, interdependent)
- Next state depends on previous state heavily
- You want centralized, predictable state transitions (like mini-Redux)

### 22. What are custom hooks? Give an example.

Custom hooks are JS functions that use built-in hooks to encapsulate reusable logic:

```jsx
function useWindowSize() {
  const [size, setSize] = useState({ width: 0, height: 0 });
  useEffect(() => {
    const handler = () => setSize({ width: window.innerWidth, height: window.innerHeight });
    window.addEventListener('resize', handler);
    return () => window.removeEventListener('resize', handler);
  }, []);
  return size;
}
```

## Performance

### 23. What causes unnecessary re-renders? How do you prevent them?

Causes: parent re-renders, state changes, context changes, stale props.

Prevention:
- `React.memo` for pure components
- `useMemo`/`useCallback` for stable references
- Lift state down (keep state close to where it's used)
- Use keys correctly in lists
- Split context (don't put everything in one context)

### 24. Explain code splitting in React. How does React.lazy work?

`React.lazy` enables dynamic imports for component-level code splitting:

```jsx
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));
<Suspense fallback={<Spinner />}>
  <HeavyComponent />
</Suspense>
```

The component is only loaded when it's rendered for the first time, reducing initial bundle size.

### 25. What is reconciliation? Why is the `key` prop important?

Reconciliation is React's algorithm for diffing the VDOM tree. The `key` prop helps React identify which items changed, were added, or removed in a list. Using stable, unique keys (like IDs) ensures efficient re-renders. Never use array index as key if the list order can change.

## Advanced

### 26. What is the Context API? When would you use it vs Redux/Zustand?

Context provides a way to pass data through the component tree without prop drilling.

- **Context**: Simple global state (theme, locale, auth user). Good for low-frequency updates.
- **Zustand**: Lightweight, minimal boilerplate, works outside React. Best for medium complexity.
- **Redux**: Predictable state container with middleware. Best for large apps with complex state logic, side effects, and team scaling.

Performance note: Context causes all consumers to re-render on any value change. Use context splitting to mitigate.

### 27. What are React Portals?

Portals render children into a DOM node outside the parent component's DOM hierarchy:

```jsx
ReactDOM.createPortal(child, document.getElementById('portal-root'))
```

Used for modals, tooltips, dropdowns — anything that needs to break out of overflow/ z-index containment.

### 28. What is an Error Boundary? What can't it catch?

Error Boundaries are React components that catch JS errors in their child component tree, log them, and display a fallback UI.

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  static getDerivedStateFromError(error) { return { hasError: true }; }
  componentDidCatch(error, info) { log(error, info); }
  render() { return this.state.hasError ? <h1>Error</h1> : this.props.children; }
}
```

Cannot catch: event handlers (use try/catch), async code, server-side rendering, errors in the error boundary itself.

### 29. Explain Higher-Order Components vs Render Props vs Hooks.

| Pattern | Description | Modern replacement |
|---------|-------------|-------------------|
| HOC | Function that wraps a component and returns enhanced component | Custom hooks |
| Render Props | Component that accepts a function as `render` prop | Custom hooks |
| Hooks | Functions that encapsulate stateful logic | — |

Hooks are now the preferred pattern as they avoid wrapper hell and compose naturally.

### 30. What are React Server Components (RSC)?

RSCs are components that run **only on the server** during rendering:
- **Zero client bundle size** — large dependencies stay on server
- **Direct data access** — query DB directly, no API layer needed
- **Automatic code splitting** — client components are loaded on demand
- **Streaming** — render HTML progressively

They coexist with Client Components (marked with `'use client'`). RSC cannot use state, effects, or browser APIs.

### 31. What is `useTransition` and when would you use it?

`useTransition` marks a state update as non-urgent — React keeps showing the current UI while the update is processed:

```jsx
const [isPending, startTransition] = useTransition();
startTransition(() => setQuery(input));
```

Used for: filtering large lists, search results, tab switching — any update that can be deferred to keep the UI responsive.

### 32. Explain StrictMode in React.

`<StrictMode>` is a development-only wrapper that:
- Double-invokes effects and renders to detect impure functions
- Checks for deprecated APIs (like `componentWillMount`)
- Warns about unsafe lifecycle methods
- Helps prepare for concurrent features

It does **not** affect production builds.

### 33. What is Suspense? What are its use cases?

`<Suspense>` lets you show a fallback while components deeper in the tree are loading:

```jsx
<Suspense fallback={<Loading />}>
  <SlowComponent />
</Suspense>
```

Use cases: code splitting (`React.lazy`), data fetching (with Suspense-enabled frameworks like Relay or React Router), streaming server rendering.

### 34. How does React batch state updates?

React **batches** multiple `setState` calls into a single re-render for performance. In React 18, batching happens in **all** contexts (event handlers, timeouts, promises, native events). Previously, it only batched inside event handlers.

```jsx
// Both updates are batched into one re-render
setCount(c => c + 1);
setFlag(f => !f);
```

### 35. What is the `use` hook in React 19?

`use` can read a **Promise** or **Context** directly inside render:

```jsx
const data = use(fetchData('/api/users')); // suspends until resolved
const theme = use(ThemeContext); // like useContext
```

Unlike hooks, `use` can be called inside loops and conditionals.

### 36. What are React Actions? (React 19)

Actions are async functions passed to form elements:

```jsx
<form action={async (formData) => {
  await submit(formData);
}} />
```

Combined with `useActionState` and `useFormStatus` for pending states, optimistic updates, and progressive enhancement.
