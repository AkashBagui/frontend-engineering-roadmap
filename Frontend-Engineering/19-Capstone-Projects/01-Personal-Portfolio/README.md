# Personal Portfolio

## Project Overview

Build a modern, performant personal portfolio website that showcases projects, includes a blog, supports dark/light theme, has a contact form, and uses smooth page transitions. This is the foundational capstone — it introduces Next.js App Router, Tailwind CSS, Framer Motion, and basic server/client component architecture.

## Learning Objectives

- Next.js App Router (layouts, nested routes, loading states)
- Server Components vs Client Components
- Tailwind CSS theming and responsive design
- Framer Motion animations (page transitions, scroll animations, stagger)
- Form handling and validation
- Static site generation (SSG) and ISR
- SEO optimization with Next.js Metadata API
- Accessibility fundamentals

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| Next.js 14 | Framework | App Router, SSG, ISR, image optimization |
| TypeScript | Language | Type safety, better DX |
| Tailwind CSS | Styling | Utility-first, dark mode, responsive |
| Framer Motion | Animations | Declarative, layout animations, gesture support |
| React Hook Form + Zod | Forms | Type-safe form validation |
| MDX | Blog content | Write posts in markdown with React components |
| Vercel | Deployment | Zero-config, edge functions, analytics |

## Feature List

### MVP Features
- Hero section with animated introduction
- Projects showcase with filtering (by tech, category)
- About page with skills, experience, timeline
- Blog with MDX support, categories, tags
- Contact form with validation and email integration
- Dark/light theme toggle with persistence
- Responsive design (mobile, tablet, desktop)
- Page transitions with Framer Motion
- SEO metadata (Open Graph, Twitter cards, sitemap)
- Loading skeletons and error boundaries

### Advanced Features
- Project detail pages with case study layout
- Blog reading time, table of contents, related posts
- Search across projects and blog posts
- GitHub stats integration (contributions, repos)
- Blog RSS feed
- Analytics integration (Umami or Plausible)
- i18n support (optional)
- Guestbook / testimonials section
- Blog post views counter

## Architecture Diagram

```
src/
├── app/
│   ├── layout.tsx            # Root layout, providers, fonts
│   ├── page.tsx              # Home page / Hero
│   ├── loading.tsx           # Global loading state
│   ├── error.tsx             # Global error boundary
│   ├── not-found.tsx         # Custom 404
│   ├── about/
│   │   └── page.tsx          # About page
│   ├── projects/
│   │   ├── page.tsx          # Projects grid
│   │   └── [slug]/
│   │       └── page.tsx      # Project detail
│   ├── blog/
│   │   ├── page.tsx          # Blog list with pagination
│   │   └── [slug]/
│   │       └── page.tsx      # Blog post
│   ├── contact/
│   │   └── page.tsx          # Contact form
│   └── api/
│       ├── contact/
│       │   └── route.ts      # Email API
│       └── views/
│           └── route.ts      # Blog view counter
├── components/
│   ├── layout/
│   │   ├── Header.tsx        # Navigation, theme toggle
│   │   ├── Footer.tsx
│   │   ├── MobileNav.tsx
│   │   └── ThemeProvider.tsx  # Theme context + persistence
│   ├── ui/
│   │   ├── Button.tsx
│   │   ├── Card.tsx
│   │   ├── Badge.tsx
│   │   ├── Input.tsx
│   │   ├── Skeleton.tsx
│   │   └── ...
│   ├── home/
│   │   ├── Hero.tsx
│   │   ├── FeaturedProjects.tsx
│   │   ├── Skills.tsx
│   │   └── ContactCTA.tsx
│   ├── projects/
│   │   ├── ProjectCard.tsx
│   │   ├── ProjectFilter.tsx
│   │   └── TechBadge.tsx
│   ├── blog/
│   │   ├── BlogCard.tsx
│   │   ├── BlogSidebar.tsx
│   │   ├── TableOfContents.tsx
│   │   └── ReadingTime.tsx
│   └── shared/
│       ├── AnimatedSection.tsx
│       ├── ScrollToTop.tsx
│       └── SEOHead.tsx
├── content/
│   ├── projects/
│   │   └── *.mdx            # Project markdown files
│   └── blog/
│       └── *.mdx            # Blog markdown files
├── hooks/
│   ├── useTheme.ts
│   ├── useScrollPosition.ts
│   ├── useMediaQuery.ts
│   └── useDebounce.ts
├── lib/
│   ├── utils.ts              # cn() helper, formatDate, etc.
│   ├── constants.ts          # Navigation links, social links
│   └── mdx.ts                # MDX compilation utilities
├── styles/
│   └── globals.css           # Tailwind directives, CSS variables
└── types/
    ├── project.ts
    ├── blog.ts
    └── common.ts
```

## Component Tree

