# Frontend System Design Interview

## 1. System Design Framework

### Phase 1: Requirements Gathering (5 min)

**Functional Requirements:**
- What features does the system need?
- Who are the users?
- What are the core actions users take?
- What are the edge cases?

**Non-Functional Requirements:**
- Performance: Load time, Time to Interactive, FPS
- Scalability: Number of users, data volume
- Reliability: Error handling, offline support
- Accessibility: WCAG standards
- Security: Auth, data protection
- Internationalization: Languages, locales
- Responsiveness: Device support

### Phase 2: Architecture Design (10 min)

**Component Architecture:**
```
┌─────────────────────────────────────────────┐
│                Application                   │
│  ┌─────────┐ ┌─────────┐ ┌───────────────┐  │
│  │  Shell   │ │  Auth   │ │    Router     │  │
│  └─────────┘ └─────────┘ └───────────────┘  │
│  ┌──────────────────────────────────────┐   │
│  │         Page Components              │   │
│  │  ┌──────┐ ┌──────┐ ┌─────────┐     │   │
│  │  │Feed  │ │Post  │ │Profile  │     │   │
│  │  └──────┘ └──────┘ └─────────┘     │   │
│  └──────────────────────────────────────┘   │
│  ┌──────────────────────────────────────┐   │
│  │       Shared Components             │   │
│  │  ┌──────┐ ┌──────┐ ┌─────────┐     │   │
│  │  │Button│ │Card  │ │Modal    │     │   │
│  │  └──────┘ └──────┘ └─────────┘     │   │
│  └──────────────────────────────────────┘   │
│  ┌──────────────────────────────────────┐   │
│  │         State / Data Layer          │   │
│  │  ┌─────────┐ ┌─────────┐ ┌────────┐ │   │
│  │  │  Store  │ │  Cache  │ │  API   │ │   │
│  │  └─────────┘ └─────────┘ └────────┘ │   │
│  └──────────────────────────────────────┘   │
└─────────────────────────────────────────────┘
```

**Data Flow Architecture:**
```
User Action → Component → State Update → API Call → Cache Update → Re-render
     ↑                                                          |
     └─────────────────── Optimistic UI ────────────────────────┘
```

### Phase 3: Component Tree (5 min)

```
App
├── Layout
│   ├── Header
│   │   ├── Logo
│   │   ├── Navigation
│   │   └── UserMenu
│   └── Main
│       ├── Sidebar
│       └── Content Area
└── Footer
```

### Phase 4: Data Flow & State Management (5 min)

**State Categories:**
- **Server State:** Data from API (use React Query, SWR)
- **UI State:** Modals, toasts, form inputs (use local state)
- **Global State:** User auth, theme, settings (use Context/Zustand)
- **URL State:** Query params, route params (use Router)

### Phase 5: API Design & Data Fetching (5 min)

```javascript
// REST API Design
GET    /api/posts?page=1&limit=20    // List posts
GET    /api/posts/:id                 // Single post
POST   /api/posts                     // Create post
PUT    /api/posts/:id                 // Update post
DELETE /api/posts/:id                 // Delete post

// GraphQL
query {
  posts(page: 1, limit: 20) {
    id, title, author { name }, comments { count }
  }
}

// Data fetching patterns
const { data, isLoading } = useQuery({
  queryKey: ['posts', page],
  queryFn: () => fetchPosts(page),
  staleTime: 5 * 60 * 1000, // 5 min cache
});
```

### Phase 6: Performance Considerations (5 min)

- **Bundle Size:** Code splitting, lazy loading, tree shaking
- **Images:** Next/Image, lazy loading, WebP, srcset
- **Rendering:** SSR/SSG/ISR decisions
- **Caching:** Service workers, CDN, HTTP caching
- **Virtualization:** React Window for long lists
- **Debouncing:** Search inputs, scroll handlers
- **Web Workers:** Heavy computations off main thread

---

## 2. Design Problem: Build a News Feed

### Requirements

**Functional:**
- Infinite scroll of posts
- Like, comment, share
- Image/video support
- Real-time updates
- Bookmark posts
- Filter by categories

