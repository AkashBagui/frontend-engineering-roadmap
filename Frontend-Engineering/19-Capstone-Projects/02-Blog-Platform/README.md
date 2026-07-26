# Blog Platform

## Project Overview

Build a full-featured blog platform with MDX support, categories, tags, full-text search, comments, RSS feeds, SEO optimization, and dark mode. This project introduces ISR (Incremental Static Regeneration), content management workflows, and advanced Next.js patterns.

## Learning Objectives

- MDX-based content management with custom components
- ISR and on-demand revalidation
- Full-text search implementation (client-side and server-side)
- SEO best practices (structured data, Open Graph, XML sitemaps)
- RSS feed generation
- Comment system architecture (with moderation)
- Reading time estimation and content analytics
- Table of contents generation from headings
- Related content algorithms (tag-based matching)

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| Next.js 14 | Framework | App Router, ISR, API routes |
| TypeScript | Language | Type safety |
| MDX + next-mdx-remote | Content | Write posts in markdown with React components |
| Tailwind CSS | Styling | Typography plugin, dark mode |
| Prisma + Postgres | Database | Comments, views, likes |
| Auth.js | Authentication | Comment authors, admin access |
| Fuse.js or Meilisearch | Search | Client-side or hybrid full-text search |
| React Hook Form + Zod | Forms | Comment validation |
| Vercel | Deployment | ISR support, edge functions |

## Feature List

### MVP Features
- Blog post listing with pagination
- MDX blog posts with custom components (code blocks, images, callouts)
- Categories and tags with filter pages
- Full-text search across posts
- RSS feed (XML generation)
- SEO metadata (Open Graph, Twitter cards, JSON-LD)
- Dark/light theme
- Reading time estimate
- Table of contents (auto-generated from headings)
- Related posts section
- Comment system (create, read, moderate)
- Blog post view counter

### Advanced Features
- Post series/parts functionality
- Newsletter subscription with email integration
- Social share buttons with image generation
- Blog post scheduling (publish at date)
- Content analytics (top posts, traffic sources)
- AI-powered post summaries
- Multi-author support with author pages
- Draft/preview mode (secret URLs)
- Image optimization with Cloudinary
- Webmention / pingback support

## Architecture Diagram

```
src/
├── app/
│   ├── layout.tsx              # Root layout, fonts, theme provider
│   ├── page.tsx                # Home — featured posts, recent
│   ├── blog/
│   │   ├── page.tsx            # Blog listing with pagination
│   │   ├── search/
│   │   │   └── page.tsx        # Search results page
│   │   └── [slug]/
│   │       └── page.tsx        # Individual blog post
│   ├── categories/
│   │   ├── page.tsx            # All categories
│   │   └── [category]/
│   │       └── page.tsx        # Posts in category
│   ├── tags/
│   │   ├── page.tsx            # All tags (tag cloud)
│   │   └── [tag]/
│   │       └── page.tsx        # Posts with tag
│   ├── authors/
│   │   └── [slug]/
│   │       └── page.tsx        # Author page
│   ├── about/
│   │   └── page.tsx            # About / colophon
│   └── api/
│       ├── comments/
│       │   ├── route.ts        # POST — create comment
│       │   └── [id]/
│       │       └── route.ts    # DELETE — moderate comment
│       ├── views/
│       │   └── route.ts        # POST — increment view
│       └── revalidate/
│           └── route.ts        # POST — on-demand ISR revalidation
├── components/
│   ├── layout/
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── ThemeProvider.tsx
│   ├── blog/
│   │   ├── BlogCard.tsx
│   │   ├── BlogList.tsx
│   │   ├── BlogSidebar.tsx
│   │   ├── TableOfContents.tsx
│   │   ├── ReadingTime.tsx
│   │   ├── RelatedPosts.tsx
│   │   └── ShareButtons.tsx
│   ├── comments/
│   │   ├── CommentList.tsx
│   │   ├── CommentForm.tsx
│   │   └── CommentItem.tsx
│   ├── search/
│   │   ├── SearchBar.tsx
│   │   └── SearchResults.tsx
│   └── mdx/
│       ├── CodeBlock.tsx       # Syntax highlighted code
│       ├── Image.tsx           # Optimized image component
│       ├── Callout.tsx         # Info/warning/tip callouts
│       └── Video.tsx           # Embedded video
├── content/
│   └── posts/
│       └── *.mdx              # Blog post files with frontmatter
├── lib/
│   ├── posts.ts               # Fetch, filter, sort posts
│   ├── mdx.ts                 # MDX compilation
│   ├── search.ts              # Search indexing and query
│   ├── comments.ts            # Comment CRUD
│   ├── rss.ts                 # RSS feed generation
│   ├── seo.ts                 # Structured data helpers
│   └── utils.ts
├── types/
│   ├── post.ts
│   ├── comment.ts
│   └── search.ts
└── public/
    └── feed.xml               # Generated RSS feed
```

