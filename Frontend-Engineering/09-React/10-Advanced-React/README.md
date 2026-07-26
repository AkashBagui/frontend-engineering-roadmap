# Advanced React Patterns

## Higher-Order Components (HOC)

A **higher-order component** is a function that takes a component and returns a new enhanced component.

```jsx
function withLogger(WrappedComponent) {
  return function EnhancedComponent(props) {
    useEffect(() => {
      console.log(`Rendered ${WrappedComponent.name} with`, props);
    });
    return <WrappedComponent {...props} />;
  };
}

const UserWithLogger = withLogger(User);
```

### Common HOC patterns

```jsx
// Conditional rendering (auth guard)
function withAuth(WrappedComponent) {
  return function AuthenticatedComponent(props) {
    const { user } = useAuth();
    if (!user) return <Navigate to="/login" />;
    return <WrappedComponent {...props} user={user} />;
  };
}

const ProtectedPage = withAuth(Dashboard);

// Injecting data
function withUser(WrappedComponent) {
  return function UserDataComponent(props) {
    const { data: user } = useFetch('/api/user');
    if (!user) return <Spinner />;
    return <WrappedComponent {...props} user={user} />;
  };
}
```

**Problems with HOCs:** Wrapper hell, naming collisions, static property loss (use `hoist-non-react-statics`).

## Render Props

A component that accepts a **function as a prop** to dynamically render content:

```jsx
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handler = (e) => setPosition({ x: e.clientX, y: e.clientY });
    window.addEventListener('mousemove', handler);
    return () => window.removeEventListener('mousemove', handler);
  }, []);

  return render(position);
}

// Usage
<MouseTracker render={({ x, y }) => (
  <div>Mouse is at {x}, {y}</div>
)} />
```

**Problems:** Nested callbacks lead to "callback hell" — hooks are the modern replacement.

## Compound Components

Components that work together to form a cohesive unit, sharing implicit state via context:

```jsx
// Select.jsx
const SelectContext = createContext();

function Select({ children, value, onChange }) {
  const [open, setOpen] = useState(false);

  return (
    <SelectContext.Provider value={{ value, onChange, open, setOpen }}>
      <div className="select">{children}</div>
    </SelectContext.Provider>
  );
}

Select.Trigger = function Trigger({ children }) {
  const { value, open, setOpen } = useContext(SelectContext);
  return <button onClick={() => setOpen(!open)}>{children || value}</button>;
};

Select.Options = function Options({ children }) {
  const { open } = useContext(SelectContext);
  return open ? <ul className="options">{children}</ul> : null;
};

Select.Option = function Option({ value, children }) {
  const { onChange, setOpen } = useContext(SelectContext);
  return <li onClick={() => { onChange(value); setOpen(false); }}>{children}</li>;
};

// Usage
<Select value={selected} onChange={setSelected}>
  <Select.Trigger />
  <Select.Options>
    <Select.Option value="react">React</Select.Option>
    <Select.Option value="vue">Vue</Select.Option>
  </Select.Options>
</Select>
```

Compound components are excellent for building **library-quality** components with flexible APIs.

## Refs Forwarding

`forwardRef` lets parent components pass a `ref` down to a child's DOM element:

```jsx
const FancyInput = forwardRef(function FancyInput(props, ref) {
  return <input ref={ref} className="fancy" {...props} />;
});

// Parent
function Form() {
  const inputRef = useRef(null);
  useEffect(() => inputRef.current.focus(), []);
  return <FancyInput ref={inputRef} placeholder="Auto-focused" />;
}
```

## Portals

Portals render children into a DOM node **outside** the parent component's DOM hierarchy:

```jsx
import { createPortal } from 'react-dom';

function Modal({ open, children }) {
  if (!open) return null;

  return createPortal(
    <div className="modal-overlay">
      <div className="modal-content">{children}</div>
    </div>,
    document.getElementById('portal-root')
  );
}
```

### When to use Portals

- Modals, dialogs, popovers
- Tooltips, dropdowns (to avoid z-index/overflow issues)
- Toast notifications

## Error Boundaries

Error boundaries catch **rendering errors** in the component tree and display fallback UI:

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <h1>Something went wrong</h1>;
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary fallback={<ErrorScreen />}>
  <UserProfile userId={id} />
</ErrorBoundary>
```

**Limitations:** Cannot catch errors in event handlers, async code (setTimeout), SSR, or the error boundary itself.

## Suspense

Suspense lets components **wait** for something (code, data) before rendering:

```jsx
import { Suspense, lazy } from 'react';

const HeavyComponent = lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Spinner />}>
      <HeavyComponent />
    </Suspense>
  );
}
```

## Pattern Comparison

| Pattern | Type | Best for | Modern alternative |
|---------|------|----------|-------------------|
| HOC | Function wrapping | Cross-cutting concerns (auth, logging) | Custom hooks |
| Render Props | Function prop | Dynamic rendering, state sharing | Custom hooks |
| Compound Components | Context + composition | Library APIs (Select, Tabs, Menu) | Still relevant |
| forwardRef | Ref passing | DOM access in reusable components | Still relevant |
| Portals | DOM mounting | Modals, tooltips, overlays | Still relevant |
| Error Boundaries | Class component | Error catching, fallback UI | Still required |
| Suspense | Declarative loading | Code splitting, data fetching | Still relevant |

## Modern Recommendation

For most use cases, **custom hooks** are the preferred solution — they compose naturally, avoid wrapper hell, and are easy to test. Compound components remain useful for library-style APIs. Error Boundaries require class components (no hook alternative exists yet). Portals and Refs are foundational APIs without modern alternatives.
