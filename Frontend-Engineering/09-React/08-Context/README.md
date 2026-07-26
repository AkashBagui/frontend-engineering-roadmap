# Context

The Context API provides a way to share values across the component tree without passing props through every level.

## When to Use Context

Context is designed for **global** or **widely-shared** data:

- Theme (dark/light mode)
- Authentication/user state
- Locale/preferred language
- UI state (sidebar open, toast notifications)

## Creating and Using Context

### 1. Create the context

```jsx
import { createContext, useContext } from 'react';

const ThemeContext = createContext('light');
//                  initial value (used when no Provider wraps the tree)
```

### 2. Provide the value

```jsx
function App() {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      <MainContent />
    </ThemeContext.Provider>
  );
}
```

### 3. Consume the value

```jsx
function ThemedButton() {
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <button
      onClick={() => setTheme(t => t === 'light' ? 'dark' : 'light')}
      className={theme}
    >
      Toggle Theme
    </button>
  );
}
```

### Full example

```jsx
// ThemeContext.jsx
const ThemeContext = createContext();
export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');
  const toggleTheme = () => setTheme(t => t === 'light' ? 'dark' : 'light');
  return <ThemeContext.Provider value={{ theme, toggleTheme }}>{children}</ThemeContext.Provider>;
}
export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (!ctx) throw new Error('useTheme must be used within ThemeProvider');
  return ctx;
}
```

## Context vs Prop Drilling

```
Without Context (prop drilling):
  App ──user──▶ Header ──user──▶ Nav ──user──▶ UserMenu ──user──▶ Avatar
       ──theme─▶ Header ──theme─▶ Nav ──theme─▶ UserMenu ──theme─▶ Avatar

With Context (direct access):
  App
   UserProvider ──user──▶ user context
   ThemeProvider ──theme─▶ theme context
   ├── Header (useUser → Avatar)
   ├── Nav
   ├── UserMenu
   └── Avatar
```

## Multiple Contexts

```jsx
function App() {
  return (
    <AuthProvider>
      <ThemeProvider>
        <UIContextProvider>
          <Main />
        </UIContextProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}
```

Avoid deeply nested providers by composing them into a single wrapper:

```jsx
function AppProviders({ children }) {
  return (
    <AuthProvider>
      <ThemeProvider>
        <UIContextProvider>
          {children}
        </UIContextProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}
```

## Performance Concerns

Context has a performance pitfall: **all consumers re-render when any value in the context changes**, even if they only read a part of it.

```jsx
// ❌ PROBLEM: setUser causes ALL ThemeContext consumers to re-render
<AppContext.Provider value={{ user, setUser, theme, setTheme }}>
  <App />
</AppContext.Provider>
```

### Solutions

1. **Split contexts** — separate concerns into their own providers

```jsx
<UserContext.Provider value={{ user, setUser }}>
  <ThemeContext.Provider value={{ theme, setTheme }}>
    <App />
  </ThemeContext.Provider>
</UserContext.Provider>
```

2. **Memoize the provider value**

```jsx
function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const value = useMemo(() => ({ user, setUser }), [user]);
  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
}
```

3. **Split reading and writing contexts**

```jsx
const UserReadContext = createContext();
const UserWriteContext = createContext();

function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  return (
    <UserReadContext.Provider value={user}>
      <UserWriteContext.Provider value={setUser}>
        {children}
      </UserWriteContext.Provider>
    </UserReadContext.Provider>
  );
}
// Components that only read don't re-render when setUser identity changes
```

## Context vs State Management Libraries

| Feature | Context | Zustand | Redux Toolkit |
|---------|---------|---------|---------------|
| Boilerplate | Minimal | Minimal | Moderate |
| Performance | All consumers re-render | Selector-based re-renders | Selector-based re-renders |
| Bundle size | 0 (built-in) | ~1 KB | ~12 KB |
| Outside React | Not possible | Yes (subscribe) | Yes (store.getState) |
| DevTools | React DevTools | DevTools extension | Redux DevTools |
| Middleware | Not applicable | Immer, persist, devtools | Thunk, saga, logger |
| Async logic | In components | In store actions | createAsyncThunk |
| Best for | Simple global state | Medium apps, simple state | Large apps, complex logic |

### When to use each

- **Context:** Theme, locale, auth user, small UI state (< 5 values)
- **Zustand:** Medium app state, want minimal code, like simplicity
- **Redux:** Large team, complex state logic, need middleware, time-travel debugging

## Context Pattern

```jsx
// context/AuthContext.jsx
const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    onAuthStateChanged(auth, (user) => {
      setUser(user);
      setLoading(false);
    });
  }, []);

  const value = useMemo(() => ({ user, loading, setUser }), [user, loading]);

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

export function useAuth() {
  const ctx = useContext(AuthContext);
  if (ctx === null) throw new Error('useAuth must be used within AuthProvider');
  return ctx;
}
```
