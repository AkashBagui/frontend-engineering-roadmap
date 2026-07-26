# Performance Optimization Projects

## Project: Optimize an Intentionally Slow Dashboard

**Goal:** Take a deliberately unoptimized analytics dashboard and apply performance optimizations step by step until it achieves excellent Lighthouse scores (≥ 95 Performance, ≥ 95 Accessibility).

### Starting Point: The Slow Dashboard

Below is the intentionally unoptimized version. It has poor scores by design.

#### Step 0: Initial Baseline

```tsx
// app/dashboard/page.tsx — UNOPTIMIZED
'use client';

import { useState, useEffect } from 'react';
import { Chart } from 'chart.js';
import moment from 'moment';
import _ from 'lodash';
import { DataTable } from '@/components/DataTable';

export default function DashboardPage() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('/api/dashboard')
      .then(r => r.json())
      .then(setData)
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <div>Loading...</div>;

  const processed = _.map(data.transactions, t => ({
    ...t,
    formattedDate: moment(t.date).format('MMMM Do YYYY'),
    category: _.startCase(t.category),
  }));

  return (
    <div>
      <h1>Dashboard</h1>
      <img src="/logo.png" alt="Logo" style={{ width: 400, height: 300 }} />
      <img src="/banner.jpg" alt="Banner" />
      <canvas id="chart" width="800" height="400"></canvas>
      <DataTable data={processed} />
      <script src="/heavy-analytics.js"></script>
    </div>
  );
}
```

**Initial Lighthouse Scores:**
```
Performance: 32
Accessibility: 65
Best Practices: 55
SEO: 72
```

### Step 1: Code Splitting & Bundle Optimization

#### Problem
- `chart.js` (120 KB), `moment` (231 KB), `lodash` (70 KB) all in main bundle
- Route is a monolithic Client Component

#### Fix

```tsx
// app/dashboard/page.tsx
import { Suspense } from 'react';
import { DashboardSkeleton } from './loading';

export default function DashboardPage() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<DashboardSkeleton />}>
        <DashboardContent />
      </Suspense>
    </div>
  );
}

// DashboardContent is a Server Component that fetches data
// app/dashboard/DashboardContent.tsx
export default async function DashboardContent() {
  const data = await fetchDashboardData(); // Server-side fetch
  return (
    <div>
      <StatsGrid data={data.stats} />
      <Suspense fallback={<ChartSkeleton />}>
        <RevenueChart data={data.revenue} />
      </Suspense>
      <Suspense fallback={<TableSkeleton />}>
        <TransactionsTable data={data.transactions} />
      </Suspense>
    </div>
  );
}
```

```tsx
// components/RevenueChart.tsx — Lazy loaded
'use client';
import { lazy, Suspense } from 'react';
const Chart = lazy(() => import('chart.js'));  // 120 KB → lazy chunk

export function RevenueChart({ data }) {
  return (
    <Suspense fallback={<ChartSkeleton />}>
      <Chart data={data} type="line" />
    </Suspense>
  );
}
```

#### Replace heavy libraries

```tsx
// Replace moment with date-fns (tree-shakeable)
import { format } from 'date-fns';
const formattedDate = format(new Date(t.date), 'MMMM do yyyy');

// Replace lodash with native or es-toolkit
import { startCase } from 'es-toolkit';
const category = startCase(t.category);
```

#### Result
```
Main bundle: 480 KB → 85 KB
Lazy chunks: chart.js (120 KB) loaded only when chart visible
Lighthouse Performance: 32 → 58
```

### Step 2: Image Optimization

#### Problems
- Logo image: 400×300 but displayed larger → oversized
- Banner image: No lazy loading, no dimensions, no WebP
- No responsive images

#### Fix

```tsx
import Image from 'next/image';

// Logo — properly sized, eager load (above fold)
<Image
  src="/logo.png"
  alt="Company Logo"
  width={200}
  height={80}
  priority
/>

// Banner — lazy load, responsive, WebP
<Image
  src="/banner.jpg"
  alt="Dashboard Banner"
  width={1200}
  height={400}
  sizes="(max-width: 768px) 100vw, 1200px"
  quality={80}
  placeholder="blur"
  loading="lazy"
/>
```

#### Result
```
Image weight: 2.4 MB → 180 KB (WebP, responsive)
Lighthouse Performance: 58 → 72
```

### Step 3: Lazy Loading Non-Critical Content

#### Problem
- Below-fold content (chart, table, analytics script) blocks initial render
- Heavy analytics script loaded synchronously

#### Fix

```tsx
// Lazy load below-fold sections
import { useVisibility } from '@/hooks/useVisibility';

function DashboardContent() {
  const { ref: chartRef, isVisible: showChart } = useVisibility({ rootMargin: '100px' });
  const { ref: tableRef, isVisible: showTable } = useVisibility({ rootMargin: '200px' });

  return (
    <div>
      {/* Above fold — render immediately */}
      <StatsGrid data={data.stats} />

      {/* Below fold — lazy load */}
      <div ref={chartRef}>
        {showChart && (
          <Suspense fallback={<ChartSkeleton />}>
            <RevenueChart data={data.revenue} />
          </Suspense>
        )}
      </div>

      <div ref={tableRef}>
        {showTable && (
          <Suspense fallback={<TableSkeleton />}>
            <TransactionsTable data={data.transactions} />
          </Suspense>
        )}
      </div>
    </div>
  );
}
```

