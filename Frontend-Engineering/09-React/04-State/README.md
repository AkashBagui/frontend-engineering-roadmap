# State

State is **mutable data** that belongs to a component. Unlike props (received from parent), state is local, private, and fully controlled by the component.

## useState Hook

`useState` is the primary hook for adding state to functional components.

```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  //       ^      ^              ^
  //    state  setter       initial value

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  );
}
```

### Multiple state variables

```jsx
function Form() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(18);
  // Independent pieces of state → separate useState calls
}
```

### Complex state (objects)

```jsx
function UserProfile() {
  const [user, setUser] = useState({ name: '', email: '', settings: { theme: 'light' } });

  // Must spread to preserve other properties
  const updateName = (name) => setUser(prev => ({ ...prev, name }));
  const updateTheme = (theme) => setUser(prev => ({
    ...prev,
    settings: { ...prev.settings, theme }
  }));
}
```

## State Initialization

```jsx
// Simple initial value
const [count, setCount] = useState(0);

// Lazy initializer — function runs only on first render
const [todos, setTodos] = useState(() => {
  const saved = localStorage.getItem('todos');
  return saved ? JSON.parse(saved) : [];
});
```

**When to use lazy initialization:** Expensive computations, reading from localStorage, or any logic that shouldn't run on every re-render.

## State Updates: Direct vs Functional

```jsx
const [count, setCount] = useState(0);

// Direct update — uses current closure value
setCount(count + 1); // If called multiple times, uses stale closure

// Functional update — always receives latest state
setCount(prev => prev + 1); // Safe in all scenarios
```

### Why functional updates matter

```jsx
function BuggyCounter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1); // count is 0 → sets to 1
    setCount(count + 1); // count is still 0 → sets to 1 again!
    // Result: count = 1 (not 2)
  }

  function handleClickFixed() {
    setCount(prev => prev + 1); // prev is 0 → 1
    setCount(prev => prev + 1); // prev is 1 → 2
    // Result: count = 2
  }
}
```

## State Immutability

Never mutate state directly. Always create a new reference.

```jsx
const [items, setItems] = useState([{ id: 1, text: 'a' }]);

// ❌ WRONG — mutates state directly
items.push({ id: 2, text: 'b' });
setItems(items);

// ❌ WRONG — mutates nested object
items[0].text = 'updated';
setItems(items);

// ✅ CORRECT — new array, new objects
setItems([...items, { id: 2, text: 'b' }]);
setItems(items.map(item =>
  item.id === 1 ? { ...item, text: 'updated' } : item
));

// Removing
setItems(items.filter(item => item.id !== 1));
```

**Why immutability?** React uses `Object.is` comparison to detect changes. Mutations produce the same reference, so React skips re-rendering and your UI becomes stale.

## Lifting State Up

When multiple components need to share state, **lift** it to their closest common ancestor.

```
Before (state duplicated in children):
  App
  ├── SearchBox (own query state) ← typing here
  └── ResultsList (own query state) ← doesn't know about search

After (state lifted to App):
  App (query state lives here)
  ├── SearchBox (receives query, onChange)
  └── ResultsList (receives query, fetches results)
```

```jsx
function App() {
  const [query, setQuery] = useState('');
  return (
    <div>
      <SearchBox query={query} onChange={setQuery} />
      <ResultsList query={query} />
    </div>
  );
}
```

## Derived State

State that can be **computed** from existing state or props should not be stored separately.

```jsx
function Cart({ items }) {
  // ❌ Unnecessary derived state
  const [total, setTotal] = useState(0);
  useEffect(() => {
    setTotal(items.reduce((sum, item) => sum + item.price * item.qty, 0));
  }, [items]);

  // ✅ Compute directly
  const total = items.reduce((sum, item) => sum + item.price * item.qty, 0);
}
```

### State Flow Diagram

```
┌─────────────┐     User Action      ┌──────────────┐
│   Component  │────────────────────▶│   setState()  │
│   (render)   │                     │  (schedules)  │
└──────┬───────┘                     └──────┬─────────┘
       │                                    │
       │ React calls component              │ React batches
       │ with new state                     │ multiple updates
       ▼                                    ▼
┌──────────────────────────────────────────────────────┐
│                   Reconciliation                     │
│  Old VDOM  ──diff──▶  New VDOM  ──patch──▶  Real DOM│
└──────────────────────────────────────────────────────┘
```

### Key State Principles

1. **Single source of truth** — each piece of state lives in one component
2. **State minimal** — don't store what can be computed
3. **State down** — keep state as close as possible to where it's used
4. **Immutability** — never mutate, always replace
5. **Batching** — multiple setState calls in one event handler trigger one re-render
