# Hooks

Hooks are functions that let you "hook into" React state and lifecycle features from functional components. Introduced in React 16.8, they replaced class lifecycle methods and made logic reuse possible without changing component hierarchy.

## Rules of Hooks

1. **Only call hooks at the top level.** Don't call hooks inside loops, conditions, or nested functions.
2. **Only call hooks from React function components** or custom hooks.
3. **Hooks must be called in the same order every render** — React relies on call order to associate state with the correct hook.

## Built-in Hooks

### useState

```jsx
const [state, setState] = useState(initialValue);
```
Stores state that persists across renders. See [04-State](../04-State/README.md) for details.

### useEffect

```jsx
useEffect(() => {
  // side effect: fetch, subscribe, DOM manipulation
  return () => {
    // cleanup: unsubscribe, clear timers, abort fetch
  };
}, [dependency1, dependency2]);
```

| Dependency array | Behavior |
|-----------------|----------|
| Not provided | Runs after every render |
| `[]` | Runs once on mount |
| `[a, b]` | Runs when `a` or `b` change |

**Cleanup function** runs on unmount and before re-running the effect.

```jsx
useEffect(() => {
  const controller = new AbortController();
  fetch('/api/data', { signal: controller.signal }).then(r => r.json()).then(setData);
  return () => controller.abort();
}, []);
```

### useContext

```jsx
const value = useContext(MyContext);
```
Subscribes to a React context and returns its current value. Triggers re-render when the context value changes.

### useReducer

```jsx
const [state, dispatch] = useReducer(reducer, initialState, init?);

function reducer(state, action) {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    case 'reset':     return { count: action.payload };
    default:          return state;
  }
}
```

Best for complex state logic with multiple sub-values or when next state depends on previous.

### useCallback

```jsx
const memoizedFn = useCallback(() => doSomething(a, b), [a, b]);
```
Returns a **memoized callback** that only changes if dependencies change. Use when passing callbacks to optimized child components that rely on reference equality.

### useMemo

```jsx
const memoizedValue = useMemo(() => computeExpensive(a, b), [a, b]);
```
Returns a **memoized value** that only recomputes when dependencies change. Use for expensive computations.

### useRef

```jsx
const ref = useRef(initialValue);
// ref.current persists across renders without causing re-renders
```

Use cases:
- Accessing DOM elements: `<div ref={ref}>`
- Storing mutable values: interval IDs, previous state
- Keeping references to non-React objects

```jsx
function TextInput() {
  const inputRef = useRef(null);
  return <input ref={inputRef} />;
}
```

### useImperativeHandle

```jsx
useImperativeHandle(ref, () => ({
  focus: () => inputRef.current.focus(),
  reset: () => (inputRef.current.value = ''),
}), []);
```

Customizes the instance value exposed when using `ref` with `forwardRef`.

### useLayoutEffect

```jsx
useLayoutEffect(() => {
  // Read layout: getBoundingClientRect, scroll position
  // Synchronous DOM mutations
}, [deps]);
```

Same as `useEffect` but fires **synchronously** after DOM mutations and before the browser paints. Use sparingly — blocks visual updates.

### useDebugValue

```jsx
function useWindowSize() {
  const [size, setSize] = useState({ width: 0, height: 0 });
  useDebugValue(size.width > 1000 ? 'large' : 'small');
  return size;
}
```

Displays a label in React DevTools for custom hooks.

### React 19 Hooks

| Hook | Purpose |
|------|---------|
| `useActionState(action, initialState)` | Manages form action state (pending, error, data) |
| `useFormStatus()` | Reads parent form's pending status |
| `useOptimistic(state, updateFn)` | Optimistic UI updates |
| `use(Promise)` | Reads a promise or context inside render |

## Custom Hooks

Custom hooks are **JavaScript functions that use built-in hooks** to encapsulate reusable logic.

```jsx
function useDocumentTitle(title) {
  useEffect(() => {
    document.title = title;
  }, [title]);
}

function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });
  useEffect(() => localStorage.setItem(key, JSON.stringify(value)), [key, value]);
  return [value, setValue];
}
```

Custom hooks follow the naming convention `useXxx`.

## Hook Lifecycle

```
┌─────────────────────────────────────────────────────────────────┐
│                    Component Mounts                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Render ──▶ React updates DOM ──▶ LayoutEffects ──▶ Effects     │
│                                     (sync)          (async)     │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│                    Component Updates                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Render ──▶ React updates DOM ──▶ Cleanup LayoutEffects         │
│                                   ──▶ LayoutEffects             │
│                                   ──▶ Cleanup Effects           │
│                                   ──▶ Effects                   │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│                    Component Unmounts                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Cleanup LayoutEffects ──▶ Cleanup Effects                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Comparison: useEffect vs useLayoutEffect

| useEffect | useLayoutEffect |
|-----------|----------------|
| Async (after paint) | Sync (before paint) |
| Non-blocking | Blocks visual updates |
| Default choice | Only when you need layout measurements |
| User sees flash if effect changes DOM | No flash — runs before browser paints |
