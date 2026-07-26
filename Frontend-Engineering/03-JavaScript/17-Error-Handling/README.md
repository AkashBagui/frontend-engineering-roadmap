# Error Handling in JavaScript

## try/catch/finally

```js
try {
  // Code that may throw
  const result = riskyOperation();
  console.log(result);
} catch (error) {
  // Handle the error
  console.error("Caught:", error.message);
} finally {
  // Always runs (cleanup)
  cleanup();
}
```

## The `throw` Statement

```js
throw new Error("Something went wrong");
throw "string error"; // works but not recommended
throw 42;             // any value
throw { code: 500, message: "Server error" };

// Throwing in a function
function divide(a, b) {
  if (b === 0) throw new Error("Division by zero");
  return a / b;
}
```

## Error Types

```js
// Built-in error types
new Error("Generic error");
new SyntaxError("Invalid syntax");
new ReferenceError("Variable not defined");
new TypeError("Invalid type");
new RangeError("Value out of range");
new URIError("Invalid URI");
new EvalError("eval() error");

// Check error type
try {
  JSON.parse("invalid json");
} catch (err) {
  if (err instanceof SyntaxError) {
    console.log("JSON parse error");
  } else if (err instanceof TypeError) {
    console.log("Type error");
  } else {
    console.log("Unknown error");
  }
}
```

## Custom Errors

```js
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = "ValidationError";
    this.field = field;
  }
}

class NetworkError extends Error {
  constructor(url, status) {
    super(`HTTP ${status}: ${url}`);
    this.name = "NetworkError";
    this.url = url;
    this.status = status;
  }
}

// Usage
function validateUser(user) {
  if (!user.name) {
    throw new ValidationError("Name is required", "name");
  }
  if (user.age < 0) {
    throw new ValidationError("Invalid age", "age");
  }
}
```

## Error Boundaries (React)

```js
// React error boundary (class component)
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, info) {
    logErrorToService(error, info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <h1>Something went wrong</h1>;
    }
    return this.props.children;
  }
}
```

## Error Logging

```js
// Global error handler
window.onerror = function(message, source, lineno, colno, error) {
  console.error("Global error:", { message, source, lineno, colno, error });
  logToService({ message, source, lineno, colno, error });
  return true; // prevent default handling
};

// Unhandled promise rejections
window.addEventListener("unhandledrejection", event => {
  console.error("Unhandled rejection:", event.reason);
  logToService({ type: "unhandledrejection", reason: event.reason });
  event.preventDefault();
});

// Node.js
process.on("uncaughtException", err => {
  console.error("Uncaught:", err);
  // clean up and exit
});
process.on("unhandledRejection", (reason, promise) => {
  console.error("Unhandled rejection:", reason);
});
```

## Defensive Programming

```js
// Guard clauses
function processUser(user) {
  if (!user) throw new Error("User required");
  if (!user.id) throw new Error("User must have ID");
  if (typeof user.age !== "number") throw new TypeError("Age must be number");

  // Safe to proceed
}

// Default values
function fetchData(url, options = {}) {
  const { method = "GET", timeout = 5000 } = options;
  // ...
}

// Type checking
function calculateTotal(items) {
  if (!Array.isArray(items)) return 0;
  return items.reduce((sum, item) => sum + (item.price || 0), 0);
}

// Optional chaining
const street = user?.address?.street ?? "No address";
```

## Best Practices

### 1. Don't swallow errors
```js
// ❌ Bad — empty catch hides errors
try { risky(); } catch (e) {}

// ✓ Good — log and handle
try { risky(); } catch (e) {
  console.error("Failed:", e);
  notifyUser("Operation failed");
}
```

### 2. Use specific error types
```js
// ❌ Bad
throw "error";

// ✓ Good
throw new ValidationError("Invalid email", "email");
```

### 3. Clean up in finally
```js
// ❌ Bad — if error, file stays open
const file = openFile();
try {
  write(file, data);
} catch (e) {
  console.error(e);
}
closeFile(file);

// ✓ Good — always closed
const file = openFile();
try {
  write(file, data);
} finally {
  closeFile(file);
}
```

### 4. Don't throw in finally
```js
try {
  try {
    throw new Error("A");
  } finally {
    throw new Error("B"); // overrides A!
  }
} catch (e) {
  console.log(e.message); // "B"
}
```

### 5. Async error handling
```js
// Promise
fetch("/api")
  .then(handleResponse)
  .catch(handleError);

// Async/await
async function load() {
  try {
    return await fetch("/api");
  } catch (err) {
    handleError(err);
  }
}
```

## Error Flow Diagram

```
Function A
  │
  ├── try { return B() }
  │       │
  │       ├── B() tries C()
  │       │     │
  │       │     └── throw new Error()
  │       │             │
  │       │     ┌───────┘
  │       │     ↓
  │       └── catch (B catches or propagates)
  │               │
  │               ↓
  │         Error propagated to A
  │               │
  └─────────── catch (A handles)
                  │
                  ↓
            Logged + User notified
```

## Error Cause (ES2022)

```js
try {
  await fetchData();
} catch (cause) {
  throw new Error("Failed to load dashboard", { cause });
}

// Later:
try {
  await loadDashboard();
} catch (err) {
  console.log(err.message);       // "Failed to load dashboard"
  console.log(err.cause.message); // original error
}
```
