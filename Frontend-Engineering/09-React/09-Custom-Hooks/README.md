# Custom Hooks

Custom hooks are JavaScript functions that use built-in React hooks to encapsulate and reuse **stateful logic**. They follow the naming convention `useXxx` and follow the same rules as built-in hooks.

## Why Custom Hooks?

- **Reuse logic** across components without duplicating code
- **Separate concerns** — extract complex logic out of components
- **Composable** — combine multiple hooks to build complex behavior
- **Testable** — test logic in isolation

## Creating a Custom Hook

```jsx
// useDocumentTitle.js
import { useEffect } from 'react';

export function useDocumentTitle(title) {
  useEffect(() => {
    const prev = document.title;
    document.title = title;
    return () => { document.title = prev; }; // restore on unmount
  }, [title]);
}

// Usage
function ProfilePage() {
  useDocumentTitle('User Profile');
  return <div>...</div>;
}
```

## Hook Composition

Custom hooks can call other hooks, including other custom hooks:

```jsx
function useUser(id) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    fetch(`/api/users/${id}`)
      .then(r => r.json())
      .then(setUser)
      .finally(() => setLoading(false));
  }, [id]);

  return { user, loading };
}

function useUserProfile(id) {
  const { user, loading } = useUser(id);
  useDocumentTitle(user ? user.name : 'Loading...');
  return { user, loading };
}
```

## Common Custom Hook Examples

### useLocalStorage

```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    try {
      const item = localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  useEffect(() => {
    try {
      localStorage.setItem(key, JSON.stringify(value));
    } catch (e) {
      console.error('Failed to save to localStorage', e);
    }
  }, [key, value]);

  return [value, setValue];
}
```

### useDebounce

```jsx
function useDebounce(value, delay = 500) {
  const [debounced, setDebounced] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debounced;
}

// Usage: debounce API search
function Search() {
  const [query, setQuery] = useState('');
  const debouncedQuery = useDebounce(query, 300);

  useEffect(() => {
    if (debouncedQuery) fetchResults(debouncedQuery);
  }, [debouncedQuery]);

  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```

### useFetch

```jsx
function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    if (!url) return;
    const controller = new AbortController();
    setLoading(true);

    fetch(url, { ...options, signal: controller.signal })
      .then(r => {
        if (!r.ok) throw new Error(`HTTP ${r.status}`);
        return r.json();
      })
      .then(setData)
      .catch(e => {
        if (e.name !== 'AbortError') setError(e.message);
      })
      .finally(() => setLoading(false));

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UsersList() {
  const { data: users, loading, error } = useFetch('/api/users');
  if (loading) return <Spinner />;
  if (error) return <Error message={error} />;
  return <UserList users={users} />;
}
```

### useMediaQuery

```jsx
function useMediaQuery(query) {
  const [matches, setMatches] = useState(() => window.matchMedia(query).matches);

  useEffect(() => {
    const mql = window.matchMedia(query);
    const handler = (e) => setMatches(e.matches);
    mql.addEventListener('change', handler);
    return () => mql.removeEventListener('change', handler);
  }, [query]);

  return matches;
}

// Usage
function ResponsiveComponent() {
  const isMobile = useMediaQuery('(max-width: 768px)');
  const isDark = useMediaQuery('(prefers-color-scheme: dark)');
  return <div>{isMobile ? 'Mobile' : 'Desktop'} - {isDark ? '🌙' : '☀️'}</div>;
}
```

### useIntersectionObserver

```jsx
function useIntersectionObserver(ref, options = {}) {
  const [isIntersecting, setIsIntersecting] = useState(false);

  useEffect(() => {
    const element = ref.current;
    if (!element) return;

    const observer = new IntersectionObserver(([entry]) => {
      setIsIntersecting(entry.isIntersecting);
    }, { threshold: 0.1, ...options });

    observer.observe(element);
    return () => observer.disconnect();
  }, [ref, options]);

  return isIntersecting;
}

// Usage: lazy loading images
function LazyImage({ src, alt }) {
  const imgRef = useRef();
  const isVisible = useIntersectionObserver(imgRef);

  return (
    <div ref={imgRef}>
      {isVisible ? <img src={src} alt={alt} /> : <Placeholder />}
    </div>
  );
}
```

### usePrevious

```jsx
function usePrevious(value) {
  const ref = useRef();

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref.current;
}

// Usage: detect value changes
function CountDisplay({ count }) {
  const prevCount = usePrevious(count);
  const direction = count > prevCount ? '⬆' : count < prevCount ? '⬇' : '➡';
  return <div>{direction} {count}</div>;
}
```

### useToggle

```jsx
function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  const toggle = useCallback(() => setValue(v => !v), []);
  const setTrue = useCallback(() => setValue(true), []);
  const setFalse = useCallback(() => setValue(false), []);
  return { value, toggle, setTrue, setFalse };
}

// Usage
function Menu() {
  const { value: isOpen, toggle, setFalse } = useToggle();
  return (
    <>
      <button onClick={toggle}>{isOpen ? 'Close' : 'Open'}</button>
      {isOpen && <Dropdown onClose={setFalse} />}
    </>
  );
}
```

## Custom Hook Patterns

### Parameter pattern

```jsx
function useWindowSize(debounceMs = 0) {
  // Accept configuration parameters
}

function useAuth(required = false) {
  const auth = useAuthContext();
  const navigate = useNavigate();
  useEffect(() => {
    if (required && !auth.user) navigate('/login');
  }, [required, auth.user]);
  return auth;
}
```

### Return value patterns

```jsx
// Tuple (like useState)
const [count, setCount] = useCounter(0);

// Object (descriptive)
const { data, loading, error } = useFetch('/api');

// Array (for multiple values)
const [width, height] = useWindowSize();
```

### Controlled callback pattern

```jsx
function useClipboard({ onCopy } = {}) {
  const [copied, setCopied] = useState(false);

  const copy = async (text) => {
    await navigator.clipboard.writeText(text);
    setCopied(true);
    onCopy?.(text);
    setTimeout(() => setCopied(false), 2000);
  };

  return { copied, copy };
}
```
