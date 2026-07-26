# React Interview Questions

## 1. How does the Virtual DOM work?

**Answer:** The Virtual DOM is a lightweight JavaScript representation of the actual DOM. React uses it to minimize direct DOM manipulation, which is slow.

**How it works:**
1. **Render:** When state changes, React creates a new Virtual DOM tree
2. **Diffing:** React compares the new Virtual DOM with the previous one (reconciliation)
3. **Reconciliation:** Computes the minimal set of changes needed
4. **Commit:** Applies only the necessary changes to the real DOM

```javascript
// Simplified Virtual DOM node
const vNode = {
  type: 'div',
  props: { className: 'container' },
  children: [
    { type: 'h1', props: {}, children: ['Hello'] },
    { type: 'p', props: {}, children: ['World'] }
  ]
};
```

**Benefits:**
- **Batched updates:** Multiple state changes are batched
- **Minimal DOM operations:** Only changed elements are updated
- **Cross-platform:** Same abstraction can render to DOM, Native, or other targets

## 2. Explain React reconciliation and the diffing algorithm.

**Answer:** Reconciliation is the process of comparing two Virtual DOM trees to determine the minimal set of changes.

**Diffing algorithm assumptions (O(n) time complexity):**
1. **Different element types:** If root elements differ (e.g., div -> span), React rebuilds the entire subtree
2. **Same element type:** React preserves the DOM node, updates only changed attributes
3. **Keys:** React uses `key` prop to match children across renders

```javascript
// Different types - rebuilds entire subtree
// Before: <div><Counter /></div>
// After:  <span><Counter /></span>
// div and span are different types -> Counter is unmounted and remounted

// Same type - updates only changed props
// Before: <div className="old" />
// After:  <div className="new" />
// Only className attribute is updated in real DOM

// Keys - helps identify which items changed
// Without keys: inefficient re-rendering
// With keys: React tracks each item's identity
<ul>
  {items.map(item => <li key={item.id}>{item.name}</li>)}
</ul>
```

## 3. How do React hooks work? Explain the rules of hooks.

**Answer:** Hooks are functions that let you use state and lifecycle features in functional components.

**Rules of hooks:**
1. **Only call hooks at the top level** - don't call inside loops, conditions, or nested functions
2. **Only call hooks from React functions** - functional components or custom hooks

```javascript
// Correct - top level
function Counter() {
  const [count, setCount] = useState(0);
  useEffect(() => { document.title = `Count: ${count}`; }, [count]);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}

// Wrong - conditional hook
function BadComponent({ condition }) {
  if (condition) {
    const [state, setState] = useState(); // ERROR: conditional hook
  }
}
```

**Why these rules?** React relies on the order of hook calls to maintain state between renders. Each hook has a "memory cell" identified by its call order.

```javascript
// Behind the scenes (simplified)
let hookIndex = 0;
const hooks = [];

function useState(initial) {
  const i = hookIndex++;
  if (!hooks[i]) hooks[i] = { state: initial };
  return [hooks[i].state, (newState) => { hooks[i].state = newState; }];
}
```

## 4. Explain the `useEffect` hook lifecycle.

**Answer:**

```javascript
useEffect(() => {
  // 1. Runs AFTER render (componentDidMount + componentDidUpdate)
  
  return () => {
    // 2. Cleanup runs BEFORE next effect or unmount (componentWillUnmount)
  };
}, [dependencies]);
```

**Different dependency patterns:**

```javascript
// Run once on mount (componentDidMount)
useEffect(() => {
  fetchData();
}, []); // Empty array - runs only on mount

// Run on every re-render (no dependencies array)
useEffect(() => {
  console.log('Runs after every render');
}); // No dependency array

// Run when specific values change
useEffect(() => {
  console.log('Count changed to', count);
}, [count]); // Runs when `count` changes

// Cleanup example
useEffect(() => {
  const subscription = subscribeToEvent(data);
  return () => {
    subscription.unsubscribe(); // Cleanup on unmount or before re-run
  };
}, [data]);

// Multiple effects - separate concerns
useEffect(() => {
  // Analytics tracking
}, [page]);

useEffect(() => {
  // Data fetching
}, [userId]);
```

**Order of execution:**
1. Previous cleanup function runs
2. New JSX rendered to DOM
3. Browser paints
4. New effect function runs

## 5. What is the difference between `useEffect` and `useLayoutEffect`?

**Answer:**

```javascript
// useEffect - runs AFTER browser paint (asynchronous)
useEffect(() => {
  // May cause flicker if modifying layout
}, [deps]);

// useLayoutEffect - runs BEFORE browser paint (synchronous)
useLayoutEffect(() => {
  // Runs synchronously after DOM mutations but before paint
  // Use for measuring DOM or preventing flicker
}, [deps]);
```

| Aspect | useEffect | useLayoutEffect |
|--------|-----------|-----------------|
| Timing | After paint | Before paint |
| Nature | Async | Sync |
| Performance | Better | Can block visual updates |
| Use cases | Side effects, API calls, events | DOM measurements, scroll position, animations |

```javascript
// useLayoutEffect use case - measure DOM to prevent flicker
function Tooltip({ text }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  const tooltipRef = useRef();
  
  useLayoutEffect(() => {
    const rect = tooltipRef.current.getBoundingClientRect();
    setPosition({ x: -rect.width / 2, y: -rect.height - 10 });
    // Happens before paint - no flicker
  }, [text]);
  
  return <div ref={tooltipRef} style={{ transform: `translate(${position.x}px, ${position.y}px)` }}>{text}</div>;
}
```

## 6. How does `useCallback` differ from `useMemo`?

**Answer:**

```javascript
// useCallback - memoizes a FUNCTION itself
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);
// Returns: the function reference (stable if deps unchanged)

// useMemo - memoizes a VALUE (result of computation)
const sortedList = useMemo(() => {
  return items.sort((a, b) => a.name.localeCompare(b.name));
}, [items]);
// Returns: the sorted array (recomputed only if items change)
```

