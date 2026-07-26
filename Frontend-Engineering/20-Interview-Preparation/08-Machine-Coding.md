# Machine Coding Interview Guide

## 1. Overview

Machine coding interviews test your ability to build a working application from scratch within a time limit (usually 60-90 minutes).

### What Interviewers Look For:
- **Code organization** - Clean structure, separation of concerns
- **Problem-solving** - Breaking down requirements into components
- **Functional correctness** - Features work as expected
- **Edge case handling** - Loading, empty, error states
- **Code quality** - Readable, maintainable, idiomatic
- **Testing** - Both manual and automated where appropriate
- **Communication** - Explaining decisions and trade-offs

### Common Problem Patterns:
1. **Component Library** - Button, Modal, Dropdown, Tabs
2. **Interactive Widgets** - Autocomplete, DatePicker, Star Rating
3. **Data Display** - Table with sort/filter, Kanban Board, Calendar
4. **Games** - Tic-Tac-Toe, Memory Game, Snake
5. **Form Builders** - Dynamic forms, Survey builder
6. **Visualization** - Charts, Graphs, Mind Maps

---

## 2. Approach Framework

### Phase 1: Understand & Plan (5-10 min)

1. **Clarify requirements**
   - What are the core features?
   - What are nice-to-haves?
   - What's the minimum viable product?

2. **Design component tree**
   - Identify main components
   - Define data flow
   - Plan state management

3. **Choose your stack**
   - React + TypeScript (most common)
   - Styling: CSS Modules, Tailwind, styled-components
   - State: useState, useReducer, Context
   - Routing: if needed

### Phase 2: Setup & Scaffold (5 min)

```bash
# Create React App or Vite
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install

# Clean up boilerplate
# Remove default styles, set up project structure
```

**Recommended structure:**
```
src/
├── components/       # Reusable components
├── hooks/           # Custom hooks
├── utils/           # Helper functions
├── types/           # TypeScript types
├── App.tsx
└── index.tsx
```

### Phase 3: Build Core Features (30-40 min)

1. **Start with data/model** - Define types and initial state
2. **Build components top-down** - Parent first, then children
3. **Implement one feature at a time** - Get it working
4. **Handle states** - Loading, empty, error, success

### Phase 4: Polish & Edge Cases (10-15 min)

1. **Edge cases** - Empty lists, long text, rapid clicks
2. **Accessibility** - ARIA labels, keyboard navigation
3. **Performance** - Memoization, avoiding re-renders
4. **Styling** - Responsive, visual polish

### Phase 5: Review & Refactor (5-10 min)

1. **Clean up code** - Remove console.logs, unused imports
2. **Extract logic** - Custom hooks, utility functions
3. **Add comments** - Explain complex logic (if needed)
4. **Test manually** - Click through the features

---

## 3. Common Component Patterns

### Pattern 1: Autocomplete / Search

```tsx
// Requirement: Search with suggestions, debounced input
interface AutocompleteProps {
  options: string[];
  onSelect: (value: string) => void;
  placeholder?: string;
}

function Autocomplete({ options, onSelect, placeholder }: AutocompleteProps) {
  const [query, setQuery] = useState('');
  const [suggestions, setSuggestions] = useState<string[]>([]);
  const [isOpen, setIsOpen] = useState(false);
  const [activeIndex, setActiveIndex] = useState(-1);
  
  const debouncedSearch = useMemo(
    () => debounce((value: string) => {
      const filtered = options.filter(opt =>
        opt.toLowerCase().includes(value.toLowerCase())
      );
      setSuggestions(filtered);
      setIsOpen(filtered.length > 0);
      setActiveIndex(-1);
    }, 300),
    [options]
  );
  
  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    setQuery(e.target.value);
    debouncedSearch(e.target.value);
  };
  
  const handleKeyDown = (e: React.KeyboardEvent) => {
    switch (e.key) {
      case 'ArrowDown':
        setActiveIndex(prev => Math.min(prev + 1, suggestions.length - 1));
        break;
      case 'ArrowUp':
        setActiveIndex(prev => Math.max(prev - 1, 0));
        break;
      case 'Enter':
        if (activeIndex >= 0) {
          handleSelect(suggestions[activeIndex]);
        }
        break;
      case 'Escape':
        setIsOpen(false);
        break;
    }
  };
  
  const handleSelect = (value: string) => {
    setQuery(value);
    setIsOpen(false);
    onSelect(value);
  };
  
  return (
    <div className="autocomplete">
      <input
        type="text"
        value={query}
        onChange={handleChange}
        onKeyDown={handleKeyDown}
        placeholder={placeholder}
        role="combobox"
        aria-expanded={isOpen}
        aria-autocomplete="list"
      />
      {isOpen && (
        <ul className="suggestions" role="listbox">
          {suggestions.map((suggestion, index) => (
            <li
              key={suggestion}
              role="option"
              aria-selected={index === activeIndex}
              className={index === activeIndex ? 'active' : ''}
              onClick={() => handleSelect(suggestion)}
            >
              {highlightMatch(suggestion, query)}
            </li>
          ))}
          {suggestions.length === 0 && query && (
            <li className="no-results">No results found</li>
          )}
        </ul>
      )}
    </div>
  );
}
```

