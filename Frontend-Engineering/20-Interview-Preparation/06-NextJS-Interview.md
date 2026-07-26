# Next.js Interview Questions

## 1. What is the difference between the App Router and Pages Router?

**Answer:**

```javascript
// Pages Router (Next.js 12 and below)
// pages/index.js
export default function Home() {
  return <h1>Home Page</h1>;
}

// pages/users/[id].js - Dynamic routes
export async function getServerSideProps({ params }) {
  const user = await fetch(`/api/users/${params.id}`).then(r => r.json());
  return { props: { user } };
}

// App Router (Next.js 13+)
// app/page.js
export default function Home() {
  return <h1>Home Page</h1>;
}

// app/users/[id]/page.js
export default async function UserPage({ params }) {
  const user = await fetch(`/api/users/${params.id}`).then(r => r.json());
  return <h1>{user.name}</h1>; // Server component - direct async
}
```

| Aspect | Pages Router | App Router |
|--------|-------------|------------|
| File convention | pages/ | app/ |
| Components | Client by default | Server by default |
| Data fetching | getServerSideProps, getStaticProps | Server components, async |
| Layouts | Manual with _app.js | Nested layout files |
| Loading states | Manual | loading.js (auto) |
| Error handling | Manual | error.js (auto) |
| Streaming | Manual | Built-in |
| Route groups | Not supported | Supported with (group) |

## 2. Explain SSR, SSG, and ISR in Next.js.

**Answer:**

```javascript
// SSR (Server-Side Rendering) - renders on each request
// Pages Router
export async function getServerSideProps({ req }) {
  const data = await fetchData();
  return { props: { data } };
}

// App Router - dynamic fetch (default)
export default async function Page() {
  const data = await fetch('https://api.example.com/data', {
    cache: 'no-store' // Forces SSR
  });
  return <div>{...}</div>;
}

// SSG (Static Site Generation) - pre-built at build time
// Pages Router
export async function getStaticProps() {
  const data = await fetchData();
  return { props: { data }, revalidate: 3600 }; // With ISR
}

export async function getStaticPaths() {
  const posts = await fetchPosts();
  return { paths: posts.map(p => ({ params: { id: p.id } })), fallback: 'blocking' };
}

// App Router - static by default
export default async function Page() {
  const data = await fetch('https://api.example.com/data');
  // Default: fetch caches data (static generation)
  return <div>{...}</div>;
}

// ISR (Incremental Static Regeneration) - revalidate in background
// App Router
export default async function Page() {
  const data = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 } // Regenerate every hour
  });
  return <div>{...}</div>;
}

// On-demand revalidation
// API route
export async function POST(request) {
  revalidatePath('/blog');
  revalidateTag('posts');
  return Response.json({ revalidated: true });
}
```

## 3. What are React Server Components in Next.js?

**Answer:**

```javascript
// Server Components - default in App Router
// They run on the server, reducing bundle size

// app/products/page.js - Server Component
import ProductList from '@/components/ProductList';
import { db } from '@/lib/db';

export default async function ProductsPage() {
  // Direct database access (no API route needed)
  const products = await db.query('SELECT * FROM products');
  
  return (
    <div>
      <h1>Products</h1>
      <ProductList products={products} />
    </div>
  );
}

// Client Component (when you need interactivity)
// app/components/AddToCartButton.js
'use client';

import { useState } from 'react';

export default function AddToCartButton({ productId }) {
  const [added, setAdded] = useState(false);
  
  return (
    <button onClick={() => setAdded(true)}>
      {added ? 'Added!' : 'Add to Cart'}
    </button>
  );
}

// Hybrid - Server + Client components
// app/page.js - Server Component
export default function Page() {
  return (
    <div>
      {/* Server component renders on server */}
      <ProductDescription />
      
      {/* Client component injected into server-rendered HTML */}
      <AddToCartButton productId={123} />
    </div>
  );
}
```

**Server Component Benefits:**
- Zero client-side JavaScript
- Direct backend access (DB, files, etc.)
- Automatic code splitting
- Smaller bundles

## 4. How do Server Actions work?

**Answer:**