```javascript
// When to use each:

// useCallback - prevent unnecessary re-renders of children
function Parent() {
  const [count, setCount] = useState(0);
  
  // Without useCallback: new function created every render
  // Child re-renders even if count unchanged
  const handleClick = useCallback(() => {
    console.log('Clicked');
  }, []); // Stable reference
  
  return <ExpensiveChild onClick={handleClick} />;
}

// useMemo - expensive computations
function SearchResults({ query, items }) {
  // Without useMemo: filtered on every render
  const filtered = useMemo(() => {
    return items.filter(item => item.name.includes(query));
  }, [query, items]);
  
  return <List items={filtered} />;
}
```

## 7. Explain React Context and when to use it.

**Answer:** Context provides a way to pass data through the component tree without prop drilling.

```javascript
// Creating context
const ThemeContext = React.createContext('light');

// Provider at top level
function App() {
  const [theme, setTheme] = useState('light');
  
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <Layout />
    </ThemeContext.Provider>
  );
}

// Consumer via hook
function Button() {
  const { theme, setTheme } = useContext(ThemeContext);
  return (
    <button
      className={`btn-${theme}`}
      onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}
    >
      Toggle Theme
    </button>
  );
}
```

**When to use context:**
- Theme, localization, auth state, user preferences
- Global state that rarely changes

**When NOT to use context:**
- Frequently updating state (causes re-renders across tree)
- State only needed in a few components (use component composition)

```javascript
// Performance optimization - split contexts
const UserContext = React.createContext();
const ThemeContext = React.createContext();

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <UserContext.Provider value={{ name: 'Alice' }}>
        <Layout />
      </UserContext.Provider>
    </ThemeContext.Provider>
  );
}
```

## 8. How does React.memo and PureComponent work?

**Answer:**

```javascript
// React.memo - functional component optimization
const ExpensiveComponent = React.memo(function Expensive({ data, onClick }) {
  console.log('Rendered');
  return <div onClick={onClick}>{data.name}</div>;
});

// Only re-renders if props change (shallow comparison)

// Custom comparison function
const Component = React.memo(
  (props) => <div>{props.data.id}</div>,
  (prevProps, nextProps) => {
    return prevProps.data.id === nextProps.data.id;
  }
);

// Class component equivalent - PureComponent
class MyComponent extends React.PureComponent {
  render() {
    return <div>{this.props.name}</div>;
  }
}

// When memoization breaks:
function Parent() {
  const [count, setCount] = useState(0);
  
  // New object created every render - memo fails
  // Need useMemo/useCallback
  const data = useMemo(() => ({ name: 'Alice' }), []);
  const onClick = useCallback(() => {}, []);
  
  return <ExpensiveComponent data={data} onClick={onClick} />;
}
```

## 9. Explain the useReducer hook.

**Answer:**

```javascript
// Similar to useState but for complex state logic

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'set':
      return { count: action.payload };
    case 'reset':
      return { count: 0 };
    default:
      throw new Error('Unknown action');
  }
}

function Counter({ initialCount = 0 }) {
  const [state, dispatch] = useReducer(reducer, { count: initialCount });
  
  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}
```

**When to use useReducer vs useState:**

| Aspect | useState | useReducer |
|--------|----------|------------|
| State complexity | Simple values | Objects, multiple related values |
| Updates | Direct value/setter | Dispatched actions |
| Logic location | In component | In reducer (pure function) |
| Testing | Harder | Easy (pure function) |
| Next state depends on | Previous state or new | Previous state + action type |

**Lazy initialization:**
```javascript
const [state, dispatch] = useReducer(reducer, null, () => {
  return { count: parseInt(localStorage.getItem('count') || '0') };
});
```

## 10. What is the difference between controlled and uncontrolled components?

**Answer:**

```javascript
// Controlled component - React manages the state
function ControlledInput() {
  const [value, setValue] = useState('');
  
  return (
    <input
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
  // Every keystroke updates React state -> updates input
}

// Uncontrolled component - DOM manages the state
function UncontrolledInput() {
  const inputRef = useRef(null);
  
  function handleSubmit() {
    console.log(inputRef.current.value);
  }
  
  return (
    <input ref={inputRef} defaultValue="initial" />
  );
  // DOM manages value internally
}
```

| Aspect | Controlled | Uncontrolled |
|--------|-----------|-------------|
| State location | React state | DOM element |
| Source of truth | Component state | ref |
| Value access | state variable | ref.current.value |
| Real-time validation | Easy (on each change) | Harder (on submit) |
| Instant feedback | Yes | No |

**Hybrid approach:**
```javascript
function HybridInput() {
  const [value, setValue] = useState('');
  const [isDirty, setIsDirty] = useState(false);
  
  return (
    <input
      value={value}
      onChange={(e) => {
        setValue(e.target.value);
        setIsDirty(true);
      }}
    />
  );
}
```

## 11. How do you handle forms in React?

**Answer:**

```javascript
// Basic form handling
function RegistrationForm() {
  const [form, setForm] = useState({
    username: '',
    email: '',
    password: ''
  });
  const [errors, setErrors] = useState({});
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
    // Clear error on change
    if (errors[name]) setErrors(prev => ({ ...prev, [name]: '' }));
  };
  
  const validate = () => {
    const newErrors = {};
    if (!form.username) newErrors.username = 'Username required';
    if (!form.email.includes('@')) newErrors.email = 'Invalid email';
    if (form.password.length < 6) newErrors.password = 'Min 6 characters';
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (!validate()) return;
    // Submit form
    console.log(form);
  };
  
  return (
    <form onSubmit={handleSubmit} noValidate>
      <input name="username" value={form.username} onChange={handleChange} />
      {errors.username && <span className="error">{errors.username}</span>}
      
      <input name="email" type="email" value={form.email} onChange={handleChange} />
      {errors.email && <span className="error">{errors.email}</span>}
      
      <input name="password" type="password" value={form.password} onChange={handleChange} />
      {errors.password && <span className="error">{errors.password}</span>}
      
      <button type="submit">Register</button>
    </form>
  );
}

// UseForm custom hook
function useForm(initialValues, validate) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };
  
  const handleBlur = (e) => {
    const { name } = e.target;
    setTouched(prev => ({ ...prev, [name]: true }));
    if (validate) {
      const validationErrors = validate(values);
      setErrors(validationErrors);
    }
  };
  
  const handleSubmit = (callback) => (e) => {
    e.preventDefault();
    const validationErrors = validate ? validate(values) : {};
    setErrors(validationErrors);
    if (Object.keys(validationErrors).length === 0) {
      callback(values);
    }
  };
  
  return { values, errors, touched, handleChange, handleBlur, handleSubmit };
}
```

