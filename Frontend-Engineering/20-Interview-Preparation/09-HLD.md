# High-Level Design (HLD) for Frontend

## 1. Designing for Scale

### Architecture Principles

```
┌─────────────────────────────────────────────────────────────┐
│                     CDN Layer (CloudFront, Fastly)           │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │ Static   │ │ Images   │ │ API      │ │ WebSocket│       │
│  │ Assets   │ │          │ │ Caching  │ │ Cache    │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
├─────────────────────────────────────────────────────────────┤
│                  Application Layer                           │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Load Balancer (ALB)                      │   │
│  └────────────────────┬─────────────────────────────────┘   │
│  ┌────────────────────┼─────────────────────────────────┐   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐           │   │
│  │  │ Next.js  │  │ Next.js  │  │ Next.js  │           │   │
│  │  │ Instance │  │ Instance │  │ Instance │           │   │
│  │  │    #1     │  │    #2     │  │    #3     │           │   │
│  │  └──────────┘  └──────────┘  └──────────┘           │   │
│  └──────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│                   Data Layer                                 │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                    │
│  │  Redis   │ │PostgreSQL│ │  S3 /    │                    │
│  │ (Cache)  │ │ (Primary)│ │  CDN     │                    │
│  └──────────┘ └──────────┘ └──────────┘                    │
└─────────────────────────────────────────────────────────────┘
```

### Scaling Strategies

**Horizontal Scaling:**
- Deploy multiple app instances behind load balancer
- Sticky sessions (or use Redis for session store)
- Auto-scaling based on CPU/memory/request count

**Caching Layers:**
| Layer | Cache Strategy | TTL |
|-------|---------------|-----|
| CDN | Static assets, SSR HTML | 1 year (immutable) |
| Browser | Service Worker, HTTP Cache | Varies |
| Application | React Query, SWR | 5-30 min |
| API Gateway | Response caching | 1-5 min |
| Database | Query cache, Redis | Varies |

**Performance targets:**
- First Contentful Paint (FCP): < 1.5s
- Largest Contentful Paint (LCP): < 2.5s
- First Input Delay (FID): < 100ms
- Cumulative Layout Shift (CLS): < 0.1

---

## 2. Micro-Frontends Architecture

### When to Use Micro-Frontends

- Multiple teams working on same product
- Large codebase with long build times
- Different technology requirements
- Independent deployments needed
- Migrating legacy applications

### Integration Approaches

**1. Iframe (simplest, but limited)**
```html
<iframe src="http://team-b-app.com/widget"></iframe>
```

**2. Web Components (framework-agnostic)**
```javascript
// Team A builds a web component
class UserWidget extends HTMLElement {
  connectedCallback() {
    this.innerHTML = `<div>User Widget</div>`;
  }
}
customElements.define('user-widget', UserWidget);

// Team B uses it in React
function App() {
  return <user-widget user-id="123" />;
}
```

**3. Module Federation (Webpack 5)**
```javascript
// host/webpack.config.js
new ModuleFederationPlugin({
  name: 'host',
  remotes: {
    'teamA': 'teamA@http://team-a-app.com/remoteEntry.js',
    'teamB': 'teamB@http://team-b-app.com/remoteEntry.js',
  },
});

// host/src/App.js
const TeamAButton = React.lazy(() => import('teamA/Button'));

function App() {
  return (
    <Suspense fallback="Loading...">
      <TeamAButton />
    </Suspense>
  );
}
```

**4. Single-SPA (orchestration framework)**
```javascript
import { registerApplication, start } from 'single-spa';

registerApplication({
  name: 'react-app',
  app: () => import('./react-app'),
  activeWhen: '/react',
});

registerApplication({
  name: 'vue-app',
  app: () => import('./vue-app'),
  activeWhen: '/vue',
});

start();
```

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    Shell Application                          │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │  Header  │ │  Nav     │ │  Footer  │ │ Auth     │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
├─────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Content Area (Router)                    │   │
│  │                                                       │   │
│  │  ┌────────────────┐ ┌────────────────┐               │   │
│  │  │  Team A MF     │ │  Team B MF     │               │   │
│  │  │  (React)       │ │  (Vue)         │               │   │
│  │  └────────────────┘ └────────────────┘               │   │
│  └──────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│              Shared Dependencies                             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                    │
│  │  React   │ │  Design  │ │  Utils   │                    │
│  │          │ │  System  │ │          │                    │
│  └──────────┘ └──────────┘ └──────────┘                    │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. Design System Architecture

