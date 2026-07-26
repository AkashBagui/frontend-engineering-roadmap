# Next.js Projects

## Project 1: Blog Platform (SSG + ISR)

**Goal:** Build a production-ready blog platform with a headless CMS, dynamic content, and optimal performance.

### Tech Stack
- Next.js 15 (App Router)
- MDX or Contentlayer for content
- Tailwind CSS for styling
- Auth.js for authentication
- Prisma + SQLite for comments

### Requirements

#### Content Architecture

```
content/
  blog/
    hello-world.mdx
    nextjs-guide.mdx
    ...
  authors/
    john.mdx
    mary.mdx
```

#### Pages

| Route | Rendering | Description |
|-------|-----------|-------------|
| `/` | Static | Blog home with featured + recent posts |
| `/blog` | Static | Paginated post list |
| `/blog/[slug]` | ISR (revalidate: 3600) | Individual post |
| `/blog/[slug]/comments` | Dynamic | Comments section |
| `/authors` | Static | All authors |
| `/authors/[slug]` | Static | Author bio + posts |
| `/tags` | Static | All tags |
| `/tags/[tag]` | Static | Posts by tag |
| `/api/revalidate` | Dynamic | Webhook endpoint for on-demand ISR |

#### Features

1. **Homepage**
   - Featured posts section
   - Recent posts grid (6 latest)
   - Newsletter signup form (Server Action)
   - Categories/tags sidebar

2. **Blog Post**
   - MDX content with custom components (CodeBlock, Image, Alert)
   - Table of contents (auto-generated from headings)
   - Reading time estimate
   - Share buttons (copy link, Twitter, LinkedIn)
   - Related posts at the bottom
   - Comment section (if authenticated)

3. **Author Pages**
   - Avatar, bio, social links
   - List of authored posts
   - Contact form (Server Action)

4. **Search**
   - Client-side search using Fuse.js
   - Search dialog with keyboard shortcut (Ctrl+K)
   - Search results with highlighted terms

5. **RSS Feed**
   ```tsx
   // app/feed.xml/route.ts
   export async function GET() {
     const posts = await getAllPosts();
     const feed = generateRSS(posts);
     return new Response(feed, {
       headers: { 'Content-Type': 'application/xml' },
     });
   }
   ```

#### Performance Targets

```
Lighthouse score: ≥ 95
LCP: < 1.5s
CLS: < 0.1
First load JS: < 100 KB
```

#### File Structure

```
src/
  app/
    layout.tsx
    page.tsx            # Homepage
    blog/
      page.tsx          # Blog list
      [slug]/
        page.tsx        # Post page
        not-found.tsx   # 404 for missing posts
    authors/
      page.tsx
      [slug]/page.tsx
    tags/
      page.tsx
      [tag]/page.tsx
    api/
      revalidate/
        route.ts        # ISR webhook
      newsletter/
        route.ts        # Newsletter signup
    feed.xml/
      route.ts          # RSS feed
  components/
    blog/
      PostCard.tsx
      PostContent.tsx
      TableOfContents.tsx
      CommentSection.tsx
      ShareButtons.tsx
    ui/
      SearchDialog.tsx
      NewsletterForm.tsx
      CodeBlock.tsx
      Pagination.tsx
  lib/
    posts.ts            # MDX content helpers
    authors.ts
    search.ts
    rss.ts
  content/
    blog/
    authors/
```

### Deployment

- Host on Vercel
- ISR via webhooks from CMS
- CDN caching for static pages

---

## Project 2: Company Website (SSR + Server Components)

**Goal:** A marketing and company website with dynamic sections, team pages, and a contact form.

### Tech Stack
- Next.js 15 (App Router)
- Tailwind CSS + Framer Motion
- Sanity CMS (headless)
- Resend for transactional emails

### Pages

| Route | Rendering | Description |
|-------|-----------|-------------|
| `/` | ISR (10 min) | Hero, featured products, testimonials |
| `/about` | Static | Company story, team |
| `/products` | ISR (5 min) | Product listing |
| `/products/[slug]` | ISR (5 min) | Product detail |
| `/pricing` | Static | Pricing tiers |
| `/blog` | ISR (1 hour) | Company blog |
| `/careers` | Static | Open positions |
| `/careers/[id]` | Static | Job description |
| `/contact` | Dynamic | Contact form |
| `/team` | Static | Team grid |

### Features

