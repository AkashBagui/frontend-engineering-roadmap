# Semantic HTML

## What is Semantic HTML?

**Semantic HTML** means using HTML elements that carry meaning about the structure and content of the page — not just how it looks.

```
 ┌──────────────────────────────────────────┐
 │     Non-Semantic (❌)                    │
 │  <div id="header">Header</div>           │
 │  <div class="nav">Links</div>            │
 │  <div id="main">Content</div>            │
 │  <div class="footer">Footer</div>        │
 │                                          │
 │     Semantic (✅)                         │
 │  <header>Header</header>                 │
 │  <nav>Links</nav>                        │
 │  <main>Content</main>                    │
 │  <footer>Footer</footer>                 │
 └──────────────────────────────────────────┘
```

## Why Semantics Matter

### 1. SEO (Search Engine Optimization)

Search engines use HTML tags to understand page content:
- `<h1>` signals the main topic
- `<nav>` identifies navigation
- `<article>` indicates self-contained content
- Proper structure improves search rankings

### 2. Accessibility

Screen readers and assistive technologies rely on semantic elements:
- Users can navigate by landmarks (`<nav>`, `<main>`, `<footer>`)
- Headings create a document outline for quick jumping
- ARIA roles fall back to native semantics

### 3. Code Maintainability

- Easier to read and understand
- Clear visual hierarchy
- Self-documenting structure

## Page Layout with Semantic Elements

```
 ┌──────────────────────────────────────────┐
 │              <header>                     │
 │  ┌────────────────────────────────────┐  │
 │  │  Logo          <nav>               │  │
 │  │                   Home | About |   │  │
 │  │                   Contact          │  │
 │  └────────────────────────────────────┘  │
 ├──────────────────────────────────────────┤
 │              <main>                       │
 │  ┌──────────────┐  ┌──────────────────┐  │
 │  │  <article>    │  │   <aside>        │  │
 │  │  ┌─────────┐  │  │  ┌────────────┐ │  │
 │  │  │<section>│  │  │  │ Related    │ │  │
 │  │  │  <h2>   │  │  │  │ Links      │ │  │
 │  │  │  <p>    │  │  │  └────────────┘ │  │
 │  │  └─────────┘  │  │  ┌────────────┐ │  │
 │  │  ┌─────────┐  │  │  │ Ads        │ │  │
 │  │  │<section>│  │  │  └────────────┘ │  │
 │  │  └─────────┘  │  │                  │  │
 │  └──────────────┘  └──────────────────┘  │
 ├──────────────────────────────────────────┤
 │              <footer>                     │
 │  Copyright | Privacy | Terms             │
 └──────────────────────────────────────────┘
```

## Semantic Elements Reference

| Element | Purpose | Used For |
|---------|---------|----------|
| `<header>` | Introductory content | Logo, navigation, headings |
| `<nav>` | Navigation links | Menus, table of contents |
| `<main>` | Primary content | Unique page content (use once) |
| `<article>` | Self-contained content | Blog posts, news articles |
| `<section>` | Thematic grouping | Chapters, tabs, numbered sections |
| `<aside>` | Tangentially related content | Sidebars, pull quotes |
| `<footer>` | Closing content | Copyright, contact info |
| `<figure>` | Self-contained media | Images, diagrams, code blocks |
| `<figcaption>` | Caption for `<figure>` | Image captions, descriptions |
| `<mark>` | Highlighted text | Search results, emphasis |
| `<time>` | Machine-readable date/time | Dates, durations |
| `<details>` | Expandable details | Accordion, FAQs |
| `<summary>` | Summary for `<details>` | Visible heading for details |
| `<address>` | Contact information | Author info, company address |

## Before vs After Examples

### ❌ Non-Semantic (Div Soup)

```html
<div class="header">
    <div class="nav">
        <a href="/">Home</a>
        <a href="/blog">Blog</a>
    </div>
</div>
<div class="main-content">
    <div class="post">
        <h2>Blog Post Title</h2>
        <div class="meta">Posted on January 1, 2026</div>
        <p>Blog content here...</p>
    </div>
    <div class="sidebar">
        <h3>Related Posts</h3>
        <div class="related-item">Post 1</div>
    </div>
</div>
<div class="footer">Copyright 2026</div>
```

