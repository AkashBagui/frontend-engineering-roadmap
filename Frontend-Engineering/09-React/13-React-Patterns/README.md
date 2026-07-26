# React Patterns

Design patterns for building reusable, maintainable React components.

## Compound Components

Components that share implicit state via context, providing a flexible declarative API:

```jsx
// Tabs -- compound component
const TabsContext = createContext();

function Tabs({ defaultTab, children }) {
  const [activeTab, setActiveTab] = useState(defaultTab);
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

Tabs.Tab = function Tab({ label, id }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  return (
    <button
      className={activeTab === id ? 'active' : ''}
      onClick={() => setActiveTab(id)}
    >
      {label}
    </button>
  );
};

Tabs.Panel = function Panel({ id, children }) {
  const { activeTab } = useContext(TabsContext);
  return activeTab === id ? <div className="tab-panel">{children}</div> : null;
};

// Usage
<Tabs defaultTab="profile">
  <Tabs.Tab label="Profile" id="profile" />
  <Tabs.Tab label="Settings" id="settings" />
  <Tabs.Panel id="profile"><UserProfile /></Tabs.Panel>
  <Tabs.Panel id="settings"><Settings /></Tabs.Panel>
</Tabs>
```

**Best for:** Tabs, Select/Dropdown, Accordion, Stepper, Menu, Carousel.

## Controlled vs Uncontrolled Pattern

A component that works with **external state** (controlled) or **internal state** (uncontrolled):

```jsx
function Toggle({ on: controlledOn, onChange, defaultOn = false }) {
  const isControlled = controlledOn !== undefined;
  const [internalOn, setInternalOn] = useState(defaultOn);

  const on = isControlled ? controlledOn : internalOn;
  const toggle = () => {
    const newOn = !on;
    if (!isControlled) setInternalOn(newOn);
    onChange?.(newOn);
  };

  return <button onClick={toggle}>{on ? 'ON' : 'OFF'}</button>;
}

// Uncontrolled — manages its own state
<Toggle defaultOn={true} />

// Controlled — parent manages state
<Toggle on={isOn} onChange={setIsOn} />
```

**Best for:** Form inputs, Toggle, Rating, Slider, any reusable input-like component.

## Render Props

A component that uses a **function prop** to determine what to render:

```jsx
function DataList({ items, renderItem, renderEmpty }) {
  if (items.length === 0) {
    return renderEmpty?.() ?? <p>No items</p>;
  }

  return <ul>{items.map((item, i) => <li key={i}>{renderItem(item)}</li>)}</ul>;
}

// Usage
<DataList
  items={users}
  renderItem={(user) => <UserCard user={user} />}
  renderEmpty={() => <EmptyState />}
/>
```

**Best for:** Lists, tables, dropdowns — any component with dynamic rendering.

## State Reducer Pattern

Allow consumers to **override internal state logic** by providing a custom reducer:

```jsx
function useToggle({ reducer = (state, action) => action === 'toggle' ? !state : state } = {}) {
  const [on, dispatch] = useReducer(reducer, false);

  const toggle = () => dispatch('toggle');
  const setOn = () => dispatch('setOn');
  const setOff = () => dispatch('setOff');

  return { on, toggle, setOn, setOff };
}

// Consumer can override behavior
function App() {
  const { on, toggle } = useToggle({
    reducer: (state, action) => {
      switch (action) {
        case 'toggle': return !state;
        case 'setOn': return true;
        case 'setOff': return false;
        default: return state;
      }
    }
  });

  return <button onClick={toggle}>{on ? 'ON' : 'OFF'}</button>;
}
```

**Best for:** Complex interactive components where consumers need fine-grained control over behavior (Downshift, React Table).

## Provider Pattern

Provide data and functions to an entire subtree via context:

