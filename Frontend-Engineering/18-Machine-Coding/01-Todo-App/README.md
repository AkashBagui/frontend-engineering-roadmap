# Todo App

**Difficulty:** Easy | **Est. Time:** 30–45 min

---

## Problem Statement

Build a fully functional Todo application where users can create, read, update, and delete tasks. The app should support filtering, searching, and persisting todos across sessions.

---

## Requirements

### Functional
- [ ] Add a new todo with a title and optional description
- [ ] Mark a todo as completed / uncompleted (toggle)
- [ ] Edit an existing todo's title
- [ ] Delete a single todo or clear all completed
- [ ] Filter todos: All / Active / Completed
- [ ] Search/filter todos by title text
- [ ] Persist todos in LocalStorage
- [ ] Show total count and active count

### Non-Functional
- [ ] No external state management library (use useState/useReducer)
- [ ] Responsive design (mobile + desktop)
- [ ] Accessible form inputs with labels
- [ ] Keyboard support (Enter to add, Escape to cancel edit)

---

## Component Architecture

```
App
├── TodoHeader
│   ├── Logo / Title
│   └── TodoInput (controlled input + submit)
├── TodoToolbar
│   ├── FilterButtons (All | Active | Completed)
│   ├── SearchInput
│   └── ClearCompletedButton
├── TodoList
│   └── TodoItem (×N)
│       ├── Checkbox (toggle)
│       ├── Title (inline editable on double-click)
│       ├── EditInput (shown during editing)
│       └── DeleteButton
└── TodoFooter
    └── CountDisplay
```

---

## State Management

```js
// Single source of truth
const [todos, dispatch] = useReducer(todoReducer, initialTodos);

// Derived state
const filteredTodos = useMemo(() => {
  return todos
    .filter(t => filter === 'all' ? true : filter === 'active' ? !t.completed : t.completed)
    .filter(t => t.title.toLowerCase().includes(searchTerm.toLowerCase()));
}, [todos, filter, searchTerm]);
```

### Reducer Actions

```
ADD_TODO       → { id, title, completed: false, createdAt }
TOGGLE_TODO    → flip completed
EDIT_TODO      → update title
DELETE_TODO    → remove by id
CLEAR_COMPLETED → remove all completed
LOAD_TODOS     → hydrate from LocalStorage
```

---

## Implementation Steps

1. Scaffold with Vite (`npm create vite@latest todo-app -- --template react`)
2. Build static UI components: Header, Input, List, Item, Footer
3. Add `useReducer` with todos state and all CRUD actions
4. Wire up todo input (Enter to submit, prevent empty)
5. Implement toggle (checkbox onChange)
6. Implement inline edit (double-click → input → blur/Enter saves, Escape cancels)
7. Implement delete and clear completed
8. Add filter buttons (All / Active / Completed)
9. Add search input with debounce (300ms)
10. Persist to LocalStorage (`useEffect` on todos change)
11. Handle empty state ("No todos yet" / "No results found")
12. Add keyboard navigation and ARIA attributes

---

## Code Snippets

### Todo Reducer

```js
function todoReducer(state, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [...state, { id: crypto.randomUUID(), title: action.payload, completed: false, createdAt: Date.now() }];
    case 'TOGGLE_TODO':
      return state.map(t => t.id === action.id ? { ...t, completed: !t.completed } : t);
    case 'EDIT_TODO':
      return state.map(t => t.id === action.id ? { ...t, title: action.payload } : t);
    case 'DELETE_TODO':
      return state.filter(t => t.id !== action.id);
    case 'CLEAR_COMPLETED':
      return state.filter(t => !t.completed);
    case 'LOAD_TODOS':
      return action.payload;
    default:
      return state;
  }
}
```

### LocalStorage Persistence Hook

```js
function useLocalStorageReducer(key, reducer, initialState) {
  const [state, dispatch] = useReducer(reducer, initialState, () => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialState;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(state));
  }, [key, state]);

  return [state, dispatch];
}
```

### Search with Debounce

```js
const [searchTerm, setSearchTerm] = useState('');
const debouncedSearch = useDebounce(searchTerm, 300);
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Empty title | Prevent submit; show validation message |
| Very long title | Truncate with ellipsis, show full on hover |
| Duplicate todos | Allow (no constraint against duplicates) |
| LocalStorage full | Wrap setItem in try-catch; fall back to in-memory |
| Skip hydration | Wait for client render before showing persisted data |
| Edit empty title | Revert to original on blur if empty |
| Rapid double-toggle | Use the latest state (reducer guarantees consistency) |

---

## Bonus Features

- [ ] **Drag to reorder** items (HTML5 DnD or dnd-kit)
- [ ] **Undo / Redo** (keep history stack in reducer)
- [ ] **Due dates** with date picker and overdue highlighting
- [ ] **Sub-tasks** (nested todos with indentation)
- [ ] **Dark mode** toggle
- [ ] **Bulk actions** (select multiple → delete / complete)
- [ ] **Export / Import** as JSON

---

## Common Interview Questions

1. **Why useReducer over useState?** — Multiple interrelated state transitions; easier to test; predictable updates from actions.

2. **How do you prevent losing todos on accidental refresh?** — LocalStorage persistence with debounced writes. Optional: IndexedDB for large datasets.

3. **How would you scale this for 10,000 todos?** — Virtualization (react-window), pagination, lazy load, backend API with pagination.

4. **How do you handle the edit-in-place UX?** — Controlled input that appears on double-click, saves on blur/Enter, cancels on Escape.

5. **How do you test the reducer?** — Pure function: pass state + action, assert output. Use `describe`/`it` blocks per action type.

---

## Resources

- [useReducer docs](https://react.dev/reference/react/useReducer)
- [LocalStorage MDN](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)
- [dnd-kit](https://dndkit.com/) (for drag reorder)