**Non-Functional:**
- Load feed in < 2s
- Smooth scrolling (60 FPS)
- Offline support for cached posts
- Support 10M+ daily active users

### Architecture

```
┌─────────────────────────────────────┐
│         CDN (CloudFront)            │
├─────────────────────────────────────┤
│      Next.js Application            │
│  ┌───────────────────────────────┐  │
│  │    Feed Page (SSR/ISR)        │  │
│  │  ┌─────────┐ ┌───────────┐   │  │
│  │  │PostList │ │FeedFilter │   │  │
│  │  └────┬────┘ └───────────┘   │  │
│  │  ┌────┴──────────────────┐   │  │
│  │  │ PostCard (virtualized)│   │  │
│  │  │ ┌─────┐ ┌──────────┐ │   │  │
│  │  │ │Media│ │Actions   │ │   │  │
│  │  │ └─────┘ └──────────┘ │   │  │
│  │  └──────────────────────┘   │  │
│  └───────────────────────────────┘  │
├─────────────────────────────────────┤
│        Data Layer                   │
│  ┌──────────┐ ┌─────────┐          │
│  │SWR Cache │ │WebSocket│          │
│  └──────────┘ └─────────┘          │
└─────────────────────────────────────┘
```

### Component Tree

```
FeedPage
├── FeedFilter (category selector)
├── PostList (virtualized)
│   ├── PostCard (×N)
│   │   ├── PostHeader (avatar, name, time)
│   │   ├── PostMedia (image/video carousel)
│   │   ├── PostContent (text, hashtags)
│   │   ├── PostStats (likes, comments count)
│   │   └── PostActions (like, comment, share, bookmark)
│   └── InfiniteScrollTrigger
└── NewPostButton (FAB)
```

### Data Flow

```
1. FeedPage mounts → SSR renders initial posts
2. Client hydrates → SWR fetches fresh data
3. User scrolls → trigger load more (page+1)
4. User likes → optimistic update → API call → rollback on error
5. Real-time updates via WebSocket (new posts, like count changes)
6. Service worker caches feed data for offline access
```

### Performance Optimizations

```javascript
// 1. Virtualized list (only render visible items)
import { FixedSizeList } from 'react-window';

function PostList({ posts }) {
  return (
    <FixedSizeList
      height={window.innerHeight}
      itemCount={posts.length}
      itemSize={400}
    >
      {({ index, style }) => (
        <div style={style}>
          <PostCard post={posts[index]} />
        </div>
      )}
    </FixedSizeList>
  );
}

// 2. Image lazy loading with placeholders
<Image
  src={post.image}
  alt="Post image"
  loading="lazy"
  placeholder="blur"
  blurDataURL={post.blurHash}
/>

// 3. Prefetch next page
const observer = useRef();
const lastPostRef = useCallback((node) => {
  if (observer.current) observer.current.disconnect();
  observer.current = new IntersectionObserver((entries) => {
    if (entries[0].isIntersecting) fetchNextPage();
  });
  if (node) observer.current.observe(node);
}, []);

// 4. Debounced like update
const debouncedLike = useCallback(
  debounce((postId) => api.likePost(postId), 500),
  []
);

// 5. Optimistic updates
const { mutate } = useMutation({
  mutationFn: (postId) => api.likePost(postId),
  onMutate: async (postId) => {
    await queryClient.cancelQueries(['feed']);
    const previousFeed = queryClient.getQueryData(['feed']);
    queryClient.setQueryData(['feed'], (old) => ({
      ...old,
      posts: old.posts.map(p =>
        p.id === postId ? { ...p, liked: true, likes: p.likes + 1 } : p
      ),
    }));
    return { previousFeed };
  },
  onError: (err, postId, context) => {
    queryClient.setQueryData(['feed'], context.previousFeed);
  },
});
```

---

## 3. Design Problem: Build a Collaborative Document Editor

### Requirements

**Functional:**
- Rich text editing (bold, italic, headings, lists)
- Real-time collaboration (multiple users)
- Comments and suggestions
- Version history
- Auto-save

