# Meta Tags

## What are Meta Tags?

Meta tags are HTML elements that provide **metadata** about a web page — information that is not displayed on the page itself but is used by browsers, search engines, and social platforms.

## Social Sharing Preview

```
 ┌────────────────────────────────────────────────────────────┐
 │  Facebook / LinkedIn / Twitter (when shared)               │
 │                                                            │
 │  ┌──────────────────────────────────────────────────────┐  │
 │  │                                                      │  │
 │  │  ┌──────────────┐  og:title                         │  │
 │  │  │              │  ├──────────────────────────────── │  │
 │  │  │              │  og:description                  │  │
 │  │  │   og:image   │  ├─ This is the description      │  │
 │  │  │              │  │  that appears below the       │  │
 │  │  │              │  │  title in the social share     │  │
 │  │  │              │  └──────────────────────────────── │  │
 │  │  └──────────────┘  example.com                       │  │
 │  └──────────────────────────────────────────────────────┘  │
 └────────────────────────────────────────────────────────────┘
```

## Essential Meta Tags

### Character Encoding

```html
<meta charset="UTF-8">
```

- **Must be the first meta tag** in `<head>`
- Ensures proper rendering of all Unicode characters
- Without it, special characters (é, ñ, ü, emoji) may appear broken

### Viewport (Responsive Design)

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

| Component | Purpose |
|-----------|---------|
| `width=device-width` | Sets page width to device screen width |
| `initial-scale=1.0` | Sets initial zoom level to 100% |
| `minimum-scale` | Minimum allowed zoom (avoid setting — breaks accessibility) |
| `maximum-scale` | Maximum allowed zoom (avoid — breaks accessibility) |
| `user-scalable` | `yes`/`no` (avoid `no` — breaks accessibility) |

### SEO Meta Tags

```html
<!-- Page title (used in search results) -->
<title>Product Name - Brand Name | Best Widgets Online</title>

<!-- Description (shown in search snippets) -->
<meta name="description"
      content="Discover premium widgets at unbeatable prices.
               Fast shipping, 30-day return policy. Shop now!">

<!-- Keywords (⚠️ No longer used by Google, but harmless) -->
<meta name="keywords" content="widgets, premium, online shopping">

<!-- Author -->
<meta name="author" content="Company Name">

<!-- Robots control -->
<meta name="robots" content="index, follow">

<!-- Google-specific -->
<meta name="googlebot" content="index, follow">
<meta name="google" content="nositelinkssearchbox">
```

#### Robots Directives

| Value | Meaning |
|-------|---------|
| `index` | Allow indexing (default) |
| `noindex` | Do not show in search results |
| `follow` | Follow links on this page (default) |
| `nofollow` | Do not follow links on this page |
| `noarchive` | Do not show cached version |
| `nosnippet` | Do not show description snippet |
| `notranslate` | Do not offer translation |
| `max-snippet:-1` | No snippet length limit |

```html
<!-- Prevent indexing on staging/dev -->
<meta name="robots" content="noindex, nofollow">

<!-- Allow indexing, don't show cached version -->
<meta name="robots" content="index, follow, noarchive">
```

## Open Graph Tags (Facebook, LinkedIn)

```html
<!-- Required -->
<meta property="og:title" content="How to Master HTML Meta Tags">
<meta property="og:type" content="article">
<meta property="og:url" content="https://example.com/html-meta-tags">
<meta property="og:image" content="https://example.com/images/og-image.jpg">

<!-- Recommended -->
<meta property="og:description"
      content="A complete guide to HTML meta tags for SEO and social sharing.">
<meta property="og:site_name" content="WebDev Tutorials">
<meta property="og:locale" content="en_US">

<!-- Image dimensions (helps platforms render faster) -->
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">

<!-- Video content -->
<meta property="og:video" content="https://example.com/video.mp4">
<meta property="og:video:type" content="video/mp4">
```

### OG Image Best Practices

- Recommended size: **1200 × 630 pixels**
- Aspect ratio: **1.91:1**
- Max file size: **5MB**
- Use text overlay on images (visible without loading the page)
- Avoid using your logo alone — show context

## Twitter Cards

```html
<!-- Summary Card (default) -->
<meta name="twitter:card" content="summary">
<meta name="twitter:site" content="@yourhandle">
<meta name="twitter:title" content="Page Title">
<meta name="twitter:description" content="Page description">
<meta name="twitter:image" content="https://example.com/image.jpg">

<!-- Summary Card with Large Image (recommended) -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:image" content="https://example.com/image.jpg">

<!-- Player Card (for video/audio) -->
<meta name="twitter:card" content="player">
<meta name="twitter:player" content="https://example.com/player">
<meta name="twitter:player:width" content="480">
<meta name="twitter:player:height" content="480">

<!-- App Card (for mobile apps) -->
<meta name="twitter:card" content="app">
<meta name="twitter:app:id:iphone" content="123456789">
```

## Theme Color & UI

