# Components

Components are the **building blocks of React UI**. A component is a self-contained, reusable piece of code that returns React elements describing what should appear on screen.

## Functional Components

Modern React uses **functional components** — plain JavaScript functions that accept `props` and return JSX.

```jsx
// Simple component
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Arrow function component
const Greeting = ({ name }) => <h1>Hello, {name}!</h1>;

// Component with state and effects
function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>+</button>
    </div>
  );
}
```

**Why functional over class?** Less boilerplate, no `this` binding issues, better composition with hooks, easier to test, better tree-shaking support.

## Component Composition

Composition is the pattern of combining smaller components to build complex UIs. React encourages composition over inheritance.

```jsx
function Page() {
  return (
    <Layout>
      <Header />
      <Sidebar>
        <Nav />
      </Sidebar>
      <Main>
        <Article />
        <Comments />
      </Main>
      <Footer />
    </Layout>
  );
}
```

### children prop for composition

```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="card-body">{children}</div>
    </div>
  );
}

<Card title="Welcome">
  <p>This content is injected via children</p>
  <button>Click</button>
</Card>
```

## Component Tree

A React app forms a tree of components, rooted at the `App` component.

```
App
├── Header
│   ├── Logo
│   └── Navigation
│       ├── NavLink
│       └── NavLink
├── MainContent
│   ├── Sidebar
│   │   ├── UserInfo
│   │   └── MenuList
│   │       └── MenuItem[]
│   └── ContentArea
│       ├── Breadcrumb
│       ├── ArticleList
│       │   └── ArticleCard[]
│       └── Pagination
└── Footer
    ├── FooterLinks
    └── Copyright
```

**Why this matters:** The tree structure determines:
- Data flow direction (top → bottom via props)
- Where state lives (lift to common ancestor)
- Re-render scope (parent re-render → all children re-render)
- Testing boundaries (each leaf can be tested in isolation)

## Splitting Components

Knowing when to split a component into smaller pieces:

| Consider splitting when | Keep as one when |
|-----------------------|------------------|
| Component exceeds 80–100 lines | Rendering a simple list item |
| Component handles multiple responsibilities | Light wrapper/dumb presentational |
| You need to reuse part of the UI | The piece would always be used together |
| Part of the component needs its own state | Avoid premature abstraction |
| Testing a specific section is hard in isolation | The split adds unnecessary complexity |

## Presentational vs Container Components

A common pattern (less critical with hooks, but conceptually useful):

| Presentational (Child) | Container (Parent) |
|-----------------------|-------------------|
| How things look | How things work |
| Receives data via props | Manages state, effects |
| No dependencies on app logic | Calls hooks, services, APIs |
| Reusable | App-specific |
| Examples: Button, Card, List | Examples: UserListPage, TodoContainer |

```jsx
// Container — manages state
function UserListContainer() {
  const [users, setUsers] = useState([]);
  useEffect(() => { fetch('/api/users').then(r => r.json()).then(setUsers); }, []);
  return <UserList users={users} onSelect={handleSelect} />;
}

// Presentational — renders UI
function UserList({ users, onSelect }) {
  return (
    <ul>
      {users.map(u => <li key={u.id} onClick={() => onSelect(u.id)}>{u.name}</li>)}
    </ul>
  );
}
```

## File Organization

### Feature-based (recommended)

```
src/
├── features/
│   ├── auth/
│   │   ├── AuthContext.jsx
│   │   ├── LoginForm.jsx
│   │   ├── SignupForm.jsx
│   │   └── authService.js
│   ├── todos/
│   │   ├── TodoList.jsx
│   │   ├── TodoItem.jsx
│   │   ├── TodoForm.jsx
│   │   └── TodoFilter.jsx
│   └── users/
│       ├── UserList.jsx
│       ├── UserCard.jsx
│       └── userApi.js
├── components/
│   ├── ui/
│   │   ├── Button.jsx
│   │   ├── Modal.jsx
│   │   └── Spinner.jsx
│   └── layouts/
│       ├── Header.jsx
│       ├── Footer.jsx
│       └── Sidebar.jsx
└── App.jsx
```

### Naming conventions

- **PascalCase** for component files: `Button.jsx`, `UserProfile.jsx`
- One component per file (unless closely related)
- Index files for cleaner imports: `components/ui/index.js`
- Test files co-located: `Button.test.jsx`, `Button.stories.jsx`