### Component Hierarchy

```
Design System
├── Tokens
│   ├── Colors (primary, secondary, neutral)
│   ├── Typography (font family, scale, weights)
│   ├── Spacing (4px base grid)
│   ├── Shadows (elevation levels)
│   └── Breakpoints (mobile, tablet, desktop)
├── Primitives
│   ├── Box (layout container)
│   ├── Flex (flexbox wrapper)
│   ├── Grid (grid container)
│   ├── Text (typography component)
│   └── Icon (SVG icon system)
├── Components
│   ├── Button (variants: primary, secondary, ghost)
│   ├── Input (text, select, checkbox, radio)
│   ├── Modal (dialog, drawer)
│   ├── Card (with header, content, actions)
│   ├── Table (sortable, filterable)
│   ├── Tabs (horizontal, vertical)
│   └── Navigation (breadcrumb, pagination)
└── Patterns
    ├── Authentication (login form, password reset)
    ├── Data Display (data table with search)
    └── Feedback (toast, notification, alert)
```

### Technology Stack

```javascript
// Design system package structure
design-system/
├── packages/
│   ├── tokens/          # Design tokens (JSON/TS)
│   ├── icons/           # SVG icon components
│   ├── components/      # React components
│   ├── hooks/           # Custom hooks
│   └── utils/           # Helper utilities
├── docs/                # Storybook documentation
├── test/               # Testing utilities
└── tools/              # Build tools, generators

// Package.json (build configuration)
{
  "scripts": {
    "build": "rollup -c",
    "test": "vitest run",
    "storybook": "storybook dev -p 6006",
    "chromatic": "chromatic --project-token=xxx",
    "release": "semantic-release"
  }
}
```

---

## 4. CI/CD Architecture

### Pipeline Design

```
┌─────────────────────────────────────────────────────────────┐
│                   CI Pipeline                                │
│                                                             │
│  Commit → Lint → TypeCheck → Unit Test → Build → Publish   │
│               ↓                                              │
│            Integration Tests                                  │
│               ↓                                              │
│            E2E Tests (Playwright)                             │
│               ↓                                              │
│            Visual Regression (Chromatic)                      │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                   CD Pipeline                                │
│                                                             │
│  Deploy to Staging → Smoke Tests → Deploy to Production     │
│       ↓                                                      │
│  Feature Flag Rollout (10% → 50% → 100%)                    │
│       ↓                                                      │
│  Monitoring & Alerting                                        │
│       ↓                                                      │
│  Performance Budget Checks                                    │
└─────────────────────────────────────────────────────────────┘
```

### Deployment Strategies

```yaml
# .github/workflows/deploy.yml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck
      - run: npm test
      - run: npx playwright test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: npm run build
      - run: |
          npx vercel deploy --prod \
            --build-env NEXT_PUBLIC_API_URL=${{ secrets.API_URL }} \
            --token ${{ secrets.VERCEL_TOKEN }}

  # Canary deployment
  canary:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: |
          # Deploy to canary slot (10% traffic)
          npx vercel deploy --canary \
            --build-env NEXT_PUBLIC_FEATURE_FLAG=new-ui

  # Rollback
  rollback:
    if: failure()
    runs-on: ubuntu-latest
    steps:
      - run: npx vercel rollback --token ${{ secrets.VERCEL_TOKEN }}
```

---

## 5. Monitoring & Observability

### Metrics to Track

```javascript
// Frontend monitoring
import { init, instrument } from '@vercel/analytics';

// Core Web Vitals
init({
  webVitals: true,
  performance: true,
});

// Custom performance marks
performance.mark('feature-paint-start');
// ... render feature
performance.measure('feature-paint', 'feature-paint-start');

// Error tracking
window.addEventListener('error', (event) => {
  logError({
    message: event.message,
    stack: event.error?.stack,
    url: window.location.href,
    userAgent: navigator.userAgent,
    timestamp: Date.now(),
  });
});

// Custom business metrics
function trackEvent(name: string, data?: Record<string, unknown>) {
  if (typeof window.gtag !== 'undefined') {
    window.gtag('event', name, data);
  }
}
```

### Monitoring Stack

| Tool | Purpose |
|------|---------|
| Vercel Analytics | Core Web Vitals, page views |
| Sentry | Error tracking, performance |
| DataDog | APM, infrastructure monitoring |
| Grafana | Custom dashboards |
| LogRocket | Session replay |
| Lighthouse CI | Performance budget |

