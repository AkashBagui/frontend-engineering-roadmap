# React Practice Projects

---

## Project 1: Todo App

**Difficulty:** Beginner | **Concepts:** useState, props, forms, lifting state

### Requirements
- Add new todos via text input + button
- Mark todos as completed (toggle)
- Delete individual todos
- Filter: All / Active / Completed
- Show count of remaining items
- Clear completed button
- Persist todos to localStorage

### Architecture

```
┌─────────────────────────────────────────────────┐
│                   App                           │
│  ┌──────────────┐  ┌────────────────────────┐  │
│  │   TodoForm   │  │     TodoList           │  │
│  │  (input +    │  │  ┌──────────────────┐  │  │
│  │   add btn)   │  │  │   TodoItem        │  │  │
│  └──────────────┘  │  │  [☐ Task text] ✕  │  │  │
│                    │  │  [☑ Done text]  ✕  │  │  │
│  ┌──────────────┐  │  └──────────────────┘  │  │
│  │  TodoFilter  │  └────────────────────────┘  │
│  │ [All|Act|Cmp]│                               │
│  │ Items left: 3│                               │
│  └──────────────┘                               │
└─────────────────────────────────────────────────┘
```

### Component Tree

```
App
 ├─ TodoForm
 │    └─ input[type=text] + button
 ├─ TodoList
 │    └─ TodoItem[] (mapped from filtered todos)
 └─ TodoFilter
      └─ filter buttons + count display
```

### Step-by-Step

1. **Setup:** Scaffold with Vite (`npm create vite@latest todo-app -- --template react`)
2. **State in App:** `useState` for `todos` array and `filter` string
3. **TodoForm:** Controlled input, calls `onAdd(text)` from props
4. **TodoList:** Maps todos to `TodoItem`, passes `onToggle(id)` and `onDelete(id)`
5. **TodoItem:** Displays checkbox, text (with conditional strikethrough), delete button
6. **TodoFilter:** Buttons for All/Active/Completed, shows remaining count
7. **localStorage:** `useEffect` to save on change, lazy initializer for load
8. **Polish:** Empty state message, keyboard submit (Enter key)

### Key Code Snippets

```jsx
// Lazy initializer for localStorage
const [todos, setTodos] = useState(() => {
  const saved = localStorage.getItem('todos');
  return saved ? JSON.parse(saved) : [];
});

// Toggle completion
const toggleTodo = (id) => {
  setTodos(prev => prev.map(t => t.id === id ? { ...t, completed: !t.completed } : t));
};
```

---

## Project 2: Notes App

**Difficulty:** Beginner-Intermediate | **Concepts:** useReducer, context, routing, markdown

### Requirements
- Create, edit, delete notes
- Each note has title, content, tags, created/updated dates
- Preview rendered markdown
- Search notes by title/tags
- Tag filtering sidebar
- Responsive layout (sidebar + editor)
- No external state library (useReducer + Context)

### Architecture

```
┌────────────────────────────────────────────────────────┐
│                        App                             │
│  ┌──────────────┐  ┌────────────────────────────────┐ │
│  │ Sidebar      │  │  Main Panel                    │ │
│  │  ┌────────┐  │  │  ┌──────────┐ ┌─────────────┐ │ │
│  │  │ Search │  │  │  │ NoteList │ │ NoteEditor  │ │ │
│  │  └────────┘  │  │  │ (cards)  │ │ (title,     │ │ │
│  │  ┌────────┐  │  │  └──────────┘ │  content,   │ │ │
│  │  │ Tags   │  │  │               │  markdown   │ │ │
│  │  └────────┘  │  │               │  preview)   │ │ │
│  │  ┌────────┐  │  │               └─────────────┘ │ │
│  │  │ + New  │  │  └────────────────────────────────┘ │
│  │  └────────┘  │                                     │
│  └──────────────┘                                     │
└────────────────────────────────────────────────────────┘
```

### Component Tree

```
App (AppProvider — context)
 ├─ Sidebar
 │    ├─ SearchBar
 │    ├─ TagList
 │    └─ NewNoteButton
 └─ MainPanel
      ├─ NoteList
      │    └─ NoteCard[]
      └─ NoteEditor
           ├─ TitleInput
           ├─ ContentTextarea
           └─ MarkdownPreview
```

### Step-by-Step

1. **Setup:** Vite + React Router for URL-based note selection
2. **State:** `useReducer` with actions: ADD, UPDATE, DELETE, SET_FILTER
3. **Context:** `NotesContext` providing state + dispatch to all children
4. **Sidebar:** Search filters notes by title, TagList shows unique tags with counts
5. **NoteList:** Displays filtered note cards, sorted by updated date
6. **NoteEditor:** Two-pane (edit + preview), auto-saves with debounce
7. **Markdown:** Use `react-markdown` for rendering, `remark-gfm` for tables
8. **Router:** `/` shows list, `/note/:id` opens editor, `/new` creates note
9. **Persistence:** Save to localStorage via effect

