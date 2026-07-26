# Context API

## When to Use Context

Context is designed for **low-frequency state updates** that are consumed by **many components** at different nesting levels. It is not a replacement for prop drilling in all cases.

### Appropriate Use Cases
- Theme (dark/light mode)
- User authentication state
- Locale/i18n preferences
- UI layout state (sidebar open/closed)
- Feature flags

### Inappropriate Use Cases
- Frequently updating data (every frame, real-time)
- Form input values
- Lists that update often
- Data that only a single child needs

## Provider Architecture

### Basic Provider Pattern

```jsx
import { createContext, useContext, useState, useCallback } from 'react';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);

  const login = useCallback(async (credentials) => {
    setLoading(true);
    try {
      const userData = await api.login(credentials);
      setUser(userData);
    } finally {
      setLoading(false);
    }
  }, []);

  const logout = useCallback(() => {
    setUser(null);
  }, []);

  return (
    <AuthContext.Provider value={{ user, loading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => {
  const ctx = useContext(AuthContext);
  if (!ctx) throw new Error('useAuth must be used within AuthProvider');
  return ctx;
};
```

### App-Level Wiring

```jsx
function App() {
  return (
    <AuthProvider>
      <ThemeProvider>
        <LocaleProvider>
          <Router>
            <Layout />
          </Router>
        </LocaleProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}
```

## Performance Issues: Re-renders

### The Problem
When a Context value changes, **all consumers** re-render, even if they only read a part of the value that didn't change.

```jsx
// BAD: Entire context object changes on any update
const [state, dispatch] = useReducer(reducer, initialState);
<MyContext.Provider value={{ state, dispatch }}>
```

Every consumer re-renders whenever `state` changes, even if they only use `dispatch`.

### Memoize Context Values

```jsx
// GOOD: Stable references with useMemo
const value = useMemo(() => ({ user, login, logout }), [user, login, logout]);
<AuthContext.Provider value={value}>
```

## Context Splitting

Split large contexts into smaller, independently updateable contexts.

```jsx
// Instead of one giant context:
// UserContext — rarely changes (auth state)
// NotificationContext — updates frequently (toasts)
// ThemeContext — rarely changes

function AppProviders({ children }) {
  return (
    <UserProvider>
      <NotificationProvider>
        <ThemeProvider>
          {children}
        </ThemeProvider>
      </NotificationProvider>
    </UserProvider>
  );
}
```

### Context Tree Diagram

```
                     AppProviders
                    /     |      \
           UserProvider  |   ThemeProvider
                   \     |     /
            NotificationProvider
                     |
                 <Router>
                  children
```

## useReducer + Context Combination

Combine useReducer with Context for predictable state management without external libraries.

```jsx
// store.js
const initialState = {
  items: [],
  total: 0,
};

function cartReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM': {
      const existing = state.items.find(i => i.id === action.payload.id);
      if (existing) {
        return {
          ...state,
          items: state.items.map(i =>
            i.id === action.payload.id
              ? { ...i, quantity: i.quantity + 1 }
              : i
          ),
        };
      }
      return {
        ...state,
        items: [...state.items, { ...action.payload, quantity: 1 }],
      };
    }
    case 'REMOVE_ITEM':
      return {
        ...state,
        items: state.items.filter(i => i.id !== action.payload.id),
      };
    default:
      return state;
  }
}

// CartContext.jsx
export const CartContext = createContext(null);
export const CartDispatchContext = createContext(null);

export function CartProvider({ children }) {
  const [state, dispatch] = useReducer(cartReducer, initialState);

  const value = useMemo(() => state, [state]);

  return (
    <CartContext.Provider value={value}>
      <CartDispatchContext.Provider value={dispatch}>
        {children}
      </CartDispatchContext.Provider>
    </CartContext.Provider>
  );
}

export function useCart() {
  return useContext(CartContext);
}

export function useCartDispatch() {
  return useContext(CartDispatchContext);
}
```

Consumers that only dispatch don't re-render on state changes:

```jsx
function AddToCartButton({ product }) {
  const dispatch = useCartDispatch();
  // This component does NOT re-render when cart changes
  return <button onClick={() => dispatch({ type: 'ADD_ITEM', payload: product })}>Add</button>;
}
```

## Real-World Scenario: Multi-Step Form Wizard

A checkout wizard with 4 steps. Context holds form data, current step, and validation state.

```jsx
const CheckoutContext = createContext(null);

function CheckoutProvider({ children }) {
  const [step, setStep] = useState(1);
  const [formData, setFormData] = useState({
    shipping: {},
    payment: {},
    review: false,
  });

  const nextStep = () => setStep(s => Math.min(s + 1, 4));
  const prevStep = () => setStep(s => Math.max(s - 1, 1));
  const updateData = (section, data) =>
    setFormData(prev => ({ ...prev, [section]: { ...prev[section], ...data } }));

  const value = useMemo(
    () => ({ step, formData, nextStep, prevStep, updateData }),
    [step, formData]
  );

  return (
    <CheckoutContext.Provider value={value}>
      {children}
    </CheckoutContext.Provider>
  );
}
```

## Summary

| Aspect | Recommendation |
|--------|---------------|
| State frequency | Low (theme, auth) |
| State depth | Deep tree consumption |
| Team size | Small to medium |
| Bundle size | Zero KB (built-in) |
| Dev tools | React DevTools only |
| Scalability | Limited — context splitting required beyond 3-4 contexts |