## 12. Explain React fiber architecture.

**Answer:** React Fiber is the reconciliation engine that enables incremental rendering, splitting work into chunks.

**Key features:**
1. **Incremental rendering:** Split rendering work into units (fibers)
2. **Pause/Resume:** Can pause work, process high-priority updates, then resume
3. **Priority-based:** Different updates have different priorities (user input > animation > data fetch)
4. **Concurrent mode:** Multiple versions of UI can exist simultaneously

**Fiber node structure:**
```javascript
// Simplified fiber node
const fiberNode = {
  tag: HostComponent,       // Type of fiber (function, class, host, etc.)
  type: 'div',              // The actual component/HTML element
  key: null,                // Key from React elements
  stateNode: divElement,    // Reference to actual DOM node
  
  // Tree relationships
  child: childFiber,        // First child
  sibling: siblingFiber,    // Next sibling
  return: parentFiber,      // Parent
  
  // Work
  memoizedState: {},        // State/ hooks
  memoizedProps: {},        // Props from last render
  pendingProps: {},         // New props to reconcile
  effectTag: 'UPDATE',      // Side effect type
  nextEffect: null,         // Next fiber with effect
  alternate: currentFiber,  // Corresponding fiber from previous tree
};
```

**Work loop:**
```javascript
// Simplified work loop
function workLoop(deadline) {
  let shouldYield = false;
  
  while (nextUnitOfWork && !shouldYield) {
    nextUnitOfWork = performUnitOfWork(nextUnitOfWork);
    shouldYield = deadline.timeRemaining() < 1;
  }
  
  if (!nextUnitOfWork) {
    commitRoot(); // Apply all changes to DOM
  }
  
  requestIdleCallback(workLoop);
}
```

## 13. How does the `key` prop work and why is it important?

**Answer:** The `key` prop helps React identify which items in a list have changed, been added, or removed.

```javascript
// Without keys - inefficient
{items.map(item => <ListItem item={item} />)}
// React has to re-render all items if order changes

// With keys - efficient
{items.map(item => <ListItem key={item.id} item={item} />)}
// React tracks each item by identity

// When key changes, component is unmounted and remounted
{users.map(user => <UserProfile key={user.id} user={user} />)}

// Key should be stable, unique, and predictable
// DON'T use array index (unless list is static and never reordered)
{items.map((item, index) => <Item key={index} item={item} />)}
// Problem: if items reorder, React reuses wrong component instances
// Problem: if items are filtered, indices change

// Key for component reset
<Counter key={userId} /> // Counter recreated when userId changes
```

**Common pattern - key for resetting state:**
```javascript
// Reset form when user changes
function UserForm({ userId }) {
  return (
    <Form key={userId}>
      <input name="name" />
    </Form>
  );
}
```

## 14. Explain error boundaries.

**Answer:** Error boundaries catch JavaScript errors in their child component tree, log those errors, and display a fallback UI.

```javascript
// Class component error boundary
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Error caught:', error, errorInfo);
    // Log to error reporting service
    logErrorToService(error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div className="error-fallback">
          <h2>Something went wrong</h2>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary fallback={<ErrorFallback />}>
  <UserProfile />
</ErrorBoundary>

// Error boundaries do NOT catch:
// 1. Event handlers (use try/catch instead)
// 2. Async code (setTimeout, requestAnimationFrame)
// 3. Server-side rendering
// 4. Errors in the error boundary itself
```

**With React Router:**
```javascript
<Routes>
  <Route path="/users" element={
    <ErrorBoundary key={location.pathname}>
      <Users />
    </ErrorBoundary>
  } />
</Routes>
```

## 15. How do you handle code splitting in React?

**Answer:**

```javascript
// React.lazy + Suspense for component-level code splitting
const UserDashboard = React.lazy(() => import('./UserDashboard'));
const AdminPanel = React.lazy(() => import('./AdminPanel'));

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/dashboard" element={<UserDashboard />} />
        <Route path="/admin" element={<AdminPanel />} />
      </Routes>
    </Suspense>
  );
}

// With explicit error handling
function App() {
  return (
    <ErrorBoundary>
      <Suspense fallback={<LoadingSpinner />}>
        <Routes>
          <Route path="/profile" element={
            <Suspense fallback={<ProfileSkeleton />}>
              <Profile />
            </Suspense>
          } />
        </Routes>
      </Suspense>
    </ErrorBoundary>
  );
}

// Dynamic imports for non-component modules
function handleExport() {
  import('./exportUtils').then(({ exportToPDF }) => {
    exportToPDF(data);
  });
}

// Webpack magic comments for chunk naming
const Dashboard = React.lazy(() => import(/* webpackChunkName: "dashboard" */ './Dashboard'));

// Route-based code splitting (common pattern)
const routes = [
  { path: '/', component: React.lazy(() => import('./Home')) },
  { path: '/about', component: React.lazy(() => import('./About')) },
  { path: '/contact', component: React.lazy(() => import('./Contact')) },
];
```

## 16. Explain the useRef hook in detail.

**Answer:**