```html
<!-- Browser theme color (Android Chrome, Edge) -->
<meta name="theme-color" content="#317EFB">

<!-- Microsoft Tile (Windows 8/10) -->
<meta name="msapplication-TileColor" content="#317EFB">
<meta name="msapplication-TileImage" content="mstile-144x144.png">

<!-- Apple Safari -->
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="My App">
```

## Favicon Meta

```html
<!-- Classic favicon -->
<link rel="icon" href="/favicon.ico" type="image/x-icon">
<link rel="icon" href="/favicon.svg" type="image/svg+xml">

<!-- Dark mode favicon (browser support varies) -->
<link rel="icon" href="/favicon-dark.svg" media="(prefers-color-scheme: dark)">

<!-- iOS icons -->
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
<link rel="apple-touch-icon" sizes="152x152" href="/apple-touch-icon-152.png">

<!-- Android -->
<link rel="manifest" href="/site.webmanifest">

<!-- Windows -->
<meta name="msapplication-TileImage" content="/mstile-144x144.png">
```

## HTTP-Equivalent Meta Tags

```html
<!-- Refresh/redirect (avoid — breaks browser back button) -->
<meta http-equiv="refresh" content="5; url=https://example.com/new-page">

<!-- Content type -->
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">

<!-- X-UA-Compatible (IE compatibility — legacy) -->
<meta http-equiv="X-UA-Compatible" content="IE=edge">

<!-- Content Security Policy -->
<meta http-equiv="Content-Security-Policy"
      content="default-src 'self'; script-src 'self' https://analytics.example.com">

<!-- Cache control -->
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate">
<meta http-equiv="Pragma" content="no-cache">
<meta http-equiv="Expires" content="0">
```

## Complete Meta Tags Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <!-- Required -->
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Complete Guide to HTML Meta Tags</title>

    <!-- SEO -->
    <meta name="description"
          content="Learn everything about HTML meta tags: charset, viewport, Open Graph,
                   Twitter Cards, SEO, and more. Complete guide with examples.">
    <meta name="robots" content="index, follow">
    <link rel="canonical" href="https://example.com/meta-tags-guide">

    <!-- Social: Open Graph -->
    <meta property="og:type" content="article">
    <meta property="og:title" content="Complete Guide to HTML Meta Tags">
    <meta property="og:description"
          content="Learn everything about HTML meta tags with examples.">
    <meta property="og:url" content="https://example.com/meta-tags-guide">
    <meta property="og:image" content="https://example.com/og-meta-tags.jpg">
    <meta property="og:image:width" content="1200">
    <meta property="og:image:height" content="630">
    <meta property="og:site_name" content="WebDev Guide">

    <!-- Social: Twitter -->
    <meta name="twitter:card" content="summary_large_image">
    <meta name="twitter:site" content="@webdevguide">
    <meta name="twitter:title" content="Complete Guide to HTML Meta Tags">
    <meta name="twitter:description"
          content="Learn everything about HTML meta tags.">
    <meta name="twitter:image" content="https://example.com/og-meta-tags.jpg">

    <!-- Icons -->
    <link rel="icon" href="/favicon.ico" sizes="any">
    <link rel="icon" href="/favicon.svg" type="image/svg+xml">
    <link rel="apple-touch-icon" href="/apple-touch-icon.png">
    <link rel="manifest" href="/site.webmanifest">

    <!-- Theme -->
    <meta name="theme-color" content="#317EFB">
    <meta name="msapplication-TileColor" content="#317EFB">
</head>
<body>
    <!-- Page content -->
</body>
</html>
```

## Common Mistakes

| Mistake | Problem | Fix |
|---------|---------|-----|
| Missing `charset` | Special characters broken | Add `<meta charset="UTF-8">` first |
| Missing `viewport` | Non-responsive on mobile | Add viewport meta tag |
| Wrong OG image size | Cropped or low-res share | Use 1200×630 px |
| Missing `canonical` | Duplicate content penalty | Add `<link rel="canonical">` |
| Too many keywords | Ignored or penalized | Use 1-2 focused keywords max |
| Blocking CSS/JS in robots.txt | Page renders poorly for search | Allow CSS/JS for rendering |

## Testing Tools

| Tool | URL | Purpose |
|------|-----|---------|
| Facebook Sharing Debugger | `developers.facebook.com/tools/debug` | Preview OG tags |
| Twitter Card Validator | `cards-dev.twitter.com/validator` | Preview Twitter cards |
| Google Rich Results Test | `search.google.com/test/rich-results` | Test structured data |
| LinkedIn Post Inspector | `linkedin.com/post-inspector` | Preview LinkedIn shares |
| Lighthouse | Chrome DevTools | Audit meta tags |

## Key Takeaways

1. **Always** include `charset="UTF-8"` and viewport meta tags.
2. **OG tags** control how your page appears on Facebook and LinkedIn.
3. **Twitter Cards** control appearance on Twitter/X.
4. Use **canonical URLs** to prevent duplicate content issues.
5. Add **favicon** in multiple formats for different devices.
6. Keep descriptions under **160 characters** for search snippets.

---

**Next:** [10-SEO-Basics.md](10-SEO-Basics.md) — Search engine optimization basics.