### Pattern 2: Data Table with Sort/Filter

```tsx
interface Column<T> {
  key: string;
  header: string;
  sortable?: boolean;
  render?: (item: T) => React.ReactNode;
}

interface TableProps<T> {
  data: T[];
  columns: Column<T>[];
  pageSize?: number;
}

function DataTable<T extends { id: string }>({ data, columns, pageSize = 10 }: TableProps<T>) {
  const [sortKey, setSortKey] = useState('');
  const [sortDir, setSortDir] = useState<'asc' | 'desc'>('asc');
  const [page, setPage] = useState(1);
  const [search, setSearch] = useState('');
  
  const filtered = useMemo(() => {
    if (!search) return data;
    return data.filter(item =>
      columns.some(col =>
        String(item[col.key as keyof T])
          .toLowerCase()
          .includes(search.toLowerCase())
      )
    );
  }, [data, search, columns]);
  
  const sorted = useMemo(() => {
    if (!sortKey) return filtered;
    return [...filtered].sort((a, b) => {
      const aVal = a[sortKey as keyof T];
      const bVal = b[sortKey as keyof T];
      const cmp = String(aVal).localeCompare(String(bVal));
      return sortDir === 'asc' ? cmp : -cmp;
    });
  }, [filtered, sortKey, sortDir]);
  
  const paginated = sorted.slice((page - 1) * pageSize, page * pageSize);
  const totalPages = Math.ceil(sorted.length / pageSize);
  
  const handleSort = (key: string) => {
    if (sortKey === key) {
      setSortDir(prev => prev === 'asc' ? 'desc' : 'asc');
    } else {
      setSortKey(key);
      setSortDir('asc');
    }
  };
  
  return (
    <div className="data-table">
      <div className="table-controls">
        <input
          placeholder="Search..."
          value={search}
          onChange={(e) => { setSearch(e.target.value); setPage(1); }}
        />
      </div>
      <table>
        <thead>
          <tr>
            {columns.map(col => (
              <th
                key={col.key}
                onClick={() => col.sortable && handleSort(col.key)}
                className={col.sortable ? 'sortable' : ''}
              >
                {col.header}
                {sortKey === col.key && (sortDir === 'asc' ? ' ▲' : ' ▼')}
              </th>
            ))}
          </tr>
        </thead>
        <tbody>
          {paginated.length === 0 ? (
            <tr><td colSpan={columns.length}>No data found</td></tr>
          ) : (
            paginated.map(item => (
              <tr key={item.id}>
                {columns.map(col => (
                  <td key={col.key}>
                    {col.render ? col.render(item) : String(item[col.key as keyof T])}
                  </td>
                ))}
              </tr>
            ))
          )}
        </tbody>
      </table>
      <div className="pagination">
        <button disabled={page === 1} onClick={() => setPage(p => p - 1)}>Previous</button>
        <span>Page {page} of {totalPages}</span>
        <button disabled={page === totalPages} onClick={() => setPage(p => p + 1)}>Next</button>
      </div>
    </div>
  );
}
```