```javascript
function UseRefExamples() {
  // 1. DOM reference
  const inputRef = useRef(null);
  
  const focusInput = () => {
    inputRef.current?.focus();
  };
  
  // 2. Mutable value (survives re-renders, no re-render on change)
  const renderCount = useRef(0);
  renderCount.current += 1;
  
  // 3. Storing previous value
  const [count, setCount] = useState(0);
  const prevCount = useRef(count);
  
  useEffect(() => {
    prevCount.current = count;
  }, [count]);
  
  // 4. Storing timer ID
  const timerRef = useRef(null);
  
  const startTimer = () => {
    timerRef.current = setInterval(() => {}, 1000);
  };
  
  const stopTimer = () => {
    clearInterval(timerRef.current);
  };
  
  // 5. Callback ref - re-run when ref changes
  const measureRef = useCallback((node) => {
    if (node) {
      node.getBoundingClientRect(); // Measure on mount
    }
  }, []);
  
  // 6. Forward ref (to child)
  return (
    <>
      <input ref={inputRef} />
      <button onClick={focusInput}>Focus Input</button>
      <div ref={measureRef}>Measurable element</div>
    </>
  );
}

// Forward ref example
const FancyInput = React.forwardRef((props, ref) => {
  return <input ref={ref} className="fancy-input" {...props} />;
});
```

## 17. How does React handle events (synthetic events)?

**Answer:** React uses a synthetic event system that wraps native browser events for cross-browser compatibility.

```javascript
// Event delegation - React attaches events to root, not individual elements
// React 17+: event listeners on root container
// React <17: event listeners on document

// Synthetic event pooling
function handleClick(e) {
  // e is a SyntheticEvent wrapper
  console.log(e.type); // 'click'
  console.log(e.nativeEvent); // Original browser event
  
  // In React <17, properties are null after handler returns
  // setTimeout(() => console.log(e.type), 0); // null (before React 17)
  // e.persist(); // Needed in React <17 for async access
  
  // React 17+: no pooling, can access async
}

// Event types
function EventExamples() {
  return (
    <div>
      {/* Mouse events */}
      <div onClick={handleClick} onMouseEnter={handleMouseEnter} onMouseLeave={handleMouseLeave}>
        Hover me
      </div>
      
      {/* Keyboard events */}
      <input onKeyDown={handleKeyDown} onKeyUp={handleKeyUp} />
      
      {/* Form events */}
      <form onSubmit={handleSubmit}>
        <input onChange={handleChange} onBlur={handleBlur} onFocus={handleFocus} />
      </form>
      
      {/* Scroll / Wheel */}
      <div onScroll={handleScroll} onWheel={handleWheel} />
      
      {/* Clipboard */}
      <input onCopy={handleCopy} onPaste={handlePaste} />
      
      {/* Touch */}
      <div onTouchStart={handleTouchStart} onTouchMove={handleTouchMove} onTouchEnd={handleTouchEnd} />
    </div>
  );
}
```

**Custom events:**
```javascript
// Using ref for custom event listeners
function CustomEventListener() {
  const ref = useRef(null);
  
  useEffect(() => {
    const element = ref.current;
    element.addEventListener('custom-event', handleCustomEvent);
    return () => element.removeEventListener('custom-event', handleCustomEvent);
  }, []);
  
  return <div ref={ref} />;
}
```

## 18. What is the difference between `useState` and `useRef`?

**Answer:**

```javascript
// useState - triggers re-render when value changes
function StateExample() {
  const [count, setCount] = useState(0);
  // Changing count re-renders component
  return <div>{count}</div>;
}

// useRef - does NOT trigger re-render when value changes
function RefExample() {
  const countRef = useRef(0);
  // Changing countRef.current does NOT re-render
  
  const increment = () => {
    countRef.current += 1;
    console.log(countRef.current); // Updated but no re-render
  };
  
  return <div>{countRef.current}</div>; // Won't update visually!
}
```

| Aspect | useState | useRef |
|--------|----------|--------|
| Triggers re-render | Yes | No |
| Returns | [value, setter] | { current: initial } |
| Reads | Returns current value | .current property |
| Writes | Via setter function | Direct mutation (.current = new) |
| Use case | UI state, form inputs | DOM refs, instance variables, timers |

**When to use useRef over useState:**
- Tracking render count/previous values
- Storing timer/interval IDs
- Mutable values that shouldn't cause re-renders
- DOM element references

## 19. Explain React's StrictMode.

**Answer:** StrictMode is a development-only tool that helps identify potential problems.

```javascript
import { StrictMode } from 'react';

function App() {
  return (
    <StrictMode>
      <Root />
    </StrictMode>
  );
}
```

**What StrictMode checks:**
1. **Detecting unsafe lifecycles** - identifies components using legacy lifecycle methods
2. **Warning about legacy string ref API**
3. **Detecting unexpected side effects** - double-invokes effects, constructors, and render functions to find bugs
4. **Detecting legacy context API**
5. **Ensuring reusable state** (React 18+) - simulates mounting/unmounting to test state persistence

```javascript
// StrictMode side effect detection - runs twice in development
function Effects() {
  useEffect(() => {
    console.log('Effect runs');
    return () => console.log('Cleanup runs');
    // In StrictMode: runs, cleanup, runs again
    // Helps catch missing cleanup
  }, []);
}

// Also double-invokes:
// - useState initializer
// - useReducer reducer
// - Component constructor
// - render function
```

## 20. How does React 18's automatic batching work?

**Answer:**