### Key Code Snippets

```jsx
// Reducer
function notesReducer(state, action) {
  switch (action.type) {
    case 'ADD':
      return { ...state, notes: [action.payload, ...state.notes] };
    case 'UPDATE':
      return { ...state, notes: state.notes.map(n =>
        n.id === action.payload.id ? { ...n, ...action.payload, updatedAt: Date.now() } : n
      )};
    case 'DELETE':
      return { ...state, notes: state.notes.filter(n => n.id !== action.payload) };
    default:
      return state;
  }
}
```

---

## Project 3: Movie Explorer

**Difficulty:** Intermediate | **Concepts:** custom hooks, API calls, pagination, debounce, React Router

### Requirements
- Search movies by title (debounced)
- Display results as grid of cards with poster, title, year, type
- Pagination or infinite scroll
- Movie detail page with full info, ratings, cast
- Favorites list (localStorage)
- Filter by type (movie/series/episode)
- Loading skeletons, error states, empty states
- Responsive design

### API

Use [OMDb API](http://www.omdbapi.com/) (free tier, key required) or [TMDB API](https://developers.themoviedb.org/3).

### Architecture

```
┌─────────────────────────────────────────────────────────┐
│                         App                             │
│  ┌─────────────┐  ┌─────────────────────────────────┐  │
│  │  Header     │  │  Routes                         │  │
│  │  (logo,     │  │  ┌──────────┐  ┌─────────────┐  │  │
│  │   search)   │  │  │ HomePage │  │ MoviePage   │  │  │
│  └─────────────┘  │  │ ┌──────┐ │  │ ┌────────┐  │  │  │
│                   │  │ │Grid  │ │  │ │ Details│  │  │  │
│  ┌─────────────┐  │  │ │Card[]│ │  │ └────────┘  │  │  │
│  │  Favorites  │  │  │ └──────┘ │  └─────────────┘  │  │
│  └─────────────┘  │  │ Pagination│                   │  │
│                   │  └──────────┘                   │  │
└─────────────────────────────────────────────────────────┘
```

### Component Tree

```
App
 ├─ Header (search input + logo)
 ├─ FavoritesSidebar (or page)
 └─ Routes
      ├─ HomePage
      │    ├─ MovieGrid
      │    │    └─ MovieCard[] (poster, title, year, favorite btn)
      │    ├─ Pagination / LoadMore
      │    └─ SearchFilters (type dropdown)
      └─ MoviePage
           ├─ MovieHero (backdrop, poster, title, ratings)
           ├─ MovieDetails (plot, cast, director, runtime)
           └─ FavoriteButton
```

### Custom Hooks to Build

```jsx
// useDebounce
function useDebounce(value, delay = 500) {
  const [debounced, setDebounced] = useState(value);
  useEffect(() => {
    const timer = setTimeout(() => setDebounced(value), delay);
    return () => clearTimeout(timer);
  }, [value, delay]);
  return debounced;
}

// useMovies
function useMovies(query, page) {
  const [movies, setMovies] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  const [totalPages, setTotalPages] = useState(0);

  useEffect(() => {
    if (!query) return;
    const controller = new AbortController();
    setLoading(true);
    fetch(`/api?q=${query}&page=${page}`, { signal: controller.signal })
      .then(r => r.json())
      .then(data => { setMovies(data.Search); setTotalPages(Math.ceil(data.totalResults / 10)); })
      .catch(e => { if (e.name !== 'AbortError') setError(e.message); })
      .finally(() => setLoading(false));
    return () => controller.abort();
  }, [query, page]);

  return { movies, loading, error, totalPages };
}

// useFavorites
function useFavorites() {
  const [favorites, setFavorites] = useState(() => JSON.parse(localStorage.getItem('favs') || '[]'));
  useEffect(() => localStorage.setItem('favs', JSON.stringify(favorites)), [favorites]);
  const toggle = (movie) => setFavorites(prev =>
    prev.find(m => m.imdbID === movie.imdbID) ? prev.filter(m => m.imdbID !== movie.imdbID) : [...prev, movie]
  );
  const isFavorite = (id) => favorites.some(m => m.imdbID === id);
  return { favorites, toggle, isFavorite };
}
```

### Step-by-Step

1. **Setup:** Vite + React Router v6
2. **Search:** Controlled input → debounce → API call
3. **Grid:** Responsive CSS Grid of MovieCards
4. **Detail Page:** Route `/movie/:id`, fetch full movie data
5. **Pagination:** Page numbers or "Load More" button
6. **Favorites:** Context + localStorage for favorites persistence
7. **States:** Loading skeleton, error banner, empty state illustration
8. **Polish:** Animate card hover, star toggle for favorites, meta tags

---

## Project 4: Shopping Cart

**Difficulty:** Intermediate-Advanced | **Concepts:** useReducer, context splitting, compound components, performance optimization

### Requirements
- Browse products with categories and filters
- Product detail page with image gallery, description, reviews
- Add to cart with quantity selector
- Cart page: update quantities, remove items, coupon code
- Checkout form with validation
- Order summary with tax and shipping
- State persisted to localStorage
- Responsive with mobile-first design

### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                          App                                │
│  ┌────────────┐  ┌────────────────────────────────────────┐ │
│  │  Navbar    │  │  Routes                               │ │
│  │  (cart     │  │  ┌──────────┐ ┌────────┐ ┌────────┐  │ │
│  │   count)   │  │  │ Product  │ │Product │ │ Cart   │  │ │
│  └────────────┘  │  │ Listing  │ │ Detail │ │ Page   │  │ │
│                  │  │ ┌──────┐ │ │ ┌────┐ │ │ ┌────┐ │  │ │
│  ┌────────────┐  │  │ │Filter│ │ │ │Gall│ │ │ │Item│ │  │ │
│  │  Footer    │  │  │ └──────┘ │ │ └────┘ │ │ │s   │ │  │ │
│  └────────────┘  │  │ ┌──────┐ │ │ ┌────┐ │ │ │    │ │  │ │
│                  │  │ │Cards │ │ │ │Rev.│ │ │ │Form│ │  │ │
│                  │  │ └──────┘ │ │ └────┘ │ │ └────┘ │  │ │
│                  │  └──────────┘ └────────┘ └────────┘  │ │
│                  └────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### Context Structure (Split for Performance)

```
AppProvider
 ├─ CartContext     (items, addItem, removeItem, updateQty, total)
 ├─ ProductContext  (products, filters, categories)
 └─ UIContext       (cartOpen, mobileMenuOpen, notifications)
```

### Component Tree

```
App
 ├─ Navbar
 │    ├─ Logo
 │    ├─ NavLinks
 │    ├─ SearchBar
 │    └─ CartIcon (with badge count)
 ├─ Routes
 │    ├─ ProductListing
 │    │    ├─ FilterSidebar (categories, price range, ratings)
 │    │    ├─ SortDropdown
 │    │    └─ ProductGrid
 │    │         └─ ProductCard (image, name, price, add-to-cart btn)
 │    ├─ ProductDetail
 │    │    ├─ ImageGallery
 │    │    ├─ ProductInfo (name, price, description)
 │    │    ├─ QuantitySelector (compound component)
 │    │    ├─ AddToCartButton
 │    │    └─ ReviewSection
 │    │         └─ ReviewCard[]
 │    └─ CartPage
 │         ├─ CartItemList
 │         │    └─ CartItem (image, name, qty selector, price, remove)
 │         ├─ CouponInput
 │         ├─ OrderSummary (subtotal, shipping, tax, total)
 │         └─ CheckoutForm (validated with React Hook Form)
 └─ Footer
```

### Step-by-Step

1. **Setup:** Vite + React Router v6 + Tailwind or CSS Modules
2. **Data:** Mock products JSON or use Fake Store API
3. **ProductList:** Fetch products, filter by category/price/rating, sort
4. **ProductDetail:** Route `/product/:id`, image gallery with thumbnails
5. **CartContext:** useReducer for cart — ADD, REMOVE, UPDATE_QTY, APPLY_COUPON, CLEAR
6. **Cart Page:** List items, update quantity, remove. Calculate subtotal + tax (10%) + shipping (free over $50)
7. **Coupon:** Apply percentage discount (e.g., SAVE10 = 10%)
8. **Checkout:** React Hook Form with validation (name, email, address, card)
9. **Persistence:** useEffect to serialize cart to localStorage
10. **Performance:** React.memo on ProductCard, CartItem. useMemo for totals. Lazy load routes.

### Key Code Snippets

```jsx
// Cart reducer
function cartReducer(state, action) {
  switch (action.type) {
    case 'ADD_ITEM': {
      const existing = state.items.find(i => i.id === action.payload.id);
      return {
        ...state,
        items: existing
          ? state.items.map(i => i.id === existing.id ? { ...i, qty: i.qty + 1 } : i)
          : [...state.items, { ...action.payload, qty: 1 }],
      };
    }
    case 'REMOVE_ITEM':
      return { ...state, items: state.items.filter(i => i.id !== action.payload) };
    case 'UPDATE_QTY':
      return {
        ...state,
        items: state.items.map(i =>
          i.id === action.payload.id ? { ...i, qty: Math.max(1, action.payload.qty) } : i
        ),
      };
    case 'APPLY_COUPON':
      return { ...state, coupon: action.payload };
    case 'CLEAR':
      return { ...state, items: [], coupon: null };
    default:
      return state;
  }
}

// Memoized totals
const totals = useMemo(() => {
  const subtotal = items.reduce((sum, i) => sum + i.price * i.qty, 0);
  const discount = coupon ? subtotal * 0.1 : 0;
  const shipping = subtotal > 50 ? 0 : 5.99;
  return { subtotal, discount, shipping, total: subtotal - discount + shipping };
}, [items, coupon]);
```
