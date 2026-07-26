# React Cheat Sheet

## JSX Syntax

| Feature | Syntax |
|---------|--------|
| Fragment | `<>...</>` or `<React.Fragment>...</React.Fragment>` |
| Expression | `{expression}` |
| Conditional (ternary) | `{condition ? <A /> : <B />}` |
| Conditional (&&) | `{condition && <Component />}` |
| Map loop | `{items.map(item => <Item key={item.id} />)}` |
| Class → className | `<div className="container">` |
| Inline styles | `<div style={{ color: 'red', fontSize: 14 }}>` |
| Self-closing | `<img src={url} alt="" />` |
| Comments | `{/* comment */}` |

## Functional Component

```jsx
function Greeting({ name, age = 18 }) {
  return <h1>Hello, {name}!</h1>;
}
const Greeting = ({ name }) => <h1>Hello, {name}</h1>;
```

## Hooks API

### useState

```jsx
const [count, setCount] = useState(0);
setCount(count + 1);           // direct
setCount(prev => prev + 1);    // functional (use when depending on previous state)
setCount(1);                   // reset
```

### useEffect

```jsx
useEffect(() => { /* side effect */ }, []);
useEffect(() => { /* runs on mount + when deps change */ }, [dep]);
useEffect(() => { return () => cleanup; }, []);
```

| Dependency array | Runs |
|-----------------|------|
| omitted | after every render |
| `[]` | once on mount |
| `[a, b]` | when `a` or `b` change |

### useRef

```jsx
const ref = useRef(initialValue);
ref.current; // mutable, persists across renders, no re-render on change
```

### useContext

```jsx
const value = useContext(MyContext);
```

### useReducer

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
// reducer: (state, action) => newState
```

### useMemo

```jsx
const memoized = useMemo(() => computeExpensive(a, b), [a, b]);
```

### useCallback

```jsx
const memoizedFn = useCallback(() => doSomething(a, b), [a, b]);
```

### useLayoutEffect

Same as `useEffect` but fires synchronously after DOM mutations. Use for reading layout and synchronous re-renders.

### useImperativeHandle

```jsx
useImperativeHandle(ref, () => ({ focus, reset }), []);
```

### useDebugValue

```jsx
useDebugValue(value); // shows in React DevTools
```

## Rules of Hooks

1. **Call hooks at the top level** — not inside loops, conditions, or nested functions.
2. **Only call hooks from React functions** — functional components or custom hooks.

## Common Patterns

### Conditional Rendering

```jsx
{isLoading ? <Spinner /> : <Data />}
{isLoading && <Spinner />}
{isLoading || <Data />}
```

### List Rendering

```jsx
{items.map((item, index) => (
  <Item key={item.id} {...item} />
))}
```

### Event Handling

```jsx
<button onClick={() => setCount(c => c + 1)}>+</button>
<button onClick={handleClick}>+</button>
```

### Controlled Input

```jsx
const [value, setValue] = useState('');
<input value={value} onChange={e => setValue(e.target.value)} />
```

### Lifting State Up

```jsx
function Parent() {
  const [data, setData] = useState(null);
  return <Child data={data} onChange={setData} />;
}
```

## Performance

| Technique | Hook/API | When to use |
|-----------|----------|-------------|
| Memoize component | `React.memo(Comp)` | Props don't change often |
| Memoize value | `useMemo(() => val, [deps])` | Expensive computation |
| Memoize callback | `useCallback(fn, [deps])` | Pass stable fn to memoized children |
| Lazy load | `React.lazy(() => import('./Comp'))` | Code-split route/page components |
| Virtualize list | `react-window` / `react-virtuoso` | Long lists (1000+ items) |
| Avoid inline objects | Extract to `const styles = {}` | Re-render prevention |
| Key prop | Stable unique keys | List reconciliation |

## React Router v6

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/" element={<Home />} />
    <Route path="/users/:id" element={<User />} />
    <Route path="/dashboard/*" element={<Dashboard />} />
  </Routes>
</BrowserRouter>
```

```jsx
import { Link, NavLink, useParams, useNavigate, Outlet } from 'react-router-dom';

const { id } = useParams();
const navigate = useNavigate();
navigate('/home');
<NavLink to="/" className={({ isActive }) => isActive ? 'active' : ''} />
<Outlet /> {/* renders nested route */}
```

### Lazy Loading

```jsx
const LazyPage = React.lazy(() => import('./pages/Page'));
<React.Suspense fallback={<Loading />}>
  <LazyPage />
</React.Suspense>
```

## Context API

```jsx
const ThemeContext = React.createContext('light');
<ThemeContext.Provider value="dark">
  <App />
</ThemeContext.Provider>
const theme = useContext(ThemeContext);
```

## Error Boundary

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };
  static getDerivedStateFromError() { return { hasError: true }; }
  componentDidCatch(error, info) { logError(error, info); }
  render() { return this.state.hasError ? <Fallback /> : this.props.children; }
}
```

## Key Differences React 18 → 19

| Feature | React 18 | React 19 |
|---------|----------|----------|
| Concurrent rendering | `createRoot`, `startTransition` | Stable, enhanced |
| Automatic batching | Batched in event handlers + timeouts | Batched everywhere |
| use() hook | — | `use(promise)` / `use(context)` |
| Server Components | Experimental | Stable |
| Actions | — | `<form action={fn}>`, `useActionState` |
| useOptimistic | — | `useOptimistic` |
| useFormStatus | — | `useFormStatus` |