```javascript
// Before React 18 - batching only in React event handlers
function OldBatching() {
  const handleClick = () => {
    setCount(c => c + 1);
    setFlag(f => !f);
    // Single re-render (batched)
  };
  
  // setTimeout: NO batching
  setTimeout(() => {
    setCount(c => c + 1); // Render
    setFlag(f => !f);     // Another render
  }, 1000);
  
  // Promise: NO batching
  fetch('/data').then(() => {
    setCount(c => c + 1); // Render
    setFlag(f => !f);     // Another render
  });
}

// React 18+ - automatic batching everywhere
function NewBatching() {
  const handleClick = () => {
    setCount(c => c + 1);
    setFlag(f => !f);
    // Single re-render
  };
  
  // setTimeout: BATCHED
  setTimeout(() => {
    setCount(c => c + 1);
    setFlag(f => !f);
    // Single re-render
  }, 1000);
  
  // Promise: BATCHED
  fetch('/data').then(() => {
    setCount(c => c + 1);
    setFlag(f => !f);
    // Single re-render
  });
  
  // Native events: BATCHED
  useEffect(() => {
    element.addEventListener('click', () => {
      setCount(c => c + 1);
      setFlag(f => !f);
      // Single re-render
    });
  }, []);
  
  // Opt-out of batching using flushSync
  const { flushSync } = require('react-dom');
  flushSync(() => setCount(c => c + 1)); // Immediate render
  flushSync(() => setFlag(f => !f));     // Second render
}
```

## 21. What are portals in React?

**Answer:** Portals render children into a different DOM subtree outside the parent component.

```javascript
import { createPortal } from 'react-dom';

function Modal({ children, onClose }) {
  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        {children}
      </div>
    </div>,
    document.getElementById('modal-root')
  );
}

// Usage
function App() {
  const [showModal, setShowModal] = useState(false);
  
  return (
    <div>
      <button onClick={() => setShowModal(true)}>Open Modal</button>
      {showModal && (
        <Modal onClose={() => setShowModal(false)}>
          <h2>Modal Title</h2>
          <p>This renders in #modal-root but events bubble through React tree</p>
        </Modal>
      )}
    </div>
  );
}
```

**Use cases for portals:**
- Modals, dialogs
- Tooltips, popovers
- Dropdown menus
- Toast notifications

**Important:** Event propagation follows React component tree, not DOM tree. A click event inside a portal still bubbles up through the parent React component hierarchy.

```javascript
// Portal example - tooltip
function Tooltip({ children, targetRef }) {
  const [position, setPosition] = useState({ top: 0, left: 0 });
  
  useLayoutEffect(() => {
    if (targetRef.current) {
      const rect = targetRef.current.getBoundingClientRect();
      setPosition({ top: rect.bottom + 8, left: rect.left });
    }
  }, [targetRef]);
  
  return createPortal(
    <div className="tooltip" style={{ position: 'absolute', ...position }}>
      {children}
    </div>,
    document.body
  );
}
```

## 22. Explain the `useTransition` and `useDeferredValue` hooks.

**Answer:**

```javascript
// useTransition - mark state update as non-urgent
function SearchPage() {
  const [query, setQuery] = useState('');
  const [isPending, startTransition] = useTransition();
  const [results, setResults] = useState([]);
  
  const handleChange = (e) => {
    // Urgent: update input value immediately
    setQuery(e.target.value);
    
    // Transition: defer results rendering for better UX
    startTransition(() => {
      setResults(searchItems(e.target.value));
    });
  };
  
  return (
    <div>
      <input value={query} onChange={handleChange} />
      {isPending && <Spinner />}
      <ResultsList results={results} />
    </div>
  );
}

// useDeferredValue - defer a value (similar to debouncing)
function SearchResults({ query }) {
  const deferredQuery = useDeferredValue(query);
  const isPending = query !== deferredQuery;
  
  const results = useMemo(() => {
    return searchItems(deferredQuery);
  }, [deferredQuery]);
  
  return (
    <div>
      {isPending && <LoadingIndicator />}
      <List items={results} />
    </div>
  );
}
```

**Key differences:**
| Aspect | useTransition | useDeferredValue |
|--------|---------------|------------------|
| Trigger | State update function | Value (usually controlled) |
| Control | You decide what's urgent | Value is automatically deferred |
| Visual pending | isPending flag | Compare value vs deferred |

## 23. How does React 18's Suspense work with data fetching?

**Answer:**

```javascript
// Suspense enables declarative loading states for data fetching

// Component with data fetching
function UserProfile() {
  // Data fetched via Suspense-compatible framework (Relay, SWR, React Query)
  const user = use(fetchUser(1));
  return <div>{user.name}</div>;
}

// Parent with Suspense boundary
function ProfilePage() {
  return (
    <Suspense fallback={<ProfileSkeleton />}>
      <UserProfile />
    </Suspense>
  );
}

// Nested Suspense for progressive loading
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      
      <Suspense fallback={<HeaderSkeleton />}>
        <ProfileHeader />
      </Suspense>
      
      <Suspense fallback={<StatsSkeleton />}>
        <UserStats />
      </Suspense>
      
      <Suspense fallback={<ActivitySkeleton />}>
        <RecentActivity />
      </Suspense>
    </div>
  );
}

// Transition with Suspense
function Tabs() {
  const [tab, setTab] = useState('profile');
  const [isPending, startTransition] = useTransition();
  
  return (
    <div>
      <button onClick={() => startTransition(() => setTab('profile'))}>
        Profile
      </button>
      <button onClick={() => startTransition(() => setTab('settings'))}>
        Settings
      </button>
      
      {isPending && <div>Loading...</div>}
      
      <Suspense fallback={<Spinner />}>
        {tab === 'profile' ? <Profile /> : <Settings />}
      </Suspense>
    </div>
  );
}
```

## 24. What are custom hooks and how do you create them?

**Answer:**