#### Defer analytics

```tsx
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        {/* Analytics loaded after page is interactive */}
        <Script
          src="/heavy-analytics.js"
          strategy="afterInteractive"
        />
      </body>
    </html>
  );
}
```

#### Result
```
Initial load JS: 480 KB → 85 KB (main) + 20 KB (above-fold)
Lighthouse Performance: 72 → 82
```

### Step 4: Core Web Vitals Fixes

#### Problems
- CLS: Images without dimensions, layout shifts
- LCP: Large hero image not preloaded
- FID/INP: Long tasks from heavy JS

#### Fix CLS

```tsx
// Reserve space for lazy-loaded sections
<div style={{ minHeight: '400px' }} ref={chartRef}>
  {showChart && <RevenueChart />}
</div>

// Always include width/height on images
<Image width={1200} height={400} ... />

// Use transform for animations instead of layout-triggering props
.element {
  transition: transform 0.3s ease;
}
```

#### Optimize LCP

```tsx
// Pages router: preload LCP image
<Head>
  <link rel="preload" href="/hero.webp" as="image" />
</Head>

// App Router: priority prop on above-fold images
<Image src="/hero.webp" alt="Hero" priority />
```

#### Yield to Main Thread

```tsx
// Break up long tasks
async function processTransactions(transactions: Transaction[]) {
  const chunkSize = 50;
  for (let i = 0; i < transactions.length; i += chunkSize) {
    const chunk = transactions.slice(i, i + chunkSize);
    processChunk(chunk);
    
    // Yield to let browser paint/interact
    await new Promise(resolve => setTimeout(resolve, 0));
  }
}
```

#### Result
```
CLS: 0.42 → 0.05
LCP: 6.2s → 1.8s
INP: 320ms → 85ms
Lighthouse Performance: 82 → 91
```

### Step 5: Accessibility & SEO

#### Fix Accessibility

```tsx
// Add ARIA labels
<button aria-label="Open menu" onClick={toggleMenu}>
  <MenuIcon />
</button>

// Proper heading hierarchy
<h1>Dashboard</h1>
<section aria-labelledby="stats-heading">
  <h2 id="stats-heading">Statistics</h2>
</section>

// Ensure color contrast
// Before: #999 on #fff (2.8:1 ratio)
// After: #595959 on #fff (4.6:1 ratio) ✓

// Form labels
<label htmlFor="search">Search transactions</label>
<input id="search" type="search" />

// Keyboard navigation
<button onKeyDown={(e) => e.key === 'Enter' && handleAction()}>
```

#### Fix SEO

```tsx
// app/dashboard/page.tsx
export const metadata = {
  title: 'Dashboard | Company',
  description: 'View your analytics, transactions, and revenue charts.',
  openGraph: {
    title: 'Company Dashboard',
    description: 'Real-time business analytics dashboard',
  },
};
```

#### Result
```
Accessibility: 65 → 96
SEO: 72 → 100
```

### Step 6: Final Optimization & Verification

#### Performance Budget

```json
{
  "performanceBudget": {
    "lcp": 2500,
    "cls": 0.1,
    "inp": 200,
    "totalBundleSize": 200000,
    "firstLoadJS": 100000,
    "imageWeight": 300000
  }
}
```

#### Lighthouse CI

```yaml
# lighthouse-ci.yml
assertions:
  categories:performance: ["error", { minScore: 0.95 }]
  categories:accessibility: ["error", { minScore: 0.95 }]
  categories:best-practices: ["error", { minScore: 0.90 }]
  categories:seo: ["error", { minScore: 0.95 }]
  lcp: ["error", { maxNumericValue: 2500 }]
  cls: ["error", { maxNumericValue: 0.1 }]
  total-blocking-time: ["error", { maxNumericValue: 200 }]
```

#### Final Scores

```
Performance:  97
Accessibility: 98
Best Practices: 93
SEO:          100
```

### Summary of Optimizations

| Step | Optimization | Impact | Effort |
|------|-------------|--------|--------|
| 1 | Code splitting, library replacements | +26 Performance | High |
| 2 | Image optimization (WebP, responsive) | +14 Performance | Low |
| 3 | Lazy loading below-fold content | +10 Performance | Medium |
| 4 | Core Web Vitals (CLS, LCP, INP) | +9 Performance | Medium |
| 5 | Accessibility & SEO | +31 A11y, +28 SEO | Medium |
| 6 | CI integration & budgets | Prevents regressions | Low |

### Key Takeaways

1. **Measure first** — Lighthouse provides specific, actionable feedback
2. **Largest impact first** — Images and JavaScript dominate page weight
3. **Iterate** — Each optimization unlocks the next layer of improvements
4. **Automate** — Lighthouse CI prevents regressions in pull requests
5. **Real users matter** — Complement lab data with RUM (web-vitals)