1. **Server Components for Data**
   ```tsx
   // app/page.tsx
   import { HeroSection } from './HeroSection';
   import { Testimonials } from './Testimonials';
   import { FeaturedProducts } from './FeaturedProducts';
   import { NewsletterSection } from './NewsletterSection';

   export default async function HomePage() {
     return (
       <div>
         <HeroSection />
         <Suspense fallback={<ProductGridSkeleton />}>
           <FeaturedProducts />
         </Suspense>
         <Suspense fallback={<TestimonialSkeleton />}>
           <Testimonials />
         </Suspense>
         <NewsletterSection />
       </div>
     );
   }
   ```

2. **Client-Side Interactivity**
   - Mobile navigation hamburger menu
   - Animated counters (Framer Motion)
   - Testimonial carousel
   - Product image gallery
   - Contact form validation + submission

3. **Contact Form with Server Action**
   ```tsx
   // app/contact/actions.ts
   'use server';
   import { z } from 'zod';
   import { Resend } from 'resend';

   const schema = z.object({
     name: z.string().min(2),
     email: z.string().email(),
     message: z.string().min(10),
   });

   export async function submitContact(prevState, formData) {
     const validated = schema.safeParse({
       name: formData.get('name'),
       email: formData.get('email'),
       message: formData.get('message'),
     });

     if (!validated.success) {
       return { errors: validated.error.flatten().fieldErrors };
     }

     await resend.emails.send({
       from: 'noreply@company.com',
       to: 'contact@company.com',
       subject: `Contact from ${validated.data.name}`,
       text: validated.data.message,
     });

     return { success: true };
   }
   ```

4. **SEO**
   ```tsx
   // app/layout.tsx
   export async function generateMetadata() {
     return {
       title: {
         default: 'Company Name',
         template: '%s | Company Name',
       },
       description: 'Company description',
       openGraph: {
         type: 'website',
         locale: 'en_US',
         url: 'https://company.com',
         siteName: 'Company Name',
       },
       robots: {
         index: true,
         follow: true,
       },
     };
   }
   ```

5. **Analytics**
   - Custom analytics endpoint via Route Handler
   - Page view tracking
   - Conversion tracking for contact form
   - Dashboard in /admin

### File Structure

```
src/
  app/
    layout.tsx
    page.tsx
    about/page.tsx
    products/
      page.tsx
      [slug]/page.tsx
    pricing/page.tsx
    blog/
      page.tsx
      [slug]/page.tsx
    careers/
      page.tsx
      [id]/page.tsx
    contact/
      page.tsx
      actions.ts
    team/page.tsx
    api/
      analytics/
        route.ts
    sitemap.ts
    robots.ts
  components/
    marketing/
      HeroSection.tsx
      Testimonials.tsx
      ProductCard.tsx
      PricingCard.tsx
      TeamMember.tsx
      ContactForm.tsx
    layout/
      Header.tsx
      Footer.tsx
      MobileNav.tsx
  lib/
    cms.ts
    analytics.ts
    email.ts
```

---

## Project 3: SaaS Dashboard (App Router + Server Actions + Auth)

**Goal:** A full-featured SaaS admin dashboard with authentication, team management, real-time data, and settings.

### Tech Stack
- Next.js 15 (App Router)
- Auth.js (NextAuth v5)
- Prisma + PostgreSQL
- TanStack Query (for client-side chart data)
- Recharts for charts
- Tailwind CSS

### Pages

| Route | Auth | Description |
|-------|------|-------------|
| `/login` | Guest | Login with email + OAuth |
| `/register` | Guest | Registration |
| `/dashboard` | Auth | Main dashboard with stats |
| `/dashboard/analytics` | Auth | Detailed analytics |
| `/dashboard/team` | Auth (Admin) | Team management |
| `/dashboard/projects` | Auth | Project list |
| `/dashboard/projects/[id]` | Auth | Project detail |
| `/dashboard/settings` | Auth | User settings |
| `/dashboard/settings/billing` | Auth | Billing & plans |
| `/admin` | Auth (Superadmin) | Admin panel |
| `/api/*` | Varies | Route handlers |

### Features

1. **Authentication Flow**
   ```tsx
   // auth.ts
   export const { auth, handlers, signIn, signOut } = NextAuth({
     providers: [GitHub, Google, Credentials],
     adapter: PrismaAdapter(db),
     session: { strategy: 'jwt' },
     callbacks: {
       jwt({ token, user }) {
         if (user) {
           token.role = user.role;
           token.tenantId = user.tenantId;
         }
         return token;
       },
       session({ session, token }) {
         session.user.role = token.role;
         session.user.tenantId = token.tenantId;
         return session;
       },
     },
   });
   ```