```javascript
// Custom hook - reusable logic extracted from components

// 1. useLocalStorage - persist state to localStorage
function useLocalStorage(key, initialValue) {
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });
  
  const setValue = (value) => {
    const valueToStore = value instanceof Function ? value(storedValue) : value;
    setStoredValue(valueToStore);
    window.localStorage.setItem(key, JSON.stringify(valueToStore));
  };
  
  return [storedValue, setValue];
}

// 2. useDebounce - debounce a value
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  
  return debouncedValue;
}

// 3. useFetch - data fetching hook
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    let cancelled = false;
    
    async function fetchData() {
      setLoading(true);
      setError(null);
      
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error('Network error');
        const json = await response.json();
        if (!cancelled) setData(json);
      } catch (err) {
        if (!cancelled) setError(err);
      } finally {
        if (!cancelled) setLoading(false);
      }
    }
    
    fetchData();
    return () => { cancelled = true; };
  }, [url]);
  
  return { data, loading, error };
}

// 4. useMediaQuery - responsive hook
function useMediaQuery(query) {
  const [matches, setMatches] = useState(() => window.matchMedia(query).matches);
  
  useEffect(() => {
    const mediaQuery = window.matchMedia(query);
    const handler = (e) => setMatches(e.matches);
    mediaQuery.addEventListener('change', handler);
    return () => mediaQuery.removeEventListener('change', handler);
  }, [query]);
  
  return matches;
}

// Usage in component
function SearchComponent() {
  const [searchTerm, setSearchTerm] = useLocalStorage('search', '');
  const debouncedTerm = useDebounce(searchTerm, 300);
  const { data, loading } = useFetch(`/api/search?q=${debouncedTerm}`);
  const isMobile = useMediaQuery('(max-width: 768px)');
  
  return (
    <div className={isMobile ? 'mobile' : 'desktop'}>
      <input value={searchTerm} onChange={(e) => setSearchTerm(e.target.value)} />
      {loading ? <Spinner /> : <Results data={data} />}
    </div>
  );
}
```

**Custom hook conventions:**
- Start with `use` prefix
- Can call other hooks inside
- Should be pure (no side effects without useEffect)
- Return values that components need (avoid returning unnecessary state)

## 25. How does React's reconciliation work with lists?

**Answer:**

```javascript
// Key importance in lists

// Without keys (index as key) - problematic
function ListWithoutKeys({ items }) {
  return (
    <ul>
      {items.map((item, index) => (
        <ListItem key={index} item={item} />
      ))}
    </ul>
  );
}

// With stable keys - efficient
function ListWithKeys({ items }) {
  return (
    <ul>
      {items.map(item => (
        <ListItem key={item.id} item={item} />
      ))}
    </ul>
  );
}
```

**Reconciliation scenarios:**

```javascript
// Scenario 1: Item added at end
// Before: [A, B, C]
// After:  [A, B, C, D]
// With keys: React matches A,B,C, inserts D at end (efficient)

// Scenario 2: Item inserted at beginning
// Before: [A, B, C]
// After:  [D, A, B, C]
// With index keys: Each component sees new data and re-renders all
// With stable keys: React identifies A,B,C moved down, inserts D at start

// Scenario 3: Items removed
// Before: [A, B, C]
// After:  [A, C]
// With stable keys: React unmounts B, keeps A and C

// Scenario 4: Reordering
// Before: [A, B, C]
// After:  [C, A, B]
// With stable keys: React moves DOM nodes (doesn't recreate)
```

**Impact of index as key:**
```javascript
// Problem: index key with filtering
const [filter, setFilter] = useState('');
const filtered = users.filter(u => u.name.includes(filter));

// Each render, indices change -> React loses track of component state
// If ListItem has input, input state is lost on reorder
filtered.map((user, index) => <ListItem key={index} user={user} />);

// Fix: use unique id
filtered.map(user => <ListItem key={user.id} user={user} />);
```

## 26. Explain the render props pattern.

**Answer:**

```javascript
// Render props - component that takes a function as its children (or render prop)

// Mouse tracker using render props
class MouseTracker extends React.Component {
  state = { x: 0, y: 0 };
  
  handleMouseMove = (e) => {
    this.setState({ x: e.clientX, y: e.clientY });
  };
  
  render() {
    return (
      <div onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
        {/* or: this.props.children(this.state) */}
      </div>
    );
  }
}

// Usage
function App() {
  return (
    <MouseTracker render={({ x, y }) => (
      <h1>Mouse position: {x}, {y}</h1>
    )} />
  );
}

// Modern equivalent with hooks
function useMousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  useEffect(() => {
    const handler = (e) => setPosition({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handler);
    return () => window.removeEventListener('mousemove', handler);
  }, []);
  
  return position;
}

// Data provider with render props
function DataProvider({ url, children }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);
  
  return children({ data, loading, error });
}

// Usage
<DataProvider url="/api/users">
  {({ data, loading, error }) => {
    if (loading) return <Spinner />;
    if (error) return <Error error={error} />;
    return <UserList users={data} />;
  }}
</DataProvider>
```

## 27. How does context-based state management work (useContext + useReducer)?

**Answer:**

```javascript
// Simple Redux-like state management with context + reducer

// Store
const StoreContext = React.createContext();
const DispatchContext = React.createContext();

// Reducer
function appReducer(state, action) {
  switch (action.type) {
    case 'SET_USER':
      return { ...state, user: action.payload };
    case 'ADD_TODO':
      return { ...state, todos: [...state.todos, action.payload] };
    case 'TOGGLE_TODO':
      return {
        ...state,
        todos: state.todos.map(todo =>
          todo.id === action.payload ? { ...todo, done: !todo.done } : todo
        )
      };
    default:
      return state;
  }
}

// Provider
function StoreProvider({ children }) {
  const [state, dispatch] = useReducer(appReducer, {
    user: null,
    todos: [],
    theme: 'light'
  });
  
  return (
    <StoreContext.Provider value={state}>
      <DispatchContext.Provider value={dispatch}>
        {children}
      </DispatchContext.Provider>
    </StoreContext.Provider>
  );
}

// Custom hooks
function useStore() {
  return useContext(StoreContext);
}

function useDispatch() {
  return useContext(DispatchContext);
}

// Action creators
function useActions() {
  const dispatch = useDispatch();
  
  return useMemo(() => ({
    setUser: (user) => dispatch({ type: 'SET_USER', payload: user }),
    addTodo: (todo) => dispatch({ type: 'ADD_TODO', payload: todo }),
    toggleTodo: (id) => dispatch({ type: 'TOGGLE_TODO', payload: id }),
  }), [dispatch]);
}

// Components
function TodoList() {
  const { todos } = useStore();
  const { toggleTodo, addTodo } = useActions();
  const [text, setText] = useState('');
  
  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button onClick={() => { addTodo({ id: Date.now(), text, done: false }); setText(''); }}>
        Add
      </button>
      <ul>
        {todos.map(todo => (
          <li key={todo.id} onClick={() => toggleTodo(todo.id)}>
            {todo.done ? <s>{todo.text}</s> : todo.text}
          </li>
        ))}
      </ul>
    </div>
  );
}

// Usage
function App() {
  return (
    <StoreProvider>
      <TodoList />
    </StoreProvider>
  );
}
```

