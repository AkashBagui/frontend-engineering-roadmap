# React Performance

Performance optimization in React is about **avoiding unnecessary work** — preventing re-renders that produce the same output and reducing the cost of expensive operations.

## React.memo

`React.memo` is a higher-order component that memoizes the rendered output. It performs a **shallow comparison** of props and skips re-rendering if props haven't changed.

```jsx
const MemoizedCard = React.memo(function Card({ title, content }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <p>{content}</p>
    </div>
  );
});

// With custom comparison function
const MemoizedList = React.memo(List, (prevProps, nextProps) => {
  return prevProps.items.length === nextProps.items.length &&
         prevProps.items.every((item, i) => item.id === nextProps.items[i].id);
});
```

### When to use React.memo

- **Pure presentational components** — same props always produce same output
- **Components that render often** due to parent re-renders but receive stable props
- **Leaf components** at the bottom of the tree
- **List item components** in large lists

### When NOT to use React.memo

- Components with cheap renders
- Components whose props always change
- Components with heavy memoization overhead (> cost of re-render)

## useMemo

Memoizes the **result of a computation**:

```jsx
function Dashboard({ transactions, users }) {
  // Expensive computation — only recompute when transactions change
  const totals = useMemo(() => {
    return transactions.reduce((acc, t) => ({
      revenue: acc.revenue + t.amount,
      count: acc.count + 1,
    }), { revenue: 0, count: 0 });
  }, [transactions]);

  // Complex filtering
  const activeUsers = useMemo(() => {
    return users.filter(u => u.active).sort((a, b) => a.name.localeCompare(b.name));
  }, [users]);

  return <div>Revenue: {totals.revenue}</div>;
}
```

### When to use useMemo

- Expensive computations (large array transformations, complex math)
- Creating objects/arrays that are dependencies of other hooks
- Stabilizing reference types passed to memoized children

## useCallback

Memoizes a **function reference**:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  // Without useCallback — new function every render
  const handleClick = () => setCount(c => c + 1);

  // With useCallback — same function reference
  const handleClick = useCallback(() => setCount(c => c + 1), []);

  return <ExpensiveButton onClick={handleClick} />;
}

// ExpensiveButton only re-renders if onClick reference changes
const ExpensiveButton = React.memo(({ onClick }) => {
  return <button onClick={onClick}>Click</button>;
});
```

### useMemo vs useCallback

```jsx
useMemo(() => fn, deps)  // returns the VALUE of fn() — the result
useCallback(fn, deps)    // returns the FUNCTION fn itself
// useCallback(fn, deps) === useMemo(() => fn, deps)
```

## Virtualization (react-window)

When rendering **thousands of items**, virtualize the list — only render what's visible:

```jsx
import { FixedSizeList } from 'react-window';

function VirtualList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      {items[index].name}
    </div>
  );

  return (
    <FixedSizeList
      height={600}
      width="100%"
      itemCount={items.length}
      itemSize={50} // row height in px
    >
      {Row}
    </FixedSizeList>
  );
}
```

| Library | Size | Features |
|---------|------|----------|
| react-window | ~3 KB | Fixed/variable size, grid |
| react-virtuoso | ~8 KB | Auto-height, sticky headers, grouping |
| @tanstack/virtual | ~3 KB | Headless virtualizer |

## Code Splitting (React.lazy)

Split your bundle so users only download what they need:

```jsx
import { lazy, Suspense } from 'react';
import { Route, Routes } from 'react-router-dom';

const Home = lazy(() => import('./pages/Home'));
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));
const Admin = lazy(() => import('./pages/Admin'));

function App() {
  return (
    <Suspense fallback={<PageSkeleton />}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/dashboard" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
        <Route path="/admin" element={<Admin />} />
      </Routes>
    </Suspense>
  );
}
```

## Bundle Analysis

### Analyze with source-map-explorer

```bash
npm run build
npx source-map-explorer build/static/js/*.js
```

### vite bundle analysis

```js
// vite.config.js
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [react(), visualizer({ open: true })],
});
```

### Key targets

- **< 100 KB** initial JS (critical path)
- **< 200 KB** total per route
- **< 50 KB** per lazy-loaded chunk

## Reconciliation Keys

The **`key` prop** helps React identify items in a list during reconciliation:

```jsx
// ❌ BAD: index as key — causes bugs with reordering, deletion
{items.map((item, index) => <Item key={index} item={item} />)}

// ✅ GOOD: stable unique ID
{items.map(item => <Item key={item.id} item={item} />)}

// ✅ ACCEPTABLE (stable list, no reordering): combined keys
{items.map((item, i) => <Item key={`${item.id}-${i}`} item={item} />)}
```

**Why keys matter:** Without stable keys, React may re-render the entire list, lose state (scroll position, input focus), or show wrong data after mutations.

## Profiling with DevTools

1. Install **React DevTools** browser extension
2. Open DevTools → Components → Profiler tab
3. Click the record button (⚫ → 🔴)
4. Interact with your app (click, type, navigate)
5. Stop recording
6. Analyze the flamegraph

### What to look for in the profiler

- **Unnecessary renders** — components re-rendering with same props
- **Slow renders** — wide bars in the flamegraph (high duration)
- **Render cascades** — one state change causing many components to re-render
- **Commit duration spike** — total time for a single update

## Performance Checklist

- [ ] Identify unnecessary re-renders with React DevTools Profiler
- [ ] Apply `React.memo` on expensive leaf components
- [ ] Use `useMemo` for expensive computations
- [ ] Use `useCallback` for callbacks passed to memoized children
- [ ] Virtualize long lists (> 1000 items)
- [ ] Lazy load route/page components
- [ ] Split context to limit re-render scope
- [ ] Keep state close to where it's used (lift down, not up)
- [ ] Avoid inline objects/arrays in JSX (extract to const or useMemo)
- [ ] Use stable keys for lists
- [ ] Analyze bundle size regularly
- [ ] Use `startTransition` for non-urgent updates