### ✅ Semantic HTML

```html
<header>
    <nav aria-label="Main navigation">
        <a href="/">Home</a>
        <a href="/blog">Blog</a>
    </nav>
</header>

<main>
    <article>
        <header>
            <h2>Blog Post Title</h2>
            <p class="meta">
                Posted on <time datetime="2026-01-01">January 1, 2026</time>
            </p>
        </header>
        <p>Blog content here...</p>
    </article>

    <aside>
        <h3>Related Posts</h3>
        <ul>
            <li><a href="/post1">Post 1</a></li>
            <li><a href="/post2">Post 2</a></li>
        </ul>
    </aside>
</main>

<footer>
    <p>&copy; 2026 My Blog</p>
</footer>
```

## Real-World Example: Blog Article

```html
<article>
    <header>
        <h1>How to Master Semantic HTML</h1>
        <p>
            Published on <time datetime="2026-03-15">March 15, 2026</time>
            by <address>Jane Doe</address>
        </p>
    </header>

    <section>
        <h2>Introduction</h2>
        <p>Semantic HTML is essential for modern web development...</p>
    </section>

    <section>
        <h2>Key Elements</h2>
        <p>Let's explore the most important semantic elements...</p>

        <figure>
            <img src="semantic-layout.png"
                 alt="Diagram showing semantic HTML layout with header, nav, main, aside, and footer">
            <figcaption>Figure 1: Typical semantic HTML page layout</figcaption>
        </figure>
    </section>

    <section>
        <h2>Best Practices</h2>
        <ul>
            <li>Use <mark>one `<main>` element</mark> per page</li>
            <li>Nest `<h1>`-`<h6>` headings hierarchically</li>
            <li>Use `<nav>` only for <mark>primary navigation</mark></li>
            <li>Don't use `<article>` solely for styling</li>
        </ul>
    </section>
</article>
```

## Heading Hierarchy

Headings create a document outline that assistive technologies use for navigation.

```html
<h1>Page Title (Used once)</h1>
    <h2>Section Title</h2>
        <h3>Sub-section Title</h3>
    <h2>Another Section</h2>
        <h3>Sub-section</h3>
            <h4>Detail</h4>
```

> **⚠️ Never skip heading levels** (e.g., go from `h2` to `h4`). Use CSS for styling, not wrong heading levels.

## Common Mistakes

| Mistake | Reason | Fix |
|---------|--------|-----|
| Using `<div>` for navigation | No semantic meaning | Use `<nav>` |
| Multiple `<main>` elements | Invalid HTML | Only one `<main>` per page |
| Using `<br>` for spacing | Structural misuse | Use CSS `margin`/`padding` |
| `<i>` for icons | `<i>` means italic voice | Use `<span>` with CSS |
| `<b>` for bold text | `<b>` means stylistic offset | Use `<strong>` for importance |
| Using `<article>` inside `<article>` for no reason | Nested incorrectly | Only nest when one article comments on another |

## Accessibility Landmarks

Screen readers use semantic elements as **landmarks** for navigation:

| Landmark | Default Role | Equivalent ARIA |
|----------|--------------|-----------------|
| `<header>` | `banner` (when in body context) | `role="banner"` |
| `<nav>` | `navigation` | `role="navigation"` |
| `<main>` | `main` | `role="main"` |
| `<article>` | `article` | `role="article"` |
| `<section>` | `region` (if has accessible name) | `role="region"` |
| `<aside>` | `complementary` | `role="complementary"` |
| `<footer>` | `contentinfo` (when in body context) | `role="contentinfo"` |
| `<form>` | `form` | `role="form"` |

## Key Takeaways

1. Semantic elements describe **what** content is, not how it looks.
2. They improve SEO, accessibility, and maintainability.
3. Use `<header>`, `<nav>`, `<main>`, `<footer>` as landmarks.
4. Maintain a logical heading hierarchy (h1 → h2 → h3).
5. Always use `<figure>` + `<figcaption>` for self-contained media.

---

**Next:** [04-Tables.md](04-Tables.md) — Building and styling HTML tables.
