# React Server Components

React Server Components (RSC) are components that **run exclusively on the server** during rendering. They represent a significant paradigm shift in how React applications are built, offering zero-bundle-size components.

## What Are Server Components?

Server Components execute on the server, not in the browser. They can access databases, file systems, and backend services directly — without exposing that logic or its dependencies to the client.

| Aspect | Server Component | Client Component |
|--------|-----------------|------------------|
| Runs on | Server | Browser |
| Bundle size | 0 bytes (never sent to client) | Full size |
| State (useState) | ❌ | ✅ |
| Effects (useEffect) | ❌ | ✅ |
| Event handlers (onClick) | ❌ | ✅ |
| Browser APIs | ❌ | ✅ |
| Direct DB access | ✅ | ❌ |
| Async/await | ✅ (in render) | ❌ (use useEffect or Suspense) |

## Benefits

### 1. Zero Bundle Size

Server Components contribute **zero JavaScript** to the client bundle. Large libraries (markdown parsers, syntax highlighters, date libraries) can stay on the server:

```jsx
// Server Component — markedown library is never sent to client
import { marked } from 'marked';

export default function Article({ content }) {
  return <div dangerouslySetInnerHTML={{ __html: marked(content) }} />;
}
```

### 2. Direct Backend Access

Query databases and APIs directly without building REST/GraphQL endpoints:

```jsx
// Server Component — direct DB access, no API layer needed
export default async function UserProfile({ userId }) {
  const user = await db.users.findUnique({ where: { id: userId } });
  return <div>{user.name} — {user.email}</div>;
}
```

### 3. Automatic Code Splitting

Client Components imported by Server Components are automatically code-split:

```jsx
// Server Component
import ClientCarousel from './ClientCarousel.client';

export default function Page() {
  return (
    <div>
      <h1>Page Title</h1>
      <ClientCarousel /> {/* This is automatically lazy-loaded */}
    </div>
  );
}
```

### 4. Streaming

Server Components can stream content progressively as it becomes available, improving perceived performance:

```
Page loads:
  ┌───────────────────────────────────────────┐
  │  Shell (layout, nav) — IMMEDIATE          │
  ├───────────────────────────────────────────┤
  │  Header section — STREAMED                │
  ├───────────────────────────────────────────┤
  │  Main content (slow DB) — STREAMED LATER  │
  └───────────────────────────────────────────┘
```

## Server vs Client Boundaries

The **"use client"** directive marks the boundary between server and client:

```jsx
// This is a Server Component (default in RSC frameworks)
import { Suspense } from 'react';
import MarkdownRenderer from './MarkdownRenderer.server';
import Comments from './Comments.client';

export default function PostPage({ postId }) {
  return (
    <div>
      <Suspense fallback={<Skeleton />}>
        <MarkdownRenderer postId={postId} />   {/* Server — 0 KB JS */}
      </Suspense>
      <Comments postId={postId} />              {/* Client — interactive */}
    </div>
  );
}
```

```jsx
// Comments.client.js — "use client" directive
'use client';

import { useState } from 'react';

export default function Comments({ postId }) {
  const [showAll, setShowAll] = useState(false);
  // ...
  return <button onClick={() => setShowAll(!showAll)}>Toggle</button>;
}
```

### Rules for 'use client'

1. Must be the **first line** in the file (before any imports)
2. Marks the file and all its dependencies as client code
3. Components without 'use client' are Server Components by default
4. A Client Component can import other Client Components and basic React elements, but **cannot** import Server Components

## When to Use Server vs Client Components

### Use Server Components for:
- Data fetching (DB queries, API calls)
- Rendering static content
- Components that don't need interactivity
- Using large dependencies (markdown, date formatting, chart rendering on server)

### Use Client Components for:
- User interactions (forms, buttons, accordions)
- State and effects (useState, useEffect, useReducer)
- Browser-specific APIs (localStorage, geolocation, canvas)
- Custom event handlers (onClick, onSubmit, onChange)

## Streaming with Suspense

Server Components combine with Suspense for streaming:

```jsx
import { Suspense } from 'react';

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<SlowWidgetSkeleton />}>
        <SlowWidget /> {/* This streams in later */}
      </Suspense>
      <Suspense fallback={<AnalyticsSkeleton />}>
        <AnalyticsPanel /> {/* This streams in even later */}
      </Suspense>
    </div>
  );
}

async function SlowWidget() {
  const data = await fetchSlowApi(); // Blocks rendering, not navigation
  return <Widget data={data} />;
}
```

## Next.js Integration

Next.js App Router (v13+) uses RSC by default:

```jsx
// app/page.js — Server Component by default
async function HomePage() {
  const posts = await db.posts.findMany(); // Direct DB access

  return (
    <main>
      {posts.map(post => (
        <article key={post.id}>
          <h2>{post.title}</h2>
          <LikeButton /> {/* Client Component */}
        </article>
      ))}
    </main>
  );
}
```

Key differences from Pages Router:

| Feature | Pages Router | App Router (RSC) |
|---------|-------------|------------------|
| Default | Client Component | Server Component |
| Data fetching | `getServerSideProps` | `async` component |
| Layouts | Per-page | Nested layouts |
| Loading | Custom | `loading.js` (Suspense) |
| Streaming | Manual | Built-in |
| Server Actions | ❌ | ✅ (form actions in Server Components) |

## Server Actions (React 19)

Server Actions are functions that run on the server but can be called from the client:

```jsx
// Server Component
export default function Form() {
  async function submit(formData) {
    'use server';
    const name = formData.get('name');
    await db.users.create({ data: { name } });
  }

  return (
    <form action={submit}>
      <input name="name" />
      <button type="submit">Save</button>
    </form>
  );
}
```

## Mental Model

```
Traditional React (all client):
  HTML + JS → Browser renders everything

React with RSC:
  Server renders Server Components
       ↓
  Streams HTML + minimal JS
       ↓
  Client hydrates Client Components
       ↓
  Interactive app (Client Components handle state/effects)
       ↓
  More Server Components stream in as data becomes available
```

## Key Takeaways

1. **RSC ≠ SSR** — SSR renders components to HTML on every request (still sends JS). RSC sends **zero JS** for server components.
2. **Interleaving** — Server and Client components coexist in the same tree
3. **'use client'** is a boundary, not a page — you sprinkle client components where interactivity is needed
4. **Async components** — Server Components can be async (await data directly)
5. **No state/effects** — Server Components cannot use hooks or browser APIs
