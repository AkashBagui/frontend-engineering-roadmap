# Meta (Facebook) Frontend Interview Guide

## Interview Process

Meta's frontend interview process:

1. **Recruiter Call** (30 min) - Background check, process overview
2. **Phone Screen** (45-60 min) - Coding (via CoderPad)
3. **On-site** (4-5 rounds):
   - **Coding Round 1** (45 min) - Algorithmic problems (LeetCode medium)
   - **Coding Round 2** (45 min) - React/frontend coding
   - **System Design** (45 min) - Frontend architecture design
   - **Behavioral** (45 min) - "Meta-specific" product sense + leadership
   - **Frontend Deep Dive** (45 min) - CSS, performance, accessibility

## Frontend-Specific Focus

### 1. React Knowledge (Deep)
Meta uses React extensively. Expect deep questions:

```javascript
// Be able to explain and implement:
// - Virtual DOM reconciliation
// - Fiber architecture
// - useState vs useReducer internals
// - useEffect cleanup and timing
// - useMemo/useCallback optimization
// - Context API and its pitfalls
// - Render props vs Hooks patterns
// - Error boundaries
// - Portals
// - Refs and forwardRef

// Example deep question:
// "How does React batch state updates and when does it flush?"
function BatchExample() {
  const [count, setCount] = useState(0);
  const [flag, setFlag] = useState(false);
  
  function handleClick() {
    // React 18: Both batched (single render)
    // React 17: Batched only in event handlers
    setCount(c => c + 1);
    setFlag(f => !f);
  }
  
  useEffect(() => {
    // Runs once per render (after both updates in React 18)
  }, [count, flag]);
  
  return <button onClick={handleClick}>Click</button>;
}
```

### 2. Performance Optimization

```javascript
// Meta cares deeply about performance at scale

// 1. List virtualization
import { FixedSizeList } from 'react-window';

function Feed({ posts }) {
  return (
    <FixedSizeList
      height={window.innerHeight}
      itemCount={posts.length}
      itemSize={500}
      overscanCount={3}
    >
      {({ index, style }) => <PostCard post={posts[index]} style={style} />}
    </FixedSizeList>
  );
}

// 2. Bundle optimization
const HeavyComponent = React.lazy(() => import('./HeavyComponent'));

// 3. Profiling
<React.Profiler id="Feed" onRender={(id, phase, actualDuration) => {
  logPerformance(id, phase, actualDuration);
}}>
  <Feed />
</React.Profiler>
```

### 3. State Management at Scale

**Expect questions on:**
- When to use Context vs Redux vs local state
- Avoiding unnecessary re-renders in large apps
- Normalized state shapes (like Redux or Zustand)
- Optimistic updates and rollbacks
- State persistence and hydration

## Common Coding Problems

### Problem 1: Implement Infinite Scroll
```typescript
function useInfiniteScroll(fetchFn: (page: number) => Promise<any[]>) {
  const [data, setData] = useState<any[]>([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);
  const observer = useRef<IntersectionObserver>();
  
  const lastElementRef = useCallback((node: HTMLElement | null) => {
    if (loading) return;
    if (observer.current) observer.current.disconnect();
    
    observer.current = new IntersectionObserver(entries => {
      if (entries[0].isIntersecting && hasMore) {
        setPage(prev => prev + 1);
      }
    });
    
    if (node) observer.current.observe(node);
  }, [loading, hasMore]);
  
  useEffect(() => {
    setLoading(true);
    fetchFn(page).then(newData => {
      setData(prev => [...prev, ...newData]);
      setHasMore(newData.length > 0);
      setLoading(false);
    });
  }, [page]);
  
  return { data, loading, hasMore, lastElementRef };
}
```

### Problem 2: Build a Polling Component
```typescript
function usePolling(callback: () => Promise<void>, interval: number, enabled: boolean) {
  const savedCallback = useRef(callback);
  
  useEffect(() => {
    savedCallback.current = callback;
  }, [callback]);
  
  useEffect(() => {
    if (!enabled) return;
    
    savedCallback.current();
    const id = setInterval(() => savedCallback.current(), interval);
    return () => clearInterval(id);
  }, [interval, enabled]);
}
```

## Preparation Strategy

### Frontend fundamentals:
- Master React hooks (useState, useEffect, useMemo, useCallback, useRef, useReducer)
- Understand reconciliation and Fiber
- CSS: Flexbox, Grid, animations, responsive design
- Performance: Core Web Vitals, Lighthouse, bundle analysis
- TypeScript: Generics, utility types, advanced patterns

### System Design questions:
- Design Facebook News Feed
- Design Facebook Messenger (chat)
- Design Facebook Photos (image gallery)
- Design Instagram Stories
- Design a real-time comments system

### Behavioral ("Meta-specific"):
Meta's behavioral questions focus on:
- **Impact** - How did your work affect users/business?
- **Move fast** - Speed vs quality decisions
- **Be open** - Receiving and giving feedback
- **Build social value** - Products that connect people

**Sample questions:**
- Tell me about a time you had to make a trade-off between speed and quality
- Describe a project that had significant impact
- How do you handle feedback on your code?
- Tell me about a time you influenced a technical decision

## Tips from Interviewees

- **Communication is key** - Talk through your approach before coding
- **Code quality matters** - Clean, readable, well-structured code
- **React depth** - Be ready for "how does this work internally?" questions
- **Performance mindset** - Always consider performance implications
- **Product sense** - Understand how your decisions affect the user experience
- **Expect follow-ups** - "How would this scale to millions of users?"

## Common Mistakes

- Not knowing React internals (Virtual DOM, Fiber, reconciliation)
- Weak CSS skills (layout, animations, responsive design)
- Not considering performance in design questions
- Forgetting about error handling and loading states
- Not preparing for behavioral questions specific to Meta