## Component Tree

```
<RootLayout>
  <ThemeProvider>
    <Header>
      <Logo />
      <SearchBar />            {/* Opens search dialog */}
      <ThemeToggle />
      <NavLinks />
    </Header>
    <main>
      {children}
      <ScrollToTop />
    </main>
    <Footer>
      <SocialLinks />
      <RSSLink />
    </Footer>
  </ThemeProvider>
</RootLayout>

BlogListingPage:
  <BlogList>
    <BlogCard />*              {/* Paginated, with category/tag badges */}
    <Pagination />
  </BlogList>
  <BlogSidebar>
    <SearchWidget />
    <CategoryWidget />
    <TagWidget />              {/* Tag cloud with sizes */}
    <RecentPosts />
  </BlogSidebar>

BlogPostPage:
  <article>
    <PostHeader>
      <CategoryBadge />
      <ReadingTime />
      <PublishDate />
    </PostHeader>
    <TableOfContents />        {/* Sticky sidebar */}
    <MDXContent>
      <CodeBlock />*
      <Image />*
      <Callout />*
    </MDXContent>
    <PostFooter>
      <Tags />
      <ShareButtons />
      <RelatedPosts />
    </PostFooter>
  </article>
  <CommentSection>
    <CommentList>
      <CommentItem />*         {/* Threaded */}
    </CommentList>
    <CommentForm />            {/* With moderation notice */}
  </CommentSection>

SearchPage:
  <SearchInput />
  <SearchResults>
    <BlogCard />*              {/* Highlighted matches */}
  </SearchResults>
```

## Data Flow

### Content Pipeline
```
MDX Files (content/posts/*.mdx)
  → frontmatter parsing (gray-matter)
  → MDX compilation (next-mdx-remote)
  → React components rendering
  → Static generation via getStaticPaths/getStaticProps
  → ISR revalidation on content changes (webhook)
```

### Search Flow
```
User types → Debounce (300ms) → Fuse.js index search (client)
                                                    ↓
                              Results filtered by title, excerpt, tags, categories
                                                    ↓
                              Highlighted matches with BlogCard display
```

### Comment Flow
```
User submits → Zod validation → API route → Database (Prisma)
                                    ↓
                    Admin approves → Public visible
                                    ↓
                    Spam detected → Flagged for review
```

### Revalidation Flow
```
Content CMS / Git push → Webhook → /api/revalidate
                                          ↓
                  On-demand ISR revalidation for affected pages
```

## Route Structure

| Route | Type | Description |
|-------|------|-------------|
| `/` | SSG | Home — featured posts |
| `/blog` | SSG + ISR | Paginated blog listing |
| `/blog/[slug]` | SSG + ISR | Blog post |
| `/blog/search` | Client | Search results |
| `/categories` | SSG | All categories |
| `/categories/[category]` | SSG + ISR | Posts in category |
| `/tags` | SSG | Tag cloud |
| `/tags/[tag]` | SSG + ISR | Posts with tag |
| `/authors/[slug]` | SSG + ISR | Author page |
| `/about` | Static | About page |
| `/api/comments` | API | Comment CRUD |
| `/api/views` | API | View counter |
| `/api/revalidate` | API | ISR revalidation |
| `/feed.xml` | Static | RSS feed |

## Database Schema