```javascript
// Server Actions - server-side functions callable from the client

// app/actions/users.js
'use server';

import { revalidatePath } from 'next/cache';
import { db } from '@/lib/db';

export async function createUser(formData) {
  const name = formData.get('name');
  const email = formData.get('email');
  
  // Server-side validation
  if (!name || !email) {
    return { error: 'Name and email required' };
  }
  
  // Database operation
  await db.query('INSERT INTO users (name, email) VALUES ($1, $2)', [name, email]);
  
  // Revalidate cache
  revalidatePath('/users');
  
  return { success: true };
}

export async function deleteUser(userId) {
  // Authorization check
  const session = await getSession();
  if (!session.isAdmin) {
    throw new Error('Unauthorized');
  }
  
  await db.query('DELETE FROM users WHERE id = $1', [userId]);
  revalidatePath('/users');
}

// Using Server Actions in forms
// app/users/page.js
import { createUser } from '@/app/actions/users';

export default function UsersPage() {
  return (
    <form action={createUser}>
      <input name="name" required />
      <input name="email" type="email" required />
      <button type="submit">Create User</button>
    </form>
  );
}

// Using Server Actions with buttons
// app/users/[id]/page.js
import { deleteUser } from '@/app/actions/users';

export default function UserPage({ params }) {
  return (
    <form action={deleteUser.bind(null, params.id)}>
      <button type="submit">Delete User</button>
    </form>
  );
}
```

## 5. How does middleware work in Next.js?

**Answer:**

```javascript
// middleware.ts - runs before every request
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const { pathname } = request.nextUrl;
  const token = request.cookies.get('token');
  
  // Authentication
  if (pathname.startsWith('/dashboard') && !token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  
  // Role-based access
  if (pathname.startsWith('/admin')) {
    const role = request.cookies.get('role');
    if (role !== 'admin') {
      return NextResponse.redirect(new URL('/unauthorized', request.url));
    }
  }
  
  // Geolocation-based redirect
  const country = request.geo?.country || 'US';
  if (country === 'UK' && pathname === '/pricing') {
    return NextResponse.redirect(new URL('/pricing-uk', request.url));
  }
  
  // Add headers
  const response = NextResponse.next();
  response.headers.set('x-custom-header', 'value');
  response.headers.set('x-request-id', crypto.randomUUID());
  
  // Continue
  return response;
}

// Matcher configuration
export const config = {
  matcher: [
    '/dashboard/:path*',
    '/admin/:path*',
    '/api/protected/:path*',
    // Exclude static files
    '/((?!_next/static|_next/image|favicon.ico).*)',
  ],
};

// A/B testing middleware
export function middleware(request) {
  const variant = Math.random() > 0.5 ? 'A' : 'B';
  const response = NextResponse.next();
  response.cookies.set('ab-test', variant);
  
  if (request.nextUrl.pathname === '/landing' && variant === 'B') {
    request.nextUrl.pathname = '/landing-v2';
    return NextResponse.rewrite(request.nextUrl);
  }
  
  return response;
}
```

## 6. Explain caching strategies in Next.js 13+.

**Answer:**

```javascript
// Next.js 13+ has 4 layers of caching

// 1. Request Memoization (per render pass)
// Multiple fetch() calls to same URL deduplicated
async function getData() {
  const a = await fetch('https://api.example.com/data'); // Fetched once
  const b = await fetch('https://api.example.com/data'); // Reused from cache
  return { a, b };
}

// 2. Data Cache (persistent across deployments)
// fetch with cache: 'force-cache' (default)
await fetch('https://api.example.com/data'); // Cached until revalidated

// 3. Full Route Cache (static HTML pages)
// Generated at build time, served as static files

// 4. Router Cache (client-side, navigation)
// Prefetched routes cached for instant back/forward navigation

// Cache control for fetch
export default async function Page() {
  // Force dynamic (no cache)
  const dynamic = await fetch('https://api.example.com/data', {
    cache: 'no-store'
  });
  
  // Cache with revalidation
  const revalidated = await fetch('https://api.example.com/data', {
    next: { revalidate: 3600 } // Cache for 1 hour
  });
  
  // Cache with tags (on-demand revalidation)
  const tagged = await fetch('https://api.example.com/data', {
    next: { tags: ['posts'] }
  });
  
  return <div>...</div>;
}

// Opting out of caching
export const dynamic = 'force-dynamic'; // Full route opt-out

// Segment-level caching
export const revalidate = 3600; // ISR for this route segment
```

