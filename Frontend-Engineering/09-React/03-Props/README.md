# Props

Props (short for "properties") are **read-only inputs** passed from a parent component to a child component. They are the primary mechanism for data flow in React.

## How Props Work

Data flows **unidirectional** (top-down): parent passes props to child, child cannot modify them.

```jsx
function Parent() {
  const user = { name: 'Alice', age: 30 };
  return <Child name={user.name} age={user.age} />;
  // or spread: <Child {...user} />
}

function Child({ name, age }) {
  return <p>{name} is {age} years old</p>;
}
```

## Destructuring Props

Always destructure props in the function parameter for cleaner code:

```jsx
// Without destructuring
function Greeting(props) {
  return <h1>Hello, {props.name}! You are {props.age}</h1>;
}

// With destructuring
function Greeting({ name, age }) {
  return <h1>Hello, {name}! You are {age}</h1>;
}

// With rest pattern for forwarding remaining props
function Button({ variant = 'primary', children, ...rest }) {
  return <button className={`btn btn-${variant}`} {...rest}>{children}</button>;
}
```

## Default Props

```jsx
// Default via destructuring (modern)
function Card({ title = 'Untitled', children }) {
  return <div className="card"><h2>{title}</h2>{children}</div>;
}

// Default props via static property (class components, legacy)
Card.defaultProps = { title: 'Untitled' };
```

## The `children` Prop

`children` is a special prop for content passed between opening and closing tags:

```jsx
function Wrapper({ children }) {
  return <div className="wrapper">{children}</div>;
}

// Usage: anything can be children
<Wrapper>
  <p>Text</p>
  {elements}
  "Plain text"
  {condition && <Component />}
</Wrapper>
```

### Patterns with children

```jsx
// Render multiple children in specific slots
function SplitPane({ left, right }) {
  return (
    <div className="split">
      <div className="left">{left}</div>
      <div className="right">{right}</div>
    </div>
  );
}
<SplitPane left={<Sidebar />} right={<Content />} />
```

## Prop Drilling

Prop drilling is passing props through multiple intermediate components that don't need them:

```
App ──user──▶ Header ──user──▶ Nav ──user──▶ Avatar
                     ↻                ↻
                Doesn't use user  Doesn't use user
```

### Solutions

1. **Component composition** — restructure to pass components directly
2. **Context API** — for truly global data (theme, auth)
3. **State management library** — Zustand, Redux for complex state

```jsx
// Composition solution: avoid drilling by passing the component
function App() {
  const user = { name: 'Alice' };
  return <Header avatar={<Avatar user={user} />} />;
}
function Header({ avatar }) { return <header>{avatar}</header>; }
function Avatar({ user }) { return <img src={user.avatar} />; }
```

## Props Patterns

```jsx
// Conditional props
function Alert({ type = 'info', message, children }) {
  return <div className={`alert alert-${type}`}>{message || children}</div>;
}

// Render prop pattern (less common, hooks preferred now)
function DataFetcher({ url, render }) {
  const [data, setData] = useState(null);
  useEffect(() => { fetch(url).then(r => r.json()).then(setData); }, [url]);
  return render(data);
}
<DataFetcher url="/api/data" render={(data) => <div>{data?.name}</div>} />

// Component as prop
function Menu({ items, ItemComponent }) {
  return <ul>{items.map(item => <ItemComponent key={item.id} item={item} />)}</ul>;
}
<Menu items={menuItems} ItemComponent={MenuItem} />

// Function as children
function Toggle({ children }) {
  const [on, setOn] = useState(false);
  return children({ on, toggle: () => setOn(!on) });
}
<Toggle>{({ on, toggle }) => <button onClick={toggle}>{on ? 'ON' : 'OFF'}</button>}</Toggle>
```

## Immutability of Props

Props are **immutable** — a component must never modify its own props.

```jsx
// ❌ WRONG: Never mutate props
function BadComponent({ user }) {
  user.name = 'New Name'; // This mutates the prop!
  return <p>{user.name}</p>;
}

// ✅ CORRECT: Create new data when needed
function GoodComponent({ user }) {
  const displayName = user.name.toUpperCase(); // Derived value, not mutation
  return <p>{displayName}</p>;
}
```

**Why?** Immutability ensures predictable behavior. If a child could mutate props, tracking data flow would become impossible — multiple components could change the same value, and React couldn't detect changes efficiently.

### Passing Data Up (via callbacks)

To "change" props from a child, call a function from the parent:

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  return <Child count={count} onIncrement={() => setCount(c => c + 1)} />;
}

function Child({ count, onIncrement }) {
  return (
    <div>
      <span>{count}</span>
      <button onClick={onIncrement}>+</button>
    </div>
  );
}
```