---

## 6. HLD Design Problems

### Problem 1: Design YouTube (Video Platform)

**Scale:**
- 500+ hours of video uploaded every minute
- 2B+ monthly active users
- Global CDN distribution

**Architecture:**
```
┌─────────────────────────────────────────────────────────────┐
│                    CDN (Multiple PoPs)                       │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Video CDN (HLS/DASH segments)                      │   │
│  └─────────────────────────────────────────────────────┘   │
├─────────────────────────────────────────────────────────────┤
│                    Application Layer                         │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │  Home    │ │  Watch   │ │  Search  │ │  Upload  │       │
│  │  Feed    │ │  Page    │ │          │ │          │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
├─────────────────────────────────────────────────────────────┤
│                    Services                                  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │  Video   │ │  Trans-  │ │  Recom-  │ │  Comments│       │
│  │  Service │ │  coding  │ │  mending │ │  Service │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
└─────────────────────────────────────────────────────────────┘
```

**Key decisions:**
- Adaptive bitrate streaming (HLS) for varying network conditions
- CDN for video content, application for metadata
- Lazy loading comments, recommendations
- Service Worker for offline playback
- Web Workers for video processing in browser

### Problem 2: Design Google Docs (Collaborative Editor)

**Scale:**
- Real-time collaboration (100+ concurrent editors)
- Millions of documents
- Offline editing support

**Architecture:**
```
┌─────────────────────────────────────────────────────────────┐
│              WebSocket Gateway (Socket Cluster)               │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                    │
│  │  WS      │ │  WS      │ │  WS      │                    │
│  │  Node #1 │ │  Node #2 │ │  Node #3 │                    │
│  └──────────┘ └──────────┘ └──────────┘                    │
├─────────────────────────────────────────────────────────────┤
│                    Services                                  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                    │
│  │  CRDT    │ │  Auth    │ │  Version │                    │
│  │  Service │ │  Service │ │  History │                    │
│  └──────────┘ └──────────┘ └──────────┘                    │
├─────────────────────────────────────────────────────────────┤
│                    Data Layer                                │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                    │
│  │  Redis   │ │PostgreSQL│ │  S3 (Doc │                    │
│  │ (Ephemer)│ │ (Meta)   │ │  Backup) │                    │
│  └──────────┘ └──────────┘ └──────────┘                    │
└─────────────────────────────────────────────────────────────┘
```

**Key decisions:**
- CRDT (Conflict-free Replicated Data Type) for conflict resolution
- Operational Transform for text operations
- Redis for ephemeral document state (fast reads)
- Periodic snapshots to S3 for persistence
- WebSocket for real-time communication
- Local-first architecture for offline editing

### Problem 3: Design an E-commerce Platform (Amazon-scale)

**Scale:**
- 300M+ active users
- 350M+ products
- 1M+ orders/day during peak

**Architecture:**
```
┌─────────────────────────────────────────────────────────────┐
│                    CDN Layer                                 │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                    │
│  │ Product  │ │  Images  │ │  Static  │                    │
│  │ API Cache│ │  CDN     │ │  Assets  │                    │
│  └──────────┘ └──────────┘ └──────────┘                    │
├─────────────────────────────────────────────────────────────┤
│                    Frontend App (Next.js)                    │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │  ISR     │ │  SSR     │ │  Client  │ │  PWA     │       │
│  │ (Product)│ │ (Search) │ │  Cart    │ │  Offline │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
├─────────────────────────────────────────────────────────────┤
│                    Microservices                             │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │ Product  │ │  Search  │ │  Order   │ │  Payment │       │
│  │ Service  │ │  Service │ │  Service │ │  Service │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
├─────────────────────────────────────────────────────────────┤
│                    Data Stores                               │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │
│  │  Redis   │ │PostgreSQL│ │Elastic-  │ │ DynamoDB │       │
│  │ (Cache)  │ │(Orders) │ │ search   │ │(Catalog) │       │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘       │
└─────────────────────────────────────────────────────────────┘
```

**Key decisions:**
- ISR for product pages (static + revalidation)
- SSR for search results (dynamic, personalized)
- Micro-frontends for different verticals
- Feature flags for gradual rollouts
- Algolia/Elasticsearch for product search
- Service Workers for cart persistence offline
- Optimistic UI for add-to-cart experience