## 7. How does streaming work in Next.js?

**Answer:**

```javascript
// Streaming sends HTML progressively as it renders

// app/dashboard/page.js
import { Suspense } from 'react';

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      
      {/* Wrapped components stream in independently */}
      <Suspense fallback={<ProfileSkeleton />}>
        <ProfileSection />
      </Suspense>
      
      <Suspense fallback={<StatsSkeleton />}>
        <StatsSection />
      </Suspense>
      
      <Suspense fallback={<ActivitySkeleton />}>
        <RecentActivity />
      </Suspense>
    </div>
  );
}

// Each Suspense boundary streams independently
async function ProfileSection() {
  // Simulate slow data fetch
  const profile = await fetch('http://slow-api/profile').then(r => r.json());
  return <ProfileCard profile={profile} />;
}

// loading.js for automatic Suspense boundaries
// app/dashboard/loading.js
export default function Loading() {
  return <DashboardSkeleton />;
}

// Streaming with edge runtime
export const runtime = 'edge'; // Faster TTFB for dynamic content

// Benefits:
// - Faster Time to First Byte (TTFB)
// - Progressive rendering
// - Better perceived performance
// - Works with Suspense for granular loading states
```

## 8. What are layouts in App Router?

**Answer:**

```javascript
// app/layout.js - Root layout (wraps all pages)
export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body>
        <Header />
        <nav>
          <Link href="/">Home</Link>
          <Link href="/about">About</Link>
          <Link href="/dashboard">Dashboard</Link>
        </nav>
        {children} {/* Page content renders here */}
        <Footer />
      </body>
    </html>
  );
}

// Nested layout - app/dashboard/layout.js
export default function DashboardLayout({ children }) {
  return (
    <div className="dashboard-layout">
      <aside>
        <DashboardNav />
      </aside>
      <main>{children}</main>
    </div>
  );
}

// Layout persistence - navigation between dashboard pages
// preserves sidebar state (no re-render)

// Template - similar to layout but creates new instance on each navigation
// app/dashboard/template.js
'use client';
export default function Template({ children }) {
  const [count, setCount] = useState(0);
  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      {children}
    </div>
  );
  // Template state resets on navigation (layout state persists)
}
```

## 9. How do you handle authentication in Next.js?

**Answer:**

```javascript
// Using NextAuth.js / Auth.js
// app/api/auth/[...nextauth]/route.js
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';
import GitHub from 'next-auth/providers/github';

const handler = NextAuth({
  providers: [
    GitHub({
      clientId: process.env.GITHUB_ID,
      clientSecret: process.env.GITHUB_SECRET,
    }),
    Credentials({
      credentials: {
        email: { label: 'Email', type: 'email' },
        password: { label: 'Password', type: 'password' }
      },
      async authorize(credentials) {
        const user = await authenticateUser(credentials);
        return user || null;
      }
    })
  ],
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.role = user.role;
        token.id = user.id;
      }
      return token;
    },
    async session({ session, token }) {
      session.user.role = token.role;
      session.user.id = token.id;
      return session;
    }
  },
  pages: {
    signIn: '/login',
    error: '/error',
  },
  session: {
    strategy: 'jwt',
    maxAge: 30 * 24 * 60 * 60, // 30 days
  }
});

export { handler as GET, handler as POST };

// Protecting Server Components
// app/dashboard/page.js
import { getServerSession } from 'next-auth';
import { redirect } from 'next/navigation';

export default async function Dashboard() {
  const session = await getServerSession();
  
  if (!session) {
    redirect('/login');
  }
  
  return <div>Welcome {session.user.name}</div>;
}

// Protecting API Routes
// app/api/protected/route.js
import { getServerSession } from 'next-auth';

export async function GET() {
  const session = await getServerSession();
  
  if (!session || session.user.role !== 'admin') {
    return new Response('Unauthorized', { status: 401 });
  }
  
  return Response.json({ data: 'sensitive' });
}

// Client-side auth
// app/components/AuthButton.js
'use client';
import { signIn, signOut, useSession } from 'next-auth/react';

export default function AuthButton() {
  const { data: session, status } = useSession();
  
  if (status === 'loading') return <div>Loading...</div>;
  
  if (session) {
    return (
      <div>
        Signed in as {session.user.email}
        <button onClick={() => signOut()}>Sign out</button>
      </div>
    );
  }
  
  return <button onClick={() => signIn()}>Sign in</button>;
}
```

