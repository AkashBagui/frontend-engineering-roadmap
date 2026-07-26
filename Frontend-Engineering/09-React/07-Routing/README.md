# Routing

React Router is the most popular routing library for React applications, enabling navigation between views without full page reloads.

## React Router v6

### Setup

```bash
npm install react-router-dom
```

### Core Components

```jsx
import {
  BrowserRouter,
  Routes,
  Route,
  Link,
  NavLink,
  Outlet,
  useParams,
  useNavigate,
  useLocation,
} from 'react-router-dom';
```

## Basic Setup

```jsx
function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <NavLink to="/contact" className={({ isActive }) => isActive ? 'active' : ''}>
          Contact
        </NavLink>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Link vs NavLink vs a tag

| Element | Use case |
|---------|----------|
| `<Link to="/page">` | Client-side navigation (no full reload) |
| `<NavLink to="/page">` | Same as Link + gets `isActive` class |
| `<a href="/page">` | Avoid — causes full page reload in SPA |

## Nested Routes

```jsx
<Routes>
  <Route path="/dashboard" element={<DashboardLayout />}>
    <Route index element={<DashboardHome />} />
    <Route path="analytics" element={<Analytics />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>

// DashboardLayout renders <Outlet /> to show nested routes
function DashboardLayout() {
  return (
    <div>
      <Sidebar />
      <main>
        <Outlet /> {/* DashboardHome | Analytics | Settings renders here */}
      </main>
    </div>
  );
}
```

## Dynamic Routes & Parameters

```jsx
// Route definition
<Route path="/users/:userId" element={<UserProfile />} />
<Route path="/users/:userId/posts/:postId" element={<PostDetail />} />

// Accessing params
function UserProfile() {
  const { userId } = useParams();
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`).then(r => r.json()).then(setUser);
  }, [userId]);

  return <div>{user?.name}</div>;
}
```

## Navigation

```jsx
function LoginButton() {
  const navigate = useNavigate();
  const location = useLocation();

  const handleLogin = async () => {
    await login();
    navigate('/dashboard');                         // push
    navigate('/dashboard', { replace: true });       // replace (no back)
    navigate(-1);                                    // go back
    navigate('/dashboard', { state: { from: location } }); // pass state
  };
}
```

## Route Parameters & Query Strings

```jsx
import { useSearchParams } from 'react-router-dom';

function SearchPage() {
  const [searchParams, setSearchParams] = useSearchParams();
  const query = searchParams.get('q') || '';
  const page = Number(searchParams.get('page')) || 1;

  const updateQuery = (newQuery) => {
    setSearchParams({ q: newQuery, page: '1' });
  };

  return <div>Searching for "{query}" - Page {page}</div>;
}
```

## Lazy Loading Routes

```jsx
import { lazy, Suspense } from 'react';

const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Dashboard = lazy(() => import('./pages/Dashboard'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<div>Loading...</div>}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/dashboard/*" element={<Dashboard />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}
```

## Protected Routes

```jsx
function RequireAuth({ children }) {
  const { user } = useAuth();
  const location = useLocation();

  if (!user) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }
  return children;
}

// Usage
<Routes>
  <Route path="/public" element={<PublicPage />} />
  <Route path="/dashboard" element={
    <RequireAuth>
      <Dashboard />
    </RequireAuth>
  } />
</Routes>
```

## SPA Routing Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    Single Page App                          │
│                                                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │                   Browser                             │  │
│  │  URL: /users/42                                       │  │
│  │                                                       │  │
│  │  ┌─ React Router ──────────────────────────────────┐  │  │
│  │  │  matchRoute('/users/42') → <UserProfile id=42>  │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  │                                                       │  │
│  │  ┌─ History API ───────────────────────────────────┐  │  │
│  │  │  pushState / replaceState / popEvent            │  │  │
│  │  │  No full page reload!                           │  │  │
│  │  └─────────────────────────────────────────────────┘  │  │
│  │                                                       │  │
│  │  User clicks <Link to="/users/42">                    │  │
│  │    1. e.preventDefault()                              │  │
│  │    2. history.pushState('/users/42')                  │  │
│  │    3. Router matches route → renders component        │  │
│  │    4. Browser URL updates (no refresh)                │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Common Patterns

```jsx
// 404 Catch-all
<Route path="*" element={<NotFound />} />

// Index route (default child of a layout route)
<Route path="/dashboard" element={<DashboardLayout />}>
  <Route index element={<DashboardHome />} />
  <Route path="settings" element={<Settings />} />
</Route>

// Relative links in nested routes
<Link to="settings">Settings</Link>   // → /dashboard/settings
<Link to="..">Back</Link>             // → /dashboard/..
<Link to="../users">Users</Link>      // → /users
```

## React Router v6 vs v5

| Feature | v5 | v6 |
|---------|----|----|
| Route syntax | `<Route path="/" component={Home} />` | `<Route path="/" element={<Home />} />` |
| Routes wrapper | `<Switch>` | `<Routes>` (more strict) |
| Nested routes | Manual in component | `<Outlet />` in parent route |
| Exact matching | `exact` prop | Default (no `exact`) |
| Relative links | Full paths required | Relative by default |
| useHistory | `useHistory()` | `useNavigate()` |
| Route ordering | Most specific first | Routes auto-ranked by specificity |