### Pattern 3: Modal / Dialog

```tsx
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: React.ReactNode;
  size?: 'sm' | 'md' | 'lg';
}

function Modal({ isOpen, onClose, title, children, size = 'md' }: ModalProps) {
  const modalRef = useRef<HTMLDivElement>(null);
  
  // Close on Escape
  useEffect(() => {
    const handler = (e: KeyboardEvent) => {
      if (e.key === 'Escape') onClose();
    };
    if (isOpen) document.addEventListener('keydown', handler);
    return () => document.removeEventListener('keydown', handler);
  }, [isOpen, onClose]);
  
  // Focus trap
  useEffect(() => {
    if (isOpen) modalRef.current?.focus();
  }, [isOpen]);
  
  // Prevent body scroll
  useEffect(() => {
    if (isOpen) {
      document.body.style.overflow = 'hidden';
      return () => { document.body.style.overflow = ''; };
    }
  }, [isOpen]);
  
  if (!isOpen) return null;
  
  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div
        ref={modalRef}
        className={`modal modal-${size}`}
        onClick={(e) => e.stopPropagation()}
        role="dialog"
        aria-modal="true"
        aria-labelledby="modal-title"
        tabIndex={-1}
      >
        <div className="modal-header">
          <h2 id="modal-title">{title}</h2>
          <button onClick={onClose} aria-label="Close">&times;</button>
        </div>
        <div className="modal-body">{children}</div>
      </div>
    </div>,
    document.body
  );
}
```

---

## 4. Practice Problems

### Problem 1: Kanban Board (60 min)

**Requirements:**
- 3 columns: To Do, In Progress, Done
- Add new tasks with title
- Drag and drop between columns
- Edit and delete tasks
- Persist in localStorage

**Solution approach:**
```typescript
// Types
type Status = 'todo' | 'in-progress' | 'done';

interface Task {
  id: string;
  title: string;
  status: Status;
  createdAt: number;
}

// State
const [tasks, setTasks] = useState<Task[]>(() => {
  const saved = localStorage.getItem('kanban-tasks');
  return saved ? JSON.parse(saved) : [];
});

// Save to localStorage
useEffect(() => {
  localStorage.setItem('kanban-tasks', JSON.stringify(tasks));
}, [tasks]);

// Drag and drop
const handleDrop = (taskId: string, newStatus: Status) => {
  setTasks(prev =>
    prev.map(task =>
      task.id === taskId ? { ...task, status: newStatus } : task
    )
  );
};
```

### Problem 2: Typeahead/Autocomplete (45 min)

**Requirements:**
- Debounced search input
- Dropdown suggestions
- Keyboard navigation
- Highlight matched text
- Async data fetching
- Cache results

**Solution approach:**
```typescript
// Custom hook for typeahead
function useTypeahead(fetchFn: (query: string) => Promise<string[]>) {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState<string[]>([]);
  const [loading, setLoading] = useState(false);
  const [cache, setCache] = useState<Map<string, string[]>>(new Map());
  
  const debouncedFetch = useMemo(
    () => debounce(async (q: string) => {
      if (q.length < 2) { setResults([]); return; }
      
      if (cache.has(q)) {
        setResults(cache.get(q)!);
        return;
      }
      
      setLoading(true);
      try {
        const data = await fetchFn(q);
        setCache(prev => new Map(prev).set(q, data));
        setResults(data);
      } finally {
        setLoading(false);
      }
    }, 300),
    [fetchFn, cache]
  );
  
  const handleChange = (value: string) => {
    setQuery(value);
    debouncedFetch(value);
  };
  
  return { query, results, loading, handleChange };
}
```

### Problem 3: Nested Comments (60 min)

**Requirements:**
- Display nested comment threads
- Add reply to any comment
- Delete comments
- Collapse/expand threads
- Like/unlike comments