## 10. How do you deploy a Next.js application?

**Answer:**

```javascript
// Deployment options and considerations

// Vercel (recommended - built by Next.js team)
// vercel.json
{
  "buildCommand": "next build",
  "outputDirectory": ".next",
  "framework": "nextjs",
  "regions": ["iad1", "hnd1"], // Deploy to multiple regions
  "crons": [
    {
      "path": "/api/cron",
      "schedule": "0 0 * * *"
    }
  ]
}

// Docker Deployment
// Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]

// Self-hosted with PM2
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'next-app',
    script: 'node_modules/next/dist/bin/next',
    args: 'start',
    env: { NODE_ENV: 'production', PORT: 3000 },
    instances: 4, // Cluster mode
    exec_mode: 'cluster',
    max_memory_restart: '1G',
  }]
};

// Environment Variables
// .env.local (development)
DATABASE_URL="postgresql://..."
AUTH_SECRET="..."

// .env.production (all environments)
NEXT_PUBLIC_API_URL="https://api.example.com"

// Build configuration
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'standalone', // For Docker deployments (smaller image)
  images: {
    remotePatterns: [
      { protocol: 'https', hostname: 'images.unsplash.com' },
    ],
  },
  experimental: {
    serverActions: true,
  },
  headers: async () => [
    {
      source: '/(.*)',
      headers: [
        { key: 'X-Frame-Options', value: 'DENY' },
        { key: 'X-Content-Type-Options', value: 'nosniff' },
        { key: 'Referrer-Policy', value: 'origin-when-cross-origin' },
      ],
    },
  ],
};

module.exports = nextConfig;
```

## 11. Explain the Next.js `Image` component.

**Answer:**

```javascript
import Image from 'next/image';

// Basic usage (automatic optimization)
export default function Page() {
  return (
    <Image
      src="/hero.jpg"
      alt="Hero image"
      width={1200}
      height={600}
      priority // LCP image - load immediately
    />
  );
}

// Responsive images
<Image
  src="/photo.jpg"
  alt="Photo"
  fill // Fill parent container
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  style={{ objectFit: 'cover' }}
/>

// Remote images (need domain in config)
// next.config.js
images: {
  remotePatterns: [
    { protocol: 'https', hostname: 'cdn.example.com' },
  ],
}

// Using remote images
<Image
  src="https://cdn.example.com/image.jpg"
  alt="Remote image"
  width={800}
  height={600}
/>

// Placeholder while loading
<Image
  src="/large-image.jpg"
  alt="Large"
  width={1920}
  height={1080}
  placeholder="blur" // Show blur placeholder
  blurDataURL="data:image/jpeg;base64,..." // Base64 blur
/>

// Image optimization features:
// - WebP/AVIF conversion (automatic)
// - Responsive srcset generation
// - Lazy loading (default)
// - Priority loading (LCP)
// - Blur placeholder
// - CLS prevention (requires width/height or fill)
```

## 12. How does the Next.js `Link` component differ from `<a>`?

**Answer:**

```javascript
import Link from 'next/link';
import { useRouter } from 'next/navigation';

// Link component - prefetches and navigates without full page reload
export default function Navigation() {
  return (
    <nav>
      {/* Link prefetches page in background (for visible links) */}
      <Link href="/">Home</Link>
      
      {/* Dynamic routes */}
      <Link href={`/posts/${post.id}`}>{post.title}</Link>
      
      {/* With query parameters */}
      <Link href={{ pathname: '/search', query: { q: 'nextjs' } }}>
        Search Results
      </Link>
      
      {/* Replace instead of push */}
      <Link href="/dashboard" replace>Dashboard</Link>
      
      {/* Scroll to top (default true) */}
      <Link href="/about" scroll={false}>About</Link>
      
      {/* Client-side navigation (no full page reload) */}
      <Link href="/settings">Settings</Link>
    </nav>
  );
}

// Programmatic navigation (useRouter)
function SubmitButton() {
  const router = useRouter();
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    await saveData();
    router.push('/success'); // Navigate after async operation
    // router.replace('/success'); // Without history entry
    // router.refresh(); // Refresh current route (Server Components)
    // router.back(); // Go back
    // router.forward(); // Go forward
  };
  
  return <button onClick={handleSubmit}>Submit</button>;
}

// Link vs a tag:
// Link: Client-side navigation, prefetching, no page reload
// a tag: Full page reload, loss of state, slower
```