**Non-Functional:**
- < 100ms latency for keystrokes
- Conflict resolution (OT/CRDT)
- Offline editing support
- Support 50+ concurrent editors

### Architecture

```
┌─────────────┐ ┌─────────────┐
│  User A     │ │  User B     │
│  (Browser)  │ │  (Browser)  │
└──────┬──────┘ └──────┬──────┘
       │                │
       ▼                ▼
┌─────────────────────────────┐
│     WebSocket Server        │
│  (Socket.io / WebRTC)       │
│  ┌──────────────────────┐  │
│  │  CRDT/OT Operations  │  │
│  └──────────────────────┘  │
└─────────────┬───────────────┘
              │
              ▼
┌─────────────────────────────┐
│   Redis (ephemeral state)   │
├─────────────────────────────┤
│   PostgreSQL (persistent)   │
└─────────────────────────────┘
```

### Key Components

```
EditorPage
├── Toolbar
│   ├── TextFormatting (bold, italic, underline)
│   ├── HeadingSelect
│   ├── ListButtons
│   └── CollaboratorAvatars
├── Editor (ProseMirror/Slate)
│   ├── DocumentNode
│   ├── CursorOverlay
│   └── SelectionHighlights
├── Sidebar
│   ├── TableOfContents
│   ├── Comments
│   └── VersionHistory
└── StatusBar (saved/editing status)
```

### Conflict Resolution (CRDT)

```javascript
// Operational Transformation approach
class Operation {
  constructor(type, position, data, userId, timestamp) {
    this.type = type; // insert, delete, format
    this.position = position;
    this.data = data;
    this.userId = userId;
    this.timestamp = timestamp;
  }
}

// Apply operations with position transformation
function applyOperation(doc, op) {
  switch (op.type) {
    case 'insert':
      // Transform position based on concurrent operations
      doc.splice(op.position, 0, op.data);
      break;
    case 'delete':
      doc.splice(op.position, op.data.length);
      break;
  }
}

// Local operations applied immediately (optimistic)
// Server broadcasts operations to other clients
// Other clients apply transformed operations
```

---

## 4. Design Problem: Build an E-commerce Product Listing Page

### Requirements

**Functional:**
- Product grid with filtering
- Search with autocomplete
- Product categories
- Price range filter
- Sort (price, popularity, rating)
- Product quick view
- Add to cart

**Non-Functional:**
- < 1s initial page load
- Smooth filtering (no page reload)
- SEO optimized
- Support 100K+ products

### Architecture

```
┌─────────────────────────────────────────────┐
│          CDN (CloudFront)                    │
├─────────────────────────────────────────────┤
│     Next.js (ISR + Client-side)             │
│  ┌──────────────────────────────────────┐   │
│  │  Product Listing Page                │   │
│  │  - Static generation for categories  │   │
│  │  - Client-side filtering/sorting     │   │
│  │  - URL-based filter state            │   │
│  └──────────────────────────────────────┘   │
├─────────────────────────────────────────────┤
│  ┌──────────┐  ┌──────────┐ ┌───────────┐  │
│  │ Product  │  │ Search   │ │ Analytics │  │
│  │ Service  │  │ Service  │ │ Service   │  │
│  └──────────┘  └──────────┘ └───────────┘  │
└─────────────────────────────────────────────┘
```

### Component Tree

```
SearchPage
├── SearchBar (debounced input + suggestions)
├── FilterPanel
│   ├── CategoryFilter (multi-select)
│   ├── PriceRange (slider)
│   ├── RatingFilter
│   └── BrandFilter
├── SortBar (sort options + result count)
├── ProductGrid
│   └── ProductCard (×N)
│       ├── ProductImage (with zoom)
│       ├── ProductName
│       ├── ProductPrice (with discount)
│       ├── RatingStars
│       ├── AddToCartButton
│       └── QuickViewButton
├── Pagination / InfiniteScroll
└── MobileFilterDrawer
```

### State Management