2. **Multi-Tenancy**
   ```tsx
   // middleware.ts
   export default auth((req) => {
     const { pathname } = req.nextUrl;
     const isLoggedIn = !!req.auth;

     // Check tenant access
     if (isLoggedIn && pathname.startsWith('/dashboard')) {
       const tenantId = req.auth?.user?.tenantId;
       const pathTenant = pathname.split('/')[2];

       if (pathTenant && pathTenant !== tenantId) {
         return Response.redirect(
           new URL('/dashboard', req.url)
         );
       }
     }
   });
   ```

3. **Dashboard Analytics**
   ```tsx
   // app/dashboard/analytics/page.tsx
   export default async function AnalyticsPage() {
     const session = await auth();
     const { role } = session.user;

     // Role-based data access
     const data = role === 'admin'
       ? await getFullAnalytics()
       : await getUserAnalytics(session.user.id);

     return (
       <div>
         <RevenueChart data={data.revenue} />
         <UserGrowthChart data={data.users} />
         <TopProjectsTable projects={data.topProjects} />
       </div>
     );
   }
   ```

4. **Server Actions for CRUD**
   ```tsx
   // app/dashboard/projects/actions.ts
   'use server';

   export async function createProject(formData: FormData) {
     const session = await auth();
     if (!session?.user) throw new Error('Unauthorized');

     const project = await db.project.create({
       data: {
         name: formData.get('name'),
         description: formData.get('description'),
         tenantId: session.user.tenantId,
         ownerId: session.user.id,
       },
     });

     revalidatePath('/dashboard/projects');
     redirect(`/dashboard/projects/${project.id}`);
   }

   export async function inviteMember(formData: FormData) {
     const session = await auth();
     if (session?.user?.role !== 'admin') throw new Error('Unauthorized');

     const email = formData.get('email');
     const role = formData.get('role');

     const invitation = await db.invitation.create({
       data: {
         email,
         role,
         tenantId: session.user.tenantId,
         invitedById: session.user.id,
       },
     });

     await sendInvitationEmail(email, invitation.token);

     revalidatePath('/dashboard/team');
     return { success: true };
   }
   ```

5. **Billing with Stripe**
   ```tsx
   // app/api/stripe/webhook/route.ts
   export async function POST(request: Request) {
     const body = await request.text();
     const sig = request.headers.get('stripe-signature');

     const event = stripe.webhooks.constructEvent(body, sig, webhookSecret);

     switch (event.type) {
       case 'checkout.session.completed':
         await handleSubscriptionCreated(event.data.object);
         break;
       case 'invoice.payment_succeeded':
         await handlePaymentSuccess(event.data.object);
         break;
       case 'customer.subscription.deleted':
         await handleSubscriptionCancelled(event.data.object);
         break;
     }

     return Response.json({ received: true });
   }
   ```

### File Structure

```
src/
  app/
    (auth)/
      login/page.tsx
      register/page.tsx
    dashboard/
      layout.tsx
      page.tsx
      analytics/page.tsx
      team/
        page.tsx
        actions.ts
      projects/
        page.tsx
        [id]/page.tsx
        actions.ts
      settings/
        page.tsx
        billing/page.tsx
    admin/
      layout.tsx
      page.tsx
      users/page.tsx
      audit-log/page.tsx
    api/
      auth/[...nextauth]/route.ts
      stripe/
        webhook/route.ts
        create-session/route.ts
  components/
    dashboard/
      Sidebar.tsx
      StatsCard.tsx
      RevenueChart.tsx
      ProjectsTable.tsx
      TeamMembersList.tsx
      InviteMemberDialog.tsx
    ui/
      Button.tsx
      Input.tsx
      Dialog.tsx
      Table.tsx
      Tabs.tsx
  lib/
    auth.ts
    db.ts
    stripe.ts
    email.ts
  providers/
    SessionProvider.tsx
```

### Performance Goals

```
Dashboard load: < 2s (TTFB + hydration)
Analytics page with charts: < 3s
Form submissions: < 1s (optimistic updates)
First load JS: < 150 KB (code splitting)
API response time (p95): < 200ms
```