## 13. What are Route Handlers (API routes) in App Router?

**Answer:**

```javascript
// Route handlers replace API routes from Pages Router
// app/api/users/route.js

// GET handler
export async function GET(request) {
  const users = await db.query('SELECT * FROM users');
  return Response.json(users);
}

// POST handler
export async function POST(request) {
  const body = await request.json();
  const user = await db.query(
    'INSERT INTO users (name, email) VALUES ($1, $2) RETURNING *',
    [body.name, body.email]
  );
  return Response.json(user[0], { status: 201 });
}

// Dynamic route handlers
// app/api/users/[id]/route.js
export async function GET(request, { params }) {
  const user = await db.query('SELECT * FROM users WHERE id = $1', [params.id]);
  
  if (!user[0]) {
    return new Response('User not found', { status: 404 });
  }
  
  return Response.json(user[0]);
}

export async function DELETE(request, { params }) {
  await db.query('DELETE FROM users WHERE id = $1', [params.id]);
  return new Response(null, { status: 204 });
}

// Request body parsing
export async function POST(request) {
  const formData = await request.formData();
  const json = await request.json();
  const text = await request.text();
  const blob = await request.blob();
  
  return Response.json({ received: true });
}

// Redirects
export async function GET(request) {
  return Response.redirect(new URL('/new-path', request.url));
  // Or permanent redirect
  return Response.redirect(new URL('/new-path', request.url), 301);
}

// Headers
export async function GET() {
  return new Response(JSON.stringify({ data: 'test' }), {
    status: 200,
    headers: {
      'Content-Type': 'application/json',
      'Cache-Control': 's-maxage=60, stale-while-revalidate',
    },
  });
}
```

## 14. How do you handle forms in Next.js?

**Answer:**

```javascript
// Client-side form with validation
// app/contact/page.js
'use client';

import { useState } from 'react';

export default function ContactForm() {
  const [state, setState] = useState({ name: '', email: '', message: '' });
  const [errors, setErrors] = useState({});
  const [status, setStatus] = useState('idle');
  
  const validate = () => {
    const newErrors = {};
    if (!state.name) newErrors.name = 'Required';
    if (!state.email.includes('@')) newErrors.email = 'Invalid email';
    if (state.message.length < 10) newErrors.message = 'Too short';
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!validate()) return;
    
    setStatus('loading');
    const res = await fetch('/api/contact', {
      method: 'POST',
      body: JSON.stringify(state),
    });
    
    if (res.ok) {
      setStatus('success');
      setState({ name: '', email: '', message: '' });
    } else {
      setStatus('error');
    }
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={state.name} onChange={e => setState(s => ({ ...s, name: e.target.value }))} />
      {errors.name && <span>{errors.name}</span>}
      
      <input name="email" value={state.email} onChange={...} />
      {errors.email && <span>{errors.email}</span>}
      
      <textarea name="message" value={state.message} onChange={...} />
      {errors.message && <span>{errors.message}</span>}
      
      <button disabled={status === 'loading'}>
        {status === 'loading' ? 'Sending...' : 'Send'}
      </button>
      {status === 'success' && <p>Message sent!</p>}
    </form>
  );
}

// Server Action form (simpler, no JavaScript required)
// app/contact/page.js
import { submitContact } from '@/app/actions';

export default function ContactPage() {
  return (
    <form action={submitContact}>
      <input name="name" required />
      <input name="email" type="email" required />
      <textarea name="message" required minLength={10} />
      <button type="submit">Send</button>
    </form>
  );
}
```

## 15. Explain static and dynamic metadata in Next.js.

**Answer:**

