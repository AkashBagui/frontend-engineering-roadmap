# Amazon Frontend Interview Guide

## Interview Process

Amazon's frontend interview process:

1. **OA (Online Assessment)** - 2 coding problems + work style assessment
2. **Phone Screen** (60 min) - Coding + LP questions (Leadership Principles)
3. **On-site Loop** (4-5 rounds):
   - **Coding** (60 min) - Data structures, algorithms
   - **Frontend Coding** (60 min) - HTML/CSS/JavaScript, React
   - **System Design** (60 min) - Frontend architecture
   - **Bar Raiser** (60 min) - Behavioral/LP deep dive
   - **Hiring Manager** (45 min) - Experience, team fit

## Leadership Principles (14 LPs)

Amazon interviews are driven by Leadership Principles. For each question, prepare a STAR answer that demonstrates one or more LPs.

### Key LPs for Frontend Engineers:

**1. Customer Obsession**
- "How did you advocate for the user in a feature decision?"
- **Example:** "I pushed back on adding a newsletter popup that would interrupt the user experience. Instead, I proposed an inline CTA after article content, which improved signups by 30% without increasing bounce rate."

**2. Deliver Results**
- "Tell me about a time you overcame obstacles to deliver."
- **Example:** "During a critical product launch, our API was delayed. I mocked the API with realistic data and built the entire frontend. When the API was ready, we integrated in 2 hours and launched on schedule."

**3. Dive Deep**
- "Tell me about a complex technical problem you solved."
- **Example:** "A memory leak in our infinite scroll component caused crashes on low-end devices. I used Chrome Memory tab to find that event listeners weren't being cleaned up. I implemented a proper cleanup in useEffect and added automated memory tests."

**4. Learn and Be Curious**
- "How do you stay updated with technology?"
- **Example:** "I dedicate Friday afternoons to learning. When React Server Components were announced, I built a prototype and presented findings to the team."

**5. Insist on the Highest Standards**
- "Tell me about a time you improved quality."
- **Example:** "I noticed our bundle size was growing 15% per sprint. I set up bundle analysis in CI, implemented code splitting, and created a bundle size budget. Bundle size reduced by 60%."

**6. Have Backbone; Disagree and Commit**
- "Tell me about a time you disagreed with a decision."
- **Example:** "My manager wanted to use client-side search for 100K items. I built a prototype showing it would take 5s to load. I proposed server-side search with debouncing. We tested both, and server-side was 10x faster."

**7. Ownership**
- "Tell me about a time you took initiative beyond your role."
- **Example:** "I noticed our team had no performance monitoring. I set up Lighthouse CI, created a performance dashboard, and established performance budgets. This became the standard across the organization."

## Frontend Coding Round

### Focus Areas:
- **JavaScript:** Closure, event loop, promises, async/await, Array methods
- **React:** Hooks, state management, component lifecycle, performance
- **CSS:** Flexbox, Grid, responsive design, animations
- **System Design:** Component architecture, data flow

### Example Problems:

```javascript
// Problem: Create a Promise queue that processes tasks sequentially
class PromiseQueue {
  constructor(concurrency = 1) {
    this.concurrency = concurrency;
    this.running = 0;
    this.queue = [];
  }
  
  add(task) {
    return new Promise((resolve, reject) => {
      this.queue.push({ task, resolve, reject });
      this.runNext();
    });
  }
  
  runNext() {
    if (this.running >= this.concurrency || this.queue.length === 0) return;
    
    this.running++;
    const { task, resolve, reject } = this.queue.shift();
    
    Promise.resolve(task())
      .then(resolve, reject)
      .finally(() => {
        this.running--;
        this.runNext();
      });
  }
}

// Problem: Build a custom hook for API polling with retry
function useApiWithRetry(url, options = {}) {
  const { retries = 3, interval = 1000 } = options;
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);
  
  const fetchWithRetry = useCallback(async (attempt = 0) => {
    try {
      setLoading(true);
      const response = await fetch(url);
      if (!response.ok) throw new Error(`HTTP ${response.status}`);
      const result = await response.json();
      setData(result);
      setError(null);
    } catch (err) {
      if (attempt < retries) {
        await new Promise(r => setTimeout(r, interval * Math.pow(2, attempt)));
        return fetchWithRetry(attempt + 1);
      }
      setError(err);
    } finally {
      setLoading(false);
    }
  }, [url, retries, interval]);
  
  useEffect(() => { fetchWithRetry(); }, [fetchWithRetry]);
  
  return { data, error, loading, refetch: () => fetchWithRetry() };
}
```

## System Design for Frontend

### Design Problems:
- **Design Amazon.com product page** - Product details, reviews, recommendations, add to cart
- **Design Amazon Cart** - State sync, optimistic updates, persistence
- **Design a product search** - Autocomplete, filters, pagination
- **Design Amazon's checkout** - Multi-step form, address auto-complete, payment

### Key Considerations:
- **Latency:** Amazon found every 100ms delay costs 1% in sales
- **Offline:** Cart must work offline with sync
- **A/B Testing:** Amazon runs thousands of experiments simultaneously
- **Accessibility:** Must work for all users

## Preparation Tips

### 3 months before:
- LeetCode (medium) - arrays, strings, trees, DP
- Master React hooks and internals
- Practice system design (draw architecture)

### 1 month before:
- Prepare 10 STAR stories covering different LPs
- Practice coding on a whiteboard or plain editor (no autocomplete)
- Review Amazon's leadership principles thoroughly

### Tips:
- **Every answer should demonstrate an LP** - Even technical questions
- **Use metrics** - Quantify impact whenever possible
- **Structure your answers** - STAR format consistently
- **Be ready for "Why Amazon?"** - Research Amazon's frontend work (AWS Console, retail, Prime Video)
- **Practice the bar raiser** - This is the most important round; it's pure behavioral with LP focus