```javascript
// URL-driven state (shareable, bookmarkable)
const [searchParams, setSearchParams] = useSearchParams();

const filters = {
  category: searchParams.get('category')?.split(',') || [],
  minPrice: Number(searchParams.get('minPrice')) || 0,
  maxPrice: Number(searchParams.get('maxPrice')) || 10000,
  rating: Number(searchParams.get('rating')) || 0,
  sort: searchParams.get('sort') || 'popularity',
  page: Number(searchParams.get('page')) || 1,
};

// Update URL on filter change (pushes state)
function updateFilter(key, value) {
  const params = new URLSearchParams(searchParams);
  params.set(key, value);
  setSearchParams(params, { replace: false });
}

// Data fetching based on URL params
const { data, isLoading } = useQuery({
  queryKey: ['products', filters],
  queryFn: () => fetchProducts(filters),
  staleTime: 5 * 60 * 1000,
  keepPreviousData: true, // Keep old data while loading new
});
```

---

## 5. Design Problem: Build a Real-Time Analytics Dashboard

### Requirements

**Functional:**
- Real-time metrics (active users, page views)
- Charts (line, bar, pie)
- Date range selector
- Filter by dimension (device, location, page)
- Export to CSV/PDF
- Custom dashboard layout

**Non-Functional:**
- Real-time updates (< 1s delay)
- Handle 100K+ events/second
- Interactive charts (60 FPS)
- Drill-down capabilities

### Architecture

```
┌──────────────┐    ┌──────────────┐
│  User Events │───▶│  Event Bus   │
│  (Browser)   │    │  (Kafka)     │
└──────────────┘    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │  Stream      │
                    │  Processor   │
                    │  (Flink)     │
                    └──────┬───────┘
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
       ┌──────────────┐         ┌──────────────┐
       │  TimescaleDB │         │  Redis       │
       │  (historical)│         │  (real-time) │
       └──────────────┘         └──────┬───────┘
                                       │
                                       ▼
                              ┌──────────────────┐
                              │  WebSocket API   │
                              │  (Socket.io)     │
                              └────────┬─────────┘
                                       │
                                       ▼
                              ┌──────────────────┐
                              │  Dashboard UI    │
                              │  (React + D3)    │
                              └──────────────────┘
```

### Component Tree

```
Dashboard
├── DashboardHeader
│   ├── DateRangePicker
│   ├── GlobalFilters (device, location, page)
│   └── RefreshInterval
├── DashboardGrid (resizable, draggable)
│   ├── MetricCard (×4)
│   │   ├── MetricValue (animated counter)
│   │   ├── MetricLabel
│   │   └── Sparkline (mini chart)
│   ├── LineChart (revenue over time)
│   ├── BarChart (page views by page)
│   ├── PieChart (traffic by source)
│   ├── GeoMap (user locations)
│   ├── DataTable (top pages/users)
│   └── FunnelChart (conversion funnel)
└── DashboardFooter (last updated, export button)
```

### Real-Time Updates

```javascript
// WebSocket connection
const socket = useMemo(() => io(WS_URL, {
  transports: ['websocket'],
  query: { dashboardId },
}), [dashboardId]);

// Subscribe to metrics
useEffect(() => {
  socket.emit('subscribe', { metrics: ['activeUsers', 'pageViews'] });
  
  socket.on('metricUpdate', (data) => {
    queryClient.setQueryData(['metrics', data.name], (old) => ({
      ...old,
      value: data.value,
      timestamp: data.timestamp,
    }));
  });
  
  socket.on('newEvent', (event) => {
    // Animate in new data point
    queryClient.setQueryData(['timeSeries'], (old) => ({
      ...old,
      points: [...old.points.slice(-100), event],
    }));
  });
  
  return () => { socket.disconnect(); };
}, [socket]);

// Chart component with smooth transitions
function RealTimeChart({ data }) {
  return (
    <ResponsiveContainer>
      <LineChart data={data}>
        <Line
          type="monotone"
          dataKey="value"
          animationDuration={300}
          dot={false}
        />
      </LineChart>
    </ResponsiveContainer>
  );
}
```