```javascript
// Static metadata (exported from layout or page)
// app/page.js
export const metadata = {
  title: 'Home Page',
  description: 'Welcome to our site',
  openGraph: {
    title: 'Home',
    description: 'Home page description',
    images: ['/og-image.png'],
  },
};

// app/layout.js - Root layout metadata
export const metadata = {
  title: {
    default: 'My App',
    template: '%s | My App', // Page titles use this format
  },
  description: 'My application description',
  icons: {
    icon: '/favicon.ico',
    apple: '/apple-icon.png',
  },
  manifest: '/manifest.json',
};

// Dynamic metadata (based on params, data, etc.)
// app/posts/[id]/page.js
export async function generateMetadata({ params, searchParams }) {
  const post = await getPost(params.id);
  
  return {
    title: post.title,
    description: post.excerpt,
    openGraph: {
      title: post.title,
      description: post.excerpt,
      images: [post.coverImage],
    },
    // Revalidate metadata
    other: {
      'article:published_time': post.date,
      'article:author': post.author,
    },
  };
}

// Generating metadata for listing pages
export async function generateStaticParams() {
  const posts = await getAllPosts();
  return posts.map(post => ({ id: post.id }));
}

// JSON-LD structured data
// app/page.js
export default function Home() {
  const jsonLd = {
    '@context': 'https://schema.org',
    '@type': 'WebSite',
    name: 'My Website',
    url: 'https://example.com',
  };
  
  return (
    <section>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      <h1>Home</h1>
    </section>
  );
}
```

## 16. What is the `next.config.js` file used for?

**Answer:**

```javascript
// next.config.js - configuration for Next.js behavior
/** @type {import('next').NextConfig} */
const nextConfig = {
  // Build configuration
  output: 'standalone', // For Docker
  
  // Image optimization
  images: {
    formats: ['image/avif', 'image/webp'],
    deviceSizes: [640, 750, 828, 1080, 1200, 1920, 2048, 3840],
    imageSizes: [16, 32, 48, 64, 96, 128, 256, 384],
  },
  
  // Internationalization
  i18n: {
    locales: ['en', 'es', 'fr'],
    defaultLocale: 'en',
    localeDetection: true,
  },
  
  // Environment variables (public)
  publicRuntimeConfig: {
    staticFolder: '/static',
  },
  
  // Redirects
  async redirects() {
    return [
      { source: '/old-page', destination: '/new-page', permanent: true },
      { source: '/blog/:slug', destination: '/posts/:slug', permanent: true },
    ];
  },
  
  // Rewrites (proxy)
  async rewrites() {
    return [
      { source: '/api/external/:path*', destination: 'https://external-api.com/:path*' },
    ];
  },
  
  // Headers
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          { key: 'X-Frame-Options', value: 'DENY' },
          { key: 'X-Content-Type-Options', value: 'nosniff' },
        ],
      },
      {
        source: '/fonts/(.*)',
        headers: [
          { key: 'Cache-Control', value: 'public, max-age=31536000, immutable' },
        ],
      },
    ];
  },
  
  // Webpack customization
  webpack: (config, { isServer }) => {
    if (!isServer) {
      config.resolve.fallback = { fs: false, net: false, tls: false };
    }
    return config;
  },
  
  // Experimental features
  experimental: {
    serverActions: true,
    scrollRestoration: true,
  },
  
  // Compression
  compress: true,
  
  // Custom build directory
  distDir: 'build',
  
  // Enable React strict mode
  reactStrictMode: true,
  
  // Powering header
  poweredByHeader: false,
  
  // Compress responses with gzip
  compress: true,
};

module.exports = nextConfig;
```

## 17. What are route groups in App Router?

**Answer:**

```javascript
// Route groups allow organizing routes without affecting URL structure

// app/(marketing)/page.js -> /
// app/(marketing)/about/page.js -> /about

// app/(dashboard)/dashboard/page.js -> /dashboard
// app/(dashboard)/dashboard/settings/page.js -> /dashboard/settings

// (auth) group
// app/(auth)/login/page.js -> /login
// app/(auth)/register/page.js -> /register

// Each group can have its own layout
// app/(marketing)/layout.js - Marketing layout
// app/(dashboard)/layout.js - Dashboard layout (with sidebar)
// app/(auth)/layout.js - Auth layout (centered form)

// Route groups help:
// 1. Organize files logically
// 2. Share layouts within groups
// 3. Prevent layout nesting
// 4. Separate concerns (public vs authenticated)

// Example: Two different layouts for different sections
// app/(public)/layout.js
export default function PublicLayout({ children }) {
  return (
    <div className="public-layout">
      <PublicHeader />
      {children}
      <PublicFooter />
    </div>
  );
}

// app/(private)/layout.js
export default function PrivateLayout({ children }) {
  return (
    <div className="private-layout">
      <Sidebar />
      <main>{children}</main>
    </div>
  );
}
```