**Solution approach:**
```typescript
interface Comment {
  id: string;
  text: string;
  author: string;
  createdAt: number;
  likes: number;
  liked: boolean;
  replies: Comment[];
}

function CommentThread({ comment, depth = 0 }: { comment: Comment; depth: number }) {
  const [collapsed, setCollapsed] = useState(false);
  const [showReply, setShowReply] = useState(false);
  
  return (
    <div className="comment" style={{ marginLeft: depth * 24 }}>
      <div className="comment-header">
        <strong>{comment.author}</strong>
        <span>{formatTime(comment.createdAt)}</span>
      </div>
      {!collapsed && <p className="comment-text">{comment.text}</p>}
      <div className="comment-actions">
        <button onClick={handleLike}>{comment.liked ? 'Unlike' : 'Like'} ({comment.likes})</button>
        <button onClick={() => setShowReply(!showReply)}>Reply</button>
        <button onClick={() => setCollapsed(!collapsed)}>
          {collapsed ? 'Expand' : 'Collapse'}
        </button>
        <button onClick={handleDelete}>Delete</button>
      </div>
      {showReply && <ReplyForm onSubmit={handleAddReply} />}
      {comment.replies.map(reply => (
        <CommentThread key={reply.id} comment={reply} depth={depth + 1} />
      ))}
    </div>
  );
}
```

### Problem 4: Star Rating Component (30 min)

**Requirements:**
- Display rating with stars (1-5)
- Interactive (click to rate)
- Hover preview
- Half-star support
- Keyboard accessible
- Display current rating text

**Solution approach:**
```typescript
interface StarRatingProps {
  value: number;
  onChange: (value: number) => void;
  maxStars?: number;
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
}

function StarRating({ value, onChange, maxStars = 5, disabled = false }: StarRatingProps) {
  const [hoverValue, setHoverValue] = useState(0);
  
  const handleMouseMove = (starIndex: number, e: React.MouseEvent) => {
    if (disabled) return;
    const rect = e.currentTarget.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const isHalf = x < rect.width / 2;
    setHoverValue(isHalf ? starIndex - 0.5 : starIndex);
  };
  
  const handleClick = (starIndex: number, e: React.MouseEvent) => {
    if (disabled) return;
    const rect = e.currentTarget.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const isHalf = x < rect.width / 2;
    onChange(isHalf ? starIndex - 0.5 : starIndex);
  };
  
  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (disabled) return;
    if (e.key === 'ArrowRight' && value < maxStars) {
      onChange(value + 1);
    } else if (e.key === 'ArrowLeft' && value > 0.5) {
      onChange(value - 0.5);
    }
  };
  
  const displayValue = hoverValue || value;
  
  return (
    <div
      className="star-rating"
      role="slider"
      aria-label="Rating"
      aria-valuemin={0.5}
      aria-valuemax={maxStars}
      aria-valuenow={displayValue}
      tabIndex={0}
      onKeyDown={handleKeyDown}
    >
      {Array.from({ length: maxStars }, (_, i) => {
        const starIndex = i + 1;
        const filled = displayValue >= starIndex;
        const half = !filled && displayValue >= starIndex - 0.5;
        
        return (
          <span
            key={i}
            className={`star ${filled ? 'filled' : ''} ${half ? 'half' : ''}`}
            onMouseMove={(e) => handleMouseMove(starIndex, e)}
            onClick={(e) => handleClick(starIndex, e)}
            onMouseLeave={() => setHoverValue(0)}
          >
            {filled ? '\u2605' : half ? '\u2605' : '\u2606'}
          </span>
        );
      })}
      <span className="rating-text">
        {LABELS[Math.round(displayValue)] || ''}
      </span>
    </div>
  );
}
```

### Problem 5: File Explorer (75 min)

**Requirements:**
- Tree view of files and folders
- Expand/collapse folders
- Create new file/folder
- Rename items
- Delete items
- Search by name
- Drag to move