## 28. How do you optimize React performance?

**Answer:**

```javascript
// 1. React.memo - prevent unnecessary re-renders
const ExpensiveList = React.memo(({ items }) => {
  return items.map(/* ... */);
});

// 2. useMemo - memoize computed values
function FilteredList({ items, filter }) {
  const filtered = useMemo(() => {
    return items.filter(item => item.name.includes(filter));
  }, [items, filter]);
  
  return <List items={filtered} />;
}

// 3. useCallback - memoize callbacks
function Parent() {
  const [count, setCount] = useState(0);
  
  const handleClick = useCallback(() => {
    setCount(c => c + 1);
  }, []);
  
  return <Child onClick={handleClick} />;
}

// 4. Virtualization for long lists
import { FixedSizeList } from 'react-window';

function VirtualList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>{items[index].name}</div>
  );
  
  return (
    <FixedSizeList
      height={400}
      itemCount={items.length}
      itemSize={50}
    >
      {Row}
    </FixedSizeList>
  );
}

// 5. Code splitting with lazy
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

// 6. Avoid unnecessary re-renders
function Child({ data }) {
  // If data is object inline, useMemo in parent
  return <Expensive data={data} />;
}

// 7. Use key properly
{items.map(item => <Item key={item.id} item={item} />)}

// 8. Debounce/throttle expensive handlers
const debouncedSearch = useCallback(
  debounce((value) => search(value), 300),
  []
);

// 9. Avoid creating new objects/arrays in render
function Parent() {
  // BAD: creates new object each render
  return <Child config={{ theme: 'dark', layout: 'grid' }} />;
  
  // GOOD: use useMemo
  const config = useMemo(() => ({ theme: 'dark', layout: 'grid' }), []);
  return <Child config={config} />;
}
```

## 29. Explain compound components pattern.

**Answer:**

```javascript
// Compound components - set of components that work together implicitly sharing state

// Accordion component
const AccordionContext = React.createContext();

function Accordion({ children, defaultIndex = 0 }) {
  const [activeIndex, setActiveIndex] = useState(defaultIndex);
  
  const toggle = useCallback((index) => {
    setActiveIndex(prev => prev === index ? null : index);
  }, []);
  
  return (
    <AccordionContext.Provider value={{ activeIndex, toggle }}>
      <div className="accordion">{children}</div>
    </AccordionContext.Provider>
  );
}

Accordion.Item = function AccordionItem({ index, children }) {
  const { activeIndex, toggle } = useContext(AccordionContext);
  
  return (
    <div className="accordion-item">
      {React.Children.map(children, child => {
        return React.cloneElement(child, { index, activeIndex, toggle });
      })}
    </div>
  );
};

Accordion.Header = function AccordionHeader({ index, activeIndex, toggle, children }) {
  return (
    <button
      className={`accordion-header ${activeIndex === index ? 'active' : ''}`}
      onClick={() => toggle(index)}
    >
      {children}
    </button>
  );
};

Accordion.Panel = function AccordionPanel({ index, activeIndex, children }) {
  return activeIndex === index ? (
    <div className="accordion-panel">{children}</div>
  ) : null;
};

// Usage
function App() {
  return (
    <Accordion defaultIndex={0}>
      <Accordion.Item index={0}>
        <Accordion.Header>Section 1</Accordion.Header>
        <Accordion.Panel>Content 1</Accordion.Panel>
      </Accordion.Item>
      <Accordion.Item index={1}>
        <Accordion.Header>Section 2</Accordion.Header>
        <Accordion.Panel>Content 2</Accordion.Panel>
      </Accordion.Item>
    </Accordion>
  );
}
```

## 30. What is the difference between `useEffect` with cleanup and without?

**Answer:**

```javascript
// Without cleanup - runs effect, no cleanup on unmount/update
useEffect(() => {
  document.title = `Count: ${count}`;
  // No cleanup needed - just setting document title
}, [count]);

// With cleanup - runs effect, runs cleanup before re-run and on unmount
useEffect(() => {
  const subscription = subscribeToEvent(data);
  
  return () => {
    // Cleanup: runs when:
    // 1. Component unmounts
    // 2. Before re-running effect (if deps change)
    subscription.unsubscribe();
  };
}, [data]);

// Common cleanup patterns:
// 1. Event listeners
useEffect(() => {
  window.addEventListener('resize', handleResize);
  return () => window.removeEventListener('resize', handleResize);
}, []);

// 2. Timers
useEffect(() => {
  const timer = setInterval(tick, 1000);
  return () => clearInterval(timer);
}, []);

// 3. Abort fetch
useEffect(() => {
  const controller = new AbortController();
  
  fetch(url, { signal: controller.signal })
    .then(res => res.json())
    .then(setData)
    .catch(err => {
      if (err.name !== 'AbortError') setError(err);
    });
  
  return () => controller.abort();
}, [url]);

// 4. WebSocket
useEffect(() => {
  const ws = new WebSocket(url);
  ws.onmessage = (e) => setMessages(prev => [...prev, e.data]);
  return () => ws.close();
}, [url]);
```

## 31. Explain server components (RSC) in React.

**Answer:**