## 18. How does the `generateStaticParams` function work?

**Answer:**

```javascript
// generateStaticParams works with dynamic segments to generate static pages
// Similar to getStaticPaths in Pages Router

// app/posts/[id]/page.js
export async function generateStaticParams() {
  // Fetch all post IDs at build time
  const posts = await fetch('https://api.example.com/posts').then(r => r.json());
  
  return posts.map(post => ({
    id: post.id.toString(),
  }));
  // Returns: [{ id: '1' }, { id: '2' }, { id: '3' }]
}

export default async function PostPage({ params }) {
  const post = await fetch(`https://api.example.com/posts/${params.id}`).then(r => r.json());
  return <article>{post.title}</article>;
}

// Multiple params
// app/[category]/[product]/page.js
export async function generateStaticParams() {
  const products = await fetch('https://api.example.com/products').then(r => r.json());
  
  return products.map(product => ({
    category: product.categorySlug,
    product: product.slug,
  }));
}

// With revalidation
export const revalidate = 3600; // Revalidate every hour

// Fallback behavior
export const dynamicParams = true; // Default - generate on-demand for non-prebuilt paths
// dynamicParams = false - return 404 for non-prebuilt paths
```

## 19. Explain Next.js 14/15 Turbopack and the bundling approach.

**Answer:**

```javascript
// Turbopack is the incremental bundler built by Vercel (Rust-based)

// Enable in development
// next.config.js
module.exports = {
  // Enabled by default in Next.js 15
  // For Next.js 14: add --turbo to dev script
};

// Benefits over webpack:
// - Rust-based (faster than JavaScript)
// - Incremental computation (cache everything)
// - Parallel processing
// - Hot Module Replacement (HMR) is 10x+ faster
// - Cold starts are much faster

// What Turbopack handles:
// - JavaScript and TypeScript
// - CSS, CSS Modules, PostCSS
// - Images and fonts
// - JSX/TSX
// - Environment variables

// package.json
{
  "scripts": {
    "dev": "next dev --turbo", // Next.js 14
    "dev": "next dev", // Next.js 15 (turbo by default)
  }
}

// Known limitations (may vary by version):
// Some webpack plugins may not work
// Certain custom configurations need adaptation

// For production builds, Next.js still uses webpack (or custom bundler)
// Turbopack is currently for development only
```

## 20. How do you implement error handling in App Router?

**Answer:**

```javascript
// error.js - Error boundary for route segment
// app/dashboard/error.js
'use client';

export default function Error({
  error,
  reset,
}) {
  return (
    <div className="error">
      <h2>Something went wrong!</h2>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}

// error.js catches errors in:
// - Page components
// - Layout components
// - Template components
// - NOT global error (use app/global-error.js)

// Nested error boundaries
// app/error.js - catches errors in all pages
// app/dashboard/error.js - catches only dashboard errors
// app/dashboard/settings/error.js - catches only settings errors

// global-error.js - replaces entire HTML (use sparingly)
// app/global-error.js
'use client';

export default function GlobalError({ error, reset }) {
  return (
    <html>
      <body>
        <h2>Critical error!</h2>
        <button onClick={() => reset()}>Reload</button>
      </body>
    </html>
  );
}

// not-found.js - 404 page for route segment
// app/not-found.js
export default function NotFound() {
  return (
    <div>
      <h2>Page not found</h2>
      <p>Could not find requested resource</p>
    </div>
  );
}

// Programmatic 404
// app/posts/[id]/page.js
import { notFound } from 'next/navigation';

export default async function PostPage({ params }) {
  const post = await getPost(params.id);
  
  if (!post) {
    notFound(); // Shows closest not-found.js
  }
  
  return <div>{post.title}</div>;
}
```