```prisma
model Post {
  id          String   @id @default(cuid())
  slug        String   @unique
  title       String
  excerpt     String
  content     String   // Raw MDX content
  coverImage  String?
  published   Boolean  @default(false)
  publishedAt DateTime?
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  views       Int      @default(0)
  categoryId  String?
  category    Category? @relation(fields: [categoryId], references: [id])
  tags        Tag[]
  comments    Comment[]
  authorId    String?
  author      Author?  @relation(fields: [authorId], references: [id])
}

model Category {
  id    String @id @default(cuid())
  slug  String @unique
  name  String
  posts Post[]
}

model Tag {
  id    String @id @default(cuid())
  slug  String @unique
  name  String
  posts Post[]
}

model Comment {
  id        String   @id @default(cuid())
  content   String
  name      String
  email     String
  approved  Boolean  @default(false)
  createdAt DateTime @default(now())
  postId    String
  post      Post     @relation(fields: [postId], references: [id])
  parentId  String?
  parent    Comment?  @relation("CommentReplies", fields: [parentId], references: [id])
  replies   Comment[] @relation("CommentReplies")
}

model Author {
  id        String  @id @default(cuid())
  slug      String  @unique
  name      String
  avatar    String?
  bio       String?
  twitter   String?
  github    String?
  website   String?
  posts     Post[]
}
```

## Key Implementation Considerations

- Use `generateStaticParams` for blog posts, categories, tags — pre-render all known content
- Implement on-demand ISR via webhook when content changes (revalidate only affected paths)
- Use `next-mdx-remote` for MDX compilation (supports custom components per post)
- Implement search index at build time or use Meilisearch for server-side search
- Generate RSS feed at build time using `feed` npm package
- Add JSON-LD structured data for blog posts (Article schema)
- Use `next-sitemap` for XML sitemap generation
- Implement comment throttling (rate limiting via Vercel KV or IP-based)
- Use optimistic updates for comment submission (add to UI immediately)
- Store raw MDX content in database for editing, render via next-mdx-remote

## Performance Considerations

- ISR with `revalidate: 3600` (1 hour) for blog listing, `revalidate: 86400` (1 day) for categories/tags
- Search index loaded lazily via dynamic import
- Comment section lazy loaded (below fold)
- Syntax highlighting for code blocks with `shiki` or `prism-react-renderer` (lazy)
- Image optimization via `next/image` with remote patterns
- Bundle analysis — ensure MDX compilation doesn't bloat client bundle
- Use React.lazy for heavy MDX components (CodeBlock, Video)

## Deployment Strategy

1. **Vercel** — native ISR support, edge functions for API routes
2. **Neon.tech** or **Supabase** for Postgres database
3. **Git-based CMS** — content changes via PR → auto-deploy → ISR revalidation
4. **Environment variables**: database URL, auth secret, email keys
5. **Vercel Cron Jobs** for scheduled tasks (newsletter, analytics aggregation)
6. **Custom domain** with Vercel DNS
7. **CDN** for static assets (images hosted on Cloudinary or Imgix)

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Content strategy, information architecture, schema design | 1 |
| Foundation | Next.js setup, Tailwind typography, layouts, theme | 1 |
| MDX Pipeline | MDX compilation, custom components, frontmatter parsing | 1.5 |
| Content Pages | Blog listing, post detail, categories, tags pages | 2 |
| Search | Search index, search UI, highlight results | 1 |
| Comments | Comment CRUD, moderation, email notifications | 1.5 |
| SEO | Structured data, sitemap, RSS, Open Graph images | 1 |
| Polish | Performance audit, accessibility, responsive pass | 1 |
| Deploy | Database setup, CI/CD, revalidation webhook | 0.5 |
| **Total** | | **~8-10 days** |

## Learning Resources

- [Next.js MDX Guide](https://nextjs.org/docs/app/building-your-application/configuring/mdx)
- [Incremental Static Regeneration](https://nextjs.org/docs/app/building-your-application/data-fetching/incremental-static-regeneration)
- [RSS Feed Generation](https://github.com/jpmonette/feed)
- [JSON-LD Structured Data](https://developers.google.com/search/docs/appearance/structured-data/article)
- [Fuse.js Documentation](https://fusejs.io/)
- [Prisma with Postgres](https://www.prisma.io/docs/orm/overview/databases/postgresql)
- [next-mdx-remote](https://github.com/hashicorp/next-mdx-remote)