**Solution approach:**
```typescript
interface TreeNode {
  id: string;
  name: string;
  type: 'file' | 'folder';
  children?: TreeNode[];
}

function FileExplorer() {
  const [tree, setTree] = useState<TreeNode[]>(initialTree);
  const [expanded, setExpanded] = useState<Set<string>>(new Set());
  const [search, setSearch] = useState('');
  
  const toggleExpand = (id: string) => {
    setExpanded(prev => {
      const next = new Set(prev);
      next.has(id) ? next.delete(id) : next.add(id);
      return next;
    });
  };
  
  const addNode = (parentId: string, type: 'file' | 'folder') => {
    const name = prompt(`Enter ${type} name:`);
    if (!name) return;
    
    const newNode: TreeNode = {
      id: crypto.randomUUID(),
      name,
      type,
      children: type === 'folder' ? [] : undefined,
    };
    
    const addRecursive = (nodes: TreeNode[]): TreeNode[] =>
      nodes.map(node => {
        if (node.id === parentId && node.children) {
          return { ...node, children: [...node.children, newNode] };
        }
        if (node.children) {
          return { ...node, children: addRecursive(node.children) };
        }
        return node;
      });
    
    setTree(addRecursive(tree));
  };
  
  const deleteNode = (id: string) => {
    const deleteRecursive = (nodes: TreeNode[]): TreeNode[] =>
      nodes
        .filter(node => node.id !== id)
        .map(node => ({
          ...node,
          children: node.children ? deleteRecursive(node.children) : undefined,
        }));
    
    setTree(deleteRecursive(tree));
  };
  
  const renameNode = (id: string) => {
    const newName = prompt('Enter new name:');
    if (!newName) return;
    
    const renameRecursive = (nodes: TreeNode[]): TreeNode[] =>
      nodes.map(node => ({
        ...node,
        name: node.id === id ? newName : node.name,
        children: node.children ? renameRecursive(node.children) : undefined,
      }));
    
    setTree(renameRecursive(tree));
  };
  
  const findNodes = (nodes: TreeNode[], query: string): TreeNode[] => {
    return nodes.reduce<TreeNode[]>((acc, node) => {
      const matches = node.name.toLowerCase().includes(query.toLowerCase());
      const childMatches = node.children ? findNodes(node.children, query) : [];
      
      if (matches || childMatches.length > 0) {
        acc.push({
          ...node,
          children: childMatches.length > 0 ? childMatches : node.children,
        });
      }
      return acc;
    }, []);
  };
  
  const displayedTree = search ? findNodes(tree, search) : tree;
  
  return (
    <div className="file-explorer">
      <div className="toolbar">
        <input
          placeholder="Search files..."
          value={search}
          onChange={(e) => setSearch(e.target.value)}
        />
        <button onClick={() => addNode('root', 'folder')}>New Folder</button>
        <button onClick={() => addNode('root', 'file')}>New File</button>
      </div>
      <TreeNodeComponent
        nodes={displayedTree}
        expanded={expanded}
        onToggle={toggleExpand}
        onDelete={deleteNode}
        onRename={renameNode}
        onAddFolder={(id) => addNode(id, 'folder')}
        onAddFile={(id) => addNode(id, 'file')}
        depth={0}
      />
    </div>
  );
}
```

---

## 5. Testing Strategy

```typescript
// Quick test setup (if time permits)
import { render, screen, fireEvent } from '@testing-library/react';

test('autocomplete shows suggestions on input', async () => {
  render(<Autocomplete options={['Apple', 'Banana', 'Orange']} />);
  
  const input = screen.getByRole('combobox');
  fireEvent.change(input, { target: { value: 'Ap' } });
  
  await screen.findByText('Apple');
  expect(screen.getByText('Apple')).toBeInTheDocument();
});
```

## 6. Time Management

```
Total: 60-90 minutes

Planning:     5-10 min  (requirements, design)
Setup:         5 min    (scaffold, structure)
Core:         30-40 min (features)
Polish:       10-15 min (edge cases, UX)
Review:        5-10 min (cleanup, test)
```

## 7. Communication Tips

- **Think aloud** - Explain your approach before coding
- **Ask questions** - Clarify ambiguous requirements
- **State trade-offs** - "I could use Context here, but for this scale, prop drilling is simpler"
- **Admit gaps** - "I haven't implemented virtualization, but I would use react-window for this"
- **Show progress** - "Let me get the basic flow working first, then add polish"