```
<RootLayout>
  <ThemeProvider>
    <Header>
      <Logo />
      <NavLinks />
      <ThemeToggle />
      <MobileNav />
    </Header>
    <main>
      <PageTransition>        {/* Framer Motion AnimatePresence */}
        {children}             {/* Route-based page content */}
      </PageTransition>
    </main>
    <Footer />
  </ThemeProvider>
</RootLayout>

HomePage:
  <Hero />                    {/* Animated intro with CTA */}
  <FeaturedProjects>
    <ProjectCard />*          {/* 3 featured projects */}
  </FeaturedProjects>
  <Skills>
    <SkillCategory />*        {/* Grouped by category */}
  </Skills>
  <ContactCTA />              {/* Brief contact section */}

ProjectsPage:
  <ProjectFilter />           {/* Filter by tech/category */}
  <ProjectGrid>
    <ProjectCard />*          {/* All projects */}
  </ProjectGrid>

BlogPage:
  <BlogList>
    <BlogCard />*             {/* Paginated list */}
  </BlogList>
  <BlogSidebar>
    <CategoryWidget />
    <TagWidget />
    <SearchWidget />
  </BlogSidebar>

ContactPage:
  <ContactForm />             {/* Validated with Zod */}
  <SocialLinks />
```

## Data Flow

### Theme Management
```
User Toggle → ThemeContext → localStorage + <html> class
                     ↓
          All components read via useTheme()
```

### Blog Content Pipeline
```
MDX Files (content/blog/) → next-mdx-remote → React Components
         ↓
  generateStaticParams() → SSG at build time
         ↓
  ISR revalidation on demand when content changes
```

### Contact Form Flow
```
User submits → Zod validation (client) → API route → Email service (Resend/SendGrid)
                                    ↓
                           Success/Error toast
```

## Route Structure

| Route | Type | Description |
|-------|------|-------------|
| `/` | SSG | Home page |
| `/about` | SSG | About page |
| `/projects` | SSG | Projects listing |
| `/projects/[slug]` | SSG (dynamic) | Project detail |
| `/blog` | SSG | Blog listing |
| `/blog/[slug]` | SSG (dynamic) | Blog post |
| `/contact` | Static | Contact form |
| `/api/contact` | Serverless | Email API |
| `/api/views` | Serverless | Views counter |

## Key Implementation Considerations

- Use `generateStaticParams` for blog and project pages to pre-render at build time
- Implement `generateMetadata` for dynamic SEO per page
- Use `next/image` with proper sizing, lazy loading, and WebP format
- Implement CSS variables for theming (`--color-*` in `globals.css`)
- Use `clsx` or `cn()` utility for conditional Tailwind classes
- Lazy load below-the-fold components with `next/dynamic`
- Implement proper focus management for theme toggle and mobile nav
- Use `next/font` with `display: swap` for font loading optimization
- Add `rel="preload"` for critical fonts and hero images

## Performance Considerations

- Lighthouse target: 95+ all categories
- Use Next.js `next/image` with proper priority hints
- Implement route-based code splitting (automatic with App Router)
- Lazy load heavy components (MDX content, animations on scroll)
- Use React.memo for card components in lists
- Preload critical CSS and fonts
- Optimize MDX images with next/image in MDX components
- Use SWR or incremental cache for external API data
- Implement virtualization if blog/project list exceeds 50 items

## Deployment Strategy

1. **GitHub repository** with proper branching strategy
2. **Vercel** for hosting (automatic deploys from main branch)
3. **Environment variables**: email API keys, analytics ID
4. **Custom domain** with Vercel DNS
5. **Vercel Analytics** for page views and web vitals
6. **Sitemap** generation with `next-sitemap`
7. **RSS feed** generation at build time

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Wireframes, component tree, content structure | 1 |
| Foundation | Next.js setup, Tailwind config, routing, layout | 1 |
| Theme | Dark/light system, global styles, CSS variables | 0.5 |
| Pages | Home, About, Projects, Blog, Contact | 2 |
| Animations | Page transitions, scroll animations, micro-interactions | 1 |
| Blog MDX | MDX setup, content, reading time, TOC | 1 |
| Polish | Responsive, accessibility, performance, SEO | 1 |
| Deploy | Domain, analytics, sitemap, RSS, CI/CD | 0.5 |
| **Total** | | **~4-6 days** |

## Learning Resources

- [Next.js App Router Documentation](https://nextjs.org/docs)
- [Tailwind CSS Dark Mode](https://tailwindcss.com/docs/dark-mode)
- [Framer Motion Documentation](https://www.framer.com/motion/)
- [MDX with Next.js](https://nextjs.org/docs/app/building-your-application/configuring/mdx)
- [React Hook Form + Zod](https://react-hook-form.com/get-started#IntegratingwithUIlibraries)
- [Vercel Deployment](https://vercel.com/docs)
- [Web.dev Performance Audits](https://web.dev/measure/)
