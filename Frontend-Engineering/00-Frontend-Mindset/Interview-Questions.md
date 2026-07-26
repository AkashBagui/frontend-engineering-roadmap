# Interview Questions — Frontend Mindset

## Conceptual Questions (The Web, Architecture, Mindset)

### 1. What happens when you type a URL into the browser and press Enter?

**Answer:** The browser follows this sequence:
1. **URL Parsing** - determines protocol (https), host (example.com), path (/)
2. **DNS Resolution** - checks browser cache → OS cache → router cache → ISP DNS → recursive lookup to find IP
3. **TCP Handshake** - 3-way handshake (SYN, SYN-ACK, ACK) establishes connection
4. **TLS Handshake** (for HTTPS) - negotiates encryption, verifies certificate, exchanges keys
5. **HTTP Request** - browser sends GET request with headers (User-Agent, Accept, Cookies)
6. **Server Processing** - server receives request, runs app logic, queries database if needed
7. **HTTP Response** - server sends back HTML (or JSON/XML) with status code and headers
8. **Rendering Pipeline** - browser parses HTML → DOM, CSS → CSSOM, builds Render Tree, Layout, Paint, Composite

### 2. What is the difference between CSR and SSR?

**Answer:**
- **CSR**: Browser downloads minimal HTML, JavaScript builds the UI. First paint is slow (wait for JS). SEO is poor. Good for highly interactive apps.
- **SSR**: Server generates full HTML with data. Fast first paint, excellent SEO. Higher server cost. Hydration needed for interactivity.
- **SSG** (Static): HTML pre-built at deploy time. Fastest, but data is stale until next build.

### 3. Explain the Critical Rendering Path.

**Answer:** The sequence of steps the browser takes to render a page:
1. **HTML → DOM** - bytes → tokens → nodes → DOM tree
2. **CSS → CSSOM** - CSS bytes → CSSOM tree (render-blocking)
3. **Render Tree** - combine DOM + CSSOM (exclude display: none)
4. **Layout** - calculate geometry (position, size)
5. **Paint** - fill pixels (text, colors, images)
6. **Composite** - send layers to GPU for final display

Optimizations: inline critical CSS, defer non-critical JS, preload key resources, minimize reflows.

### 4. What are Core Web Vitals and why do they matter?

**Answer:** Google's metrics for user experience quality:
- **LCP** (Largest Contentful Paint) - loading performance. Target: ≤ 2.5s
- **FID** (First Input Delay) - interactivity. Target: ≤ 100ms
- **CLS** (Cumulative Layout Shift) - visual stability. Target: ≤ 0.1

They directly impact user satisfaction, engagement, and search rankings (Google uses them as ranking signals).

### 5. Explain async vs defer on script tags.

**Answer:**
- **async**: Downloaded in parallel with HTML parsing. Executes immediately when downloaded - pausing parsing. Order NOT guaranteed. Use for independent scripts (analytics).
- **defer**: Downloaded in parallel with HTML parsing. Waits to execute until HTML is fully parsed. Order IS guaranteed. Use for scripts that depend on DOM or other scripts.
- **No attribute**: Blocks parsing - downloads and executes immediately. Worst for performance.

### 6. Monolith vs Microservices architecture?

**Answer:**
- **Monolith**: All code in one deployable unit. Simple to start, test, and deploy. Becomes unwieldy as team grows.
- **Microservices**: Each service is independently deployable with its own DB. Complex infrastructure (service discovery, API gateway, distributed tracing). Better for large teams.

### 7. What is technical debt and how do you manage it?

**Answer:** Technical debt is the long-term cost of taking shortcuts in code/architecture for short-term speed. Types: code debt, architecture debt, test debt, documentation debt, infrastructure debt.

**Management:**
- Track it (backlog, ADRs)
- Allocate 10-20% of sprint time to addressing it
- Pay off debt near code you are already changing (boy scout rule)
- Distinguish strategic debt (intentional) vs accidental debt (unaware)