```javascript
// Server Components - components that run on the server, never on the client
// They reduce bundle size by keeping heavy dependencies on server

// ServerComponent.server.js - runs on server
async function ProductList() {
  // Can directly use async/await (no useEffect needed)
  const products = await db.query('SELECT * FROM products');
  
  return (
    <ul>
      {products.map(product => (
        <li key={product.id}>
          {product.name} - ${product.price}
          {/* Server component can include client components */}
          <AddToCartButton productId={product.id} />
        </li>
      ))}
    </ul>
  );
}

// ClientComponent.client.js - runs on client (has interactivity)
'use client';

function AddToCartButton({ productId }) {
  const [added, setAdded] = useState(false);
  
  return (
    <button onClick={() => {
      addToCart(productId);
      setAdded(true);
    }}>
      {added ? 'Added!' : 'Add to Cart'}
    </button>
  );
}

// Server Component rules:
// - Cannot use hooks (useState, useEffect, etc.)
// - Cannot use browser APIs
// - Can be async
// - Can access databases, file system, etc.
// - Can import client components (but not vice versa)

// Client Component rules:
// - Need 'use client' directive
// - Can use all React features
// - Larger bundle (all dependencies included)
// - Cannot import server components
```

## 32. How does React handle reconciliation with fragments?

**Answer:**

```javascript
// Fragments allow returning multiple elements without extra DOM node

function Table() {
  return (
    <table>
      <tr>
        <Columns />
      </tr>
    </table>
  );
}

// Without fragments - invalid HTML (div inside tr)
function Columns() {
  return (
    <div>
      <td>Column 1</td>
      <td>Column 2</td>
    </div>
  );
}

// With fragments - correct HTML
function Columns() {
  return (
    <>
      <td>Column 1</td>
      <td>Column 2</td>
    </>
  );
}

// Explicit Fragment with key
function Glossary({ items }) {
  return (
    <dl>
      {items.map(item => (
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}

// Fragment reconciliation:
// - Fragment children are reconciled directly with parent's children
// - Does not create extra DOM nodes
// - Can have keys when creating fragments in lists
```

## 33. Explain testing strategies for React components.

**Answer:**

```javascript
// 1. Unit Testing Components (Testing Library)
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('counter increments when button clicked', async () => {
  render(<Counter initialCount={0} />);
  
  const button = screen.getByRole('button', { name: /increment/i });
  await userEvent.click(button);
  
  expect(screen.getByText('1')).toBeInTheDocument();
});

// 2. Testing with mock data
test('displays user data', async () => {
  const mockUser = { id: 1, name: 'Alice' };
  jest.spyOn(api, 'fetchUser').mockResolvedValue(mockUser);
  
  render(<UserProfile userId={1} />);
  
  await waitFor(() => {
    expect(screen.getByText('Alice')).toBeInTheDocument();
  });
});

// 3. Testing user interactions
test('form validation shows errors', async () => {
  render(<SignupForm />);
  
  const submitButton = screen.getByRole('button', { name: /submit/i });
  await userEvent.click(submitButton);
  
  expect(screen.getByText(/email is required/i)).toBeInTheDocument();
});

// 4. Testing accessibility
test('component has accessible label', () => {
  render(<InputWithLabel label="Username" />);
  expect(screen.getByLabelText('Username')).toBeInTheDocument();
});

// 5. Integration testing
test('complete checkout flow', async () => {
  render(<Checkout />);
  
  // Add item to cart
  await userEvent.click(screen.getByText('Add to Cart'));
  
  // Proceed to checkout
  await userEvent.click(screen.getByText('Checkout'));
  
  // Fill form
  await userEvent.type(screen.getByLabelText('Email'), 'user@test.com');
  await userEvent.click(screen.getByText('Place Order'));
  
  await waitFor(() => {
    expect(screen.getByText('Order Confirmed')).toBeInTheDocument();
  });
});
```

## 34. What is the `useDebugValue` hook?

**Answer:**

```javascript
// useDebugValue - adds labels to custom hooks in React DevTools

function useFriendStatus(friendId) {
  const [isOnline, setIsOnline] = useState(null);
  
  useDebugValue(isOnline ? 'Online' : 'Offline');
  // Or with formatter:
  useDebugValue(friendId, (id) => `Friend ${id} status`);
  
  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }
    ChatAPI.subscribeToFriendStatus(friendId, handleStatusChange);
    return () => ChatAPI.unsubscribeFromFriendStatus(friendId, handleStatusChange);
  }, [friendId]);
  
  return isOnline;
}

// The label appears in React DevTools when inspecting the component
```

## 35. Explain React 18's concurrent features and `startTransition`.

**Answer:**

```javascript
// Concurrent features allow React to interrupt rendering for higher priority work

// 1. startTransition - mark low-priority updates
function SearchPage() {
  const [query, setQuery] = useState('');
  const [searchResults, setSearchResults] = useState([]);
  const [isPending, startTransition] = useTransition();
  
  function handleChange(e) {
    // URGENT: keep input responsive
    setQuery(e.target.value);
    
    // NON-URGENT: can be interrupted
    startTransition(() => {
      setSearchResults(search(e.target.value));
    });
  }
  
  return (
    <div>
      <input value={query} onChange={handleChange} />
      {isPending ? <Spinner /> : <Results data={searchResults} />}
    </div>
  );
}

// 2. useDeferredValue - deferred version of a value
function SearchResults({ query }) {
  const deferredQuery = useDeferredValue(query);
  // deferredQuery lags behind query during rapid changes
  const isStale = query !== deferredQuery;
  
  const results = useMemo(() => {
    return heavySearch(deferredQuery);
  }, [deferredQuery]);
  
  return (
    <div style={{ opacity: isStale ? 0.5 : 1 }}>
      <ResultsList results={results} />
    </div>
  );
}

// 3. Automatic batching (all updates batched)
function handleClick() {
  setCount(c => c + 1);   // Batched
  setFlag(f => !f);        // Batched
}

// 4. Suspense on the server
// Server components + Suspense for streaming HTML

// 5. Selective hydration
// Hydrate only visible content first
```