```jsx
// CounterProvider.jsx
const CounterContext = createContext();
const CounterDispatchContext = createContext();

function CounterProvider({ children, initial = 0 }) {
  const [count, dispatch] = useReducer(counterReducer, initial);

  return (
    <CounterDispatchContext.Provider value={dispatch}>
      <CounterContext.Provider value={count}>
        {children}
      </CounterContext.Provider>
    </CounterDispatchContext.Provider>
  );
}

function useCount() {
  const ctx = useContext(CounterContext);
  if (ctx === undefined) throw new Error('useCount must be used within CounterProvider');
  return ctx;
}

function useDispatch() {
  const ctx = useContext(CounterDispatchContext);
  if (ctx === undefined) throw new Error('useDispatch must be used within CounterProvider');
  return ctx;
}
// Separating read/write contexts prevents unnecessary re-renders
```

**Best for:** Global or localized state (auth, theme, cart, form state).

## Container/Presentational Pattern

Separate **logic** (container) from **rendering** (presentational):

```jsx
// Container — handles state, effects, data fetching
function UserListContainer() {
  const { data: users, loading, error } = useFetch('/api/users');
  const { toggle } = useFavorites();

  if (loading) return <UserListSkeleton />;
  if (error) return <ErrorState message={error} />;

  return <UserList users={users} onFavorite={toggle} />;
}

// Presentational — renders UI
function UserList({ users, onFavorite }) {
  return (
    <div className="grid">
      {users.map(user => (
        <UserCard key={user.id} user={user} onFavorite={onFavorite} />
      ))}
    </div>
  );
}
```

**Best for:** Any component — promotes separation of concerns naturally.

## Hooks Pattern

Extract **stateful logic** into reusable custom hooks:

```jsx
function useForm(initialValues) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const handleChange = (e) => {
    const { name, value } = e.target;
    setValues(prev => ({ ...prev, [name]: value }));
  };

  const handleBlur = (e) => {
    setTouched(prev => ({ ...prev, [e.target.name]: true }));
  };

  const reset = () => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  };

  const setFieldError = (name, error) => {
    setErrors(prev => ({ ...prev, [name]: error }));
  };

  return { values, errors, touched, handleChange, handleBlur, reset, setFieldError };
}

function LoginForm() {
  const { values, handleChange, handleSubmit } = useForm({ email: '', password: '' });
  // ...
}
```

**Best for:** Reusable logic — replaces HOCs and render props for most use cases.

## Pattern Selection Guide

| Pattern | When to use | Complexity |
|---------|------------|------------|
| **Compound Components** | Building library-quality UI (Tabs, Select, Menu) | Medium |
| **Controlled/Uncontrolled** | Reusable input-like components | Low |
| **Render Props** | Dynamic rendering decisions | Low |
| **State Reducer** | Complex interactive components with customizable behavior | High |
| **Provider** | Sharing state across many components | Low-Medium |
| **Container/Presentational** | Separating logic from UI | Low |
| **Custom Hooks** | Reusing stateful logic between components | Low |

## Real-World Example: Combining Patterns

```jsx
// Combines compound components + controlled/uncontrolled + provider
function Accordion({ defaultOpen, open: controlledOpen, onChange, children }) {
  const isControlled = controlledOpen !== undefined;
  const [internalOpen, setInternalOpen] = useState(defaultOpen ?? null);
  const open = isControlled ? controlledOpen : internalOpen;

  const setOpen = (id) => {
    const next = open === id ? null : id;
    if (!isControlled) setInternalOpen(next);
    onChange?.(next);
  };

  return (
    <AccordionContext.Provider value={{ open, setOpen }}>
      <div className="accordion">{children}</div>
    </AccordionContext.Provider>
  );
}

Accordion.Item = function Item({ id, title, children }) {
  const { open, setOpen } = useContext(AccordionContext);
  const isOpen = open === id;

  return (
    <div className="accordion-item">
      <button className="accordion-header" onClick={() => setOpen(id)}>
        {title} <span>{isOpen ? '▲' : '▼'}</span>
      </button>
      {isOpen && <div className="accordion-body">{children}</div>}
    </div>
  );
};

// Usage — fully controlled by parent
<Accordion open={section} onChange={setSection}>
  <Accordion.Item id="details" title="Details">...</Accordion.Item>
  <Accordion.Item id="reviews" title="Reviews">...</Accordion.Item>
</Accordion>
```