### 8. How do you debug a performance issue in production?

**Answer:**
1. **Reproduce** - with real data and conditions
2. **Measure** - Lighthouse, Performance tab, Web Vitals library
3. **Identify** - find the bottleneck (long task, large layout, expensive paint)
4. **Analyze** - check bundle size, network waterfall, re-renders
5. **Fix** - code split, lazy load, memoize, optimize images, reduce reflows
6. **Verify** - re-measure without regressions
7. **Monitor** - add synthetic monitoring or RUM

### 9. What factors influence your choice of framework?

**Answer:**
- **Team expertise** - what does the team know?
- **Project requirements** - SEO? Real-time? Highly interactive?
- **Ecosystem** - does it have the libraries we need?
- **Performance needs** - bundle size, startup time
- **Longevity** - community size, maintenance, breaking changes
- **Build tooling** - Vite vs Webpack, SSR support
- **Scalability** - will it work for 500+ components?

### 10. What is the role of an API Gateway?

**Answer:** An API Gateway is a single entry point between frontend and backend services. It handles:
- Routing requests to appropriate services
- Authentication and authorization
- Rate limiting
- Request/response transformation
- Load balancing
- Caching
- Monitoring and logging

### 11. Explain the concept of "progressive enhancement."

**Answer:** Start with a baseline experience that works everywhere (even without JS), then layer enhancements for modern browsers. Contrasts with "graceful degradation" which starts with full experience and tries to fall back. Example: a form works with just HTML, then gets AJAX submission, then gets real-time validation.

### 12. What is the difference between reflow and repaint?

**Answer:**
- **Reflow** (layout): Recalculate element positions and sizes. Triggered by DOM changes, CSS changes, window resize. Expensive.
- **Repaint**: Redraw pixels when visual changes don't affect layout (e.g., color, visibility). Cheaper than reflow.

Rule of thumb: batch DOM reads/writes to minimize forced reflows. Use transform/opacity for animations (composited, no reflow).

### 13. What is the JAMstack?

**Answer:** JAMstack = JavaScript, APIs, Markup. Architecture where:
- Static pages are pre-built (Markup)
- Dynamic functionality comes from JavaScript calling APIs
- Served via CDN
Benefits: Fast, secure, scalable. Drawbacks: Less suitable for highly dynamic, user-specific content.

### 14. How would you optimize a slow-loading landing page?

**Answer:**
1. **Measure** current performance with Lighthouse
2. **Images** - compress (WebP/AVIF), lazy load below-fold, use responsive sizes
3. **JavaScript** - code split, tree-shake, defer non-critical, remove unused
4. **CSS** - inline critical CSS, remove unused CSS, minify
5. **Fonts** - use font-display: swap, subset fonts, preload
6. **Network** - use CDN, enable HTTP/2, add resource hints (preload, preconnect)
7. **Rendering** - reduce DOM size, avoid layout thrashing

### 15. What is the difference between React, Vue, and Angular?

**Answer:**
- **React**: Library (not framework). Unopinionated. Virtual DOM. Hooks-based. Large ecosystem. Best for flexible, large-scale apps.
- **Vue**: Progressive framework. Template-based with Composition API. Easier learning curve. Good for small-to-large apps.
- **Angular**: Full framework with everything included (routing, HTTP, forms). TypeScript-native. Opinionated. Best for enterprise apps.

### 16. What is TypeScript and why use it?

**Answer:** TypeScript is a typed superset of JavaScript that compiles to plain JS. Benefits:
- Catches type-related bugs at compile time
- Better IDE support (autocomplete, refactoring)
- Self-documenting code (types as documentation)
- Easier to refactor large codebases
- Industry standard for professional frontend development

### 17. Describe a time you had to make a difficult technical trade-off.

