# HTML Document Structure

## Document Anatomy

Every HTML document follows a tree-like structure called the **DOM** (Document Object Model).

```
                ┌──────────────────────────┐
                │   <!DOCTYPE html>        │
                │   <html lang="en">       │
                └──────────┬───────────────┘
                           │
            ┌──────────────┴──────────────┐
            │                              │
    ┌───────▼───────┐            ┌─────────▼──────────┐
    │    <head>      │            │      <body>        │
    │  (Metadata)    │            │   (Content)        │
    │                │            │                    │
    │  - meta        │            │  - header          │
    │  - title       │            │  - main            │
    │  - link        │            │  - footer          │
    │  - script      │            │  - etc.            │
    └────────────────┘            └────────────────────┘
```

## The `<html>` Element

The root element wraps all content on the page.

```html
<html lang="en" dir="ltr">
```

| Attribute | Purpose |
|-----------|---------|
| `lang` | Declares the document language (helps screen readers, search engines) |
| `dir` | Text direction: `ltr` (left-to-right) or `rtl` (right-to-left) |

## The `<head>` Element

Contains **metadata** — information about the page that is not displayed directly.

```
 ┌───────────────────────────────────┐
 │            <head>                  │
 │                                    │
 │  ┌─────────────┐                   │
 │  │ <meta>      │  Character set,   │
 │  │             │  viewport,        │
 │  │             │  description, OG  │
 │  └─────────────┘                   │
 │  ┌─────────────┐                   │
 │  │ <title>     │  Browser tab text │
 │  └─────────────┘                   │
 │  ┌─────────────┐                   │
 │  │ <link>      │  CSS, favicon,    │
 │  │             │  preload, fonts   │
 │  └─────────────┘                   │
 │  ┌─────────────┐                   │
 │  │ <script>    │  JavaScript       │
 │  └─────────────┘                   │
 └───────────────────────────────────┘
```

### Meta Tags

```html
<!-- Character Encoding — Must be first -->
<meta charset="UTF-8">

<!-- Viewport — Essential for responsive design -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- SEO -->
<meta name="description" content="A brief description of the page">
<meta name="keywords" content="html, css, javascript, tutorial">
<meta name="author" content="Your Name">
<meta name="robots" content="index, follow">

<!-- Social Sharing (Open Graph) -->
<meta property="og:title" content="Page Title">
<meta property="og:description" content="Description for sharing">
<meta property="og:image" content="https://example.com/image.jpg">
<meta property="og:url" content="https://example.com/page">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Page Title">

<!-- Theme Color (Mobile browsers) -->
<meta name="theme-color" content="#317EFB">
```

### Title

```html
<title>Home Page - My Website</title>
```

- Appears in the browser tab.
- Used as the headline in search results (critical for SEO).
- Should be unique across pages.

### Link Element

```html
<!-- Stylesheet -->
<link rel="stylesheet" href="styles.css">

<!-- Favicon -->
<link rel="icon" href="favicon.ico" type="image/x-icon">
<link rel="apple-touch-icon" href="apple-touch-icon.png">

<!-- Preload critical resources -->
<link rel="preload" href="font.woff2" as="font" type="font/woff2" crossorigin>

<!-- DNS Prefetch -->
<link rel="dns-prefetch" href="//api.example.com">

<!-- Canonical URL (prevents duplicate content issues) -->
<link rel="canonical" href="https://example.com/page">
```

### Script Placement

```html
<!-- ❌ Bad: blocks rendering -->
<head>
    <script src="large.js"></script>
</head>

<!-- ✅ Good: at end of body, non-blocking -->
<body>
    <!-- content -->
    <script src="app.js"></script>
</body>

<!-- ✅ Better: async or defer -->
<head>
    <script src="analytics.js" async></script>
    <script src="app.js" defer></script>
</head>
```

| Attribute | Behavior |
|-----------|----------|
| (none) | HTML parsing pauses, script fetches & executes |
| `async` | HTML parsing continues, script executes as soon as downloaded |
| `defer` | HTML parsing continues, script executes after parsing completes |

## The `<body>` Element

Contains all **visible content**. Only one `<body>` per document.

```html
<body>
    <header>    <!-- Logo, navigation, branding -->
        <nav>
            <a href="/">Home</a>
            <a href="/about">About</a>
        </nav>
    </header>

    <main>      <!-- Primary page content -->
        <article>
            <h1>Article Title</h1>
            <p>Article content...</p>
        </article>
    </main>

    <aside>     <!-- Sidebar, related content -->
        <h2>Related Posts</h2>
        <ul>
            <li><a href="#">Post 1</a></li>
            <li><a href="#">Post 2</a></li>
        </ul>
    </aside>

    <footer>    <!-- Copyright, links, credits -->
        <p>&copy; 2026 My Website</p>
    </footer>
</body>
```

## Complete Document Example

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="Complete HTML document structure example">
    <title>Document Structure | HTML Guide</title>
    <link rel="stylesheet" href="style.css">
    <link rel="icon" href="favicon.ico">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <script src="analytics.js" defer></script>

    <!-- Open Graph -->
    <meta property="og:title" content="HTML Document Structure Guide">
    <meta property="og:type" content="article">
    <meta property="og:url" content="https://example.com/html-document">
    <meta property="og:image" content="https://example.com/og-image.jpg">
</head>
<body>
    <header>
        <a href="/" class="logo">
            <img src="logo.svg" alt="Site logo" width="150" height="50">
        </a>
        <nav aria-label="Main navigation">
            <ul>
                <li><a href="/">Home</a></li>
                <li><a href="/tutorials">Tutorials</a></li>
                <li><a href="/about">About</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <h1>HTML Document Structure</h1>
        <p class="intro">Understanding how to structure an HTML document is the first step...</p>

        <section aria-labelledby="head-section">
            <h2 id="head-section">The Head Section</h2>
            <p>Contains metadata, links, and scripts.</p>
        </section>

        <section aria-labelledby="body-section">
            <h2 id="body-section">The Body Section</h2>
            <p>Contains all visible page content.</p>
        </section>
    </main>

    <aside>
        <h2>Related Articles</h2>
        <ul>
            <li><a href="/semantic-html">Semantic HTML</a></li>
            <li><a href="/forms">Forms Guide</a></li>
        </ul>
    </aside>

    <footer>
        <p>&copy; 2026 HTML Guide. All rights reserved.</p>
    </footer>

    <script src="main.js"></script>
</body>
</html>
```

## Key Takeaways

1. The `<head>` contains metadata — never visible content.
2. Always include `<meta charset="UTF-8">` as the first meta tag.
3. Use `defer` or `async` for scripts to avoid blocking rendering.
4. The `<body>` should follow a logical structure: header → main → aside → footer.
5. Each page needs a unique, descriptive `<title>`.

---

**Next:** [03-Semantic-HTML.md](03-Semantic-HTML.md) — Semantic HTML elements and best practices.