**Answer (structured):** Use the STAR method:
- **Situation**: We needed to ship a dashboard feature in 2 weeks
- **Task**: Build real-time data visualization
- **Action**: Chose to use a simpler chart library (Chart.js) instead of D3.js (steep learning curve). Knew we would need to rewrite later but shipping was the priority.
- **Result**: Shipped on time, got user feedback, then migrated to D3 for V2 when we understood exact requirements.

### 18. How do you stay up to date with frontend technologies?

**Answer:**
- **Newsletters**: Bytes, Frontend Focus, React Status
- **Podcasts**: Syntax, ShopTalk Show
- **Social**: Twitter/X (follow framework authors), GitHub trending
- **Practice**: Build small side projects with new tools
- **Reading**: MDN, web.dev, framework docs
- **Community**: Conferences (or talks on YouTube), local meetups

### 19. What are Web Components and how do they compare to frameworks?

**Answer:** Web Components are native browser APIs (Custom Elements, Shadow DOM, HTML Templates) for creating reusable components without frameworks.

Pros: Framework-agnostic, native browser support, no build step needed for simple cases.
Cons: Less ergonomic than frameworks, no built-in state management, no SSR, less ecosystem.

Good for: Design system components that need to work across frameworks. Not a replacement for full framework usage.

### 20. Explain the concept of "thinking in React" (component composition).

**Answer:** Thinking in React means:
1. **Break UI into components** - each does one thing well
2. **Build static version** - pass data via props, no state yet
3. **Identify state** - minimal, DRY, derived values are not state
4. **Determine state location** - lift state up to the lowest common ancestor
5. **Data flows down** - parent passes data to children via props
6. **Events flow up** - children communicate via callbacks

### 21. What is the difference between a library and a framework?

**Answer:**
- **Library**: Your code calls the library. You control the flow. Examples: React, Lodash, Axios.
- **Framework**: The framework calls your code. Inversion of control. Examples: Angular, Next.js, Vue.
- "React is a library, Angular is a framework" - React focuses on UI only, Angular provides routing, HTTP, forms, etc.

### 22. How would you implement accessibility in a component?

**Answer:**
1. **Semantic HTML** - use native elements (button, nav, main) instead of divs with ARIA
2. **Keyboard support** - all interactive elements reachable and operable via keyboard
3. **ARIA attributes** - role, aria-label, aria-expanded, aria-live where needed
4. **Color contrast** - meet WCAG AA (4.5:1 for normal text)
5. **Focus management** - visible focus indicators, manage focus in modals
6. **Screen reader** - proper heading hierarchy, alt text on images
7. **Testing** - use axe-core, Lighthouse a11y audit, test with screen reader

## Behavioral Questions

### 23. Tell me about a challenging project you worked on.

**Answer framework (STAR):**
- **Situation**: Legacy codebase with no tests, slow builds, frequent bugs
- **Task**: Stabilize and modernize the frontend
- **Action**: Introduced TypeScript, set up CI with linting and test coverage gates, refactored the most bug-prone module, wrote documentation
- **Result**: Bug rate dropped 60%, build time reduced 40%, team velocity increased

### 24. How do you handle disagreement with a designer or product manager?

**Answer:**
1. **Understand their perspective** - ask questions to learn their constraints
2. **Share your perspective** - explain technical trade-offs (load time, accessibility, maintainability)
3. **Find common ground** - propose alternatives that satisfy both goals
4. **Data over opinion** - A/B test or benchmark if possible
5. **Escalate gracefully** - involve team lead if needed, but avoid ego battles

### 25. What is your approach to code reviews?

**Answer:**
- **Author**: Keep PRs small (< 400 lines), add description and screenshots, self-review before requesting
- **Reviewer**:
  - Start with high-level: is the approach right?
  - Check correctness, security, performance
  - Look for test coverage
  - Suggest, don't dictate ("What do you think about...?")
  - Approve quickly once satisfied, don't gatekeep
- **Both**: Assume good intent, focus on code not person, learn from each other
