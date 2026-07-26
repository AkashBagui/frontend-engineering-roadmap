# HTML Best Practices

## Coding Standards

### Always Use DOCTYPE

```html
<!-- ✅ Correct -->
<!DOCTYPE html>

<!-- ❌ Wrong — triggers quirks mode -->
<html>
```

### Declare Document Language

```html
<!-- ✅ Correct -->
<html lang="en">
<html lang="es">

<!-- ❌ Wrong — screen readers use wrong pronunciation -->
<html>
```

### Proper Character Encoding

```html
<!-- ✅ Must be first meta tag -->
<meta charset="UTF-8">

<!-- ❌ Missing → special characters may render incorrectly -->
```

### Indentation and Formatting

```html
<!-- ✅ Consistent 2-space indentation -->
<main>
    <article>
        <header>
            <h1>Title</h1>
        </header>
        <p>Content</p>
    </article>
</main>

<!-- ❌ Inconsistent / no indentation -->
<main><article>
<header><h1>Title</h1></header>
<p>Content</p>
</article></main>
```

| Style | Recommendation |
|-------|----------------|
| Indent size | **2 spaces** (most common) |
| Tabs vs spaces | **Spaces** (consistent across editors) |
| Max line length | **80-120 characters** |
| Closing tags | Always include them (`<p>` needs `</p>`) |
| Self-closing | `<br>`, `<img>`, `<input>` → no `/` needed in HTML5 |

### Quoting Attributes

```html
<!-- ✅ Always use double quotes -->
<a href="/page" class="nav-link" data-value="123">Link</a>

<!-- < ❌ Inconsistent -->
<a href=/page class='nav-link'>Link</a>
```

### Lowercase Tag Names

```html
<!-- ✅ Correct -->
<section>
    <h2>Title</h2>
</section>

<!-- ❌ Wrong — mixed case -->
<SECTION>
    <H2>Title</H2>
</SECTION>
```

## File Organization

```
project/
├── index.html
├── about.html
├── contact.html
├── css/
│   └── style.css
├── js/
│   └── main.js
├── images/
│   ├── logo.svg
│   └── hero.jpg
└── favicon.ico
```

## Performance Optimization

### Minimize HTTP Requests

```html
<!-- ✅ Combine CSS -->
<link rel="stylesheet" href="styles.min.css">

<!-- ❌ Multiple files -->
<link rel="stylesheet" href="reset.css">
<link rel="stylesheet" href="layout.css">
<link rel="stylesheet" href="components.css">
```

### Optimize Script Loading

```html
<!-- ✅ Defer non-critical scripts -->
<script src="app.js" defer></script>

<!-- ✅ Async for independent scripts -->
<script src="analytics.js" async></script>

<!-- ❌ Blocking scripts in <head> -->
<head>
    <script src="large.js"></script>
</head>
```

### Optimize Images

```html
<!-- ✅ Specify dimensions (prevents layout shift) -->
<img src="photo.jpg" alt="Description" width="800" height="600" loading="lazy">

<!-- ✅ Use modern formats -->
<picture>
    <source srcset="photo.avif" type="image/avif">
    <source srcset="photo.webp" type="image/webp">
    <img src="photo.jpg" alt="Description" loading="lazy">
</picture>
```

### Preconnect to Third-Party Origins

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://analytics.example.com">
```

### Inline Critical CSS

```html
<head>
    <style>
        /* Critical above-the-fold styles */
        body { margin: 0; font-family: sans-serif; }
        .hero { min-height: 100vh; }
    </style>
    <link rel="stylesheet" href="styles.css" media="print" onload="this.media='all'">
</head>
```

## Security Best Practices

### Content Security Policy (CSP)

```html
<meta http-equiv="Content-Security-Policy"
      content="default-src 'self';
               script-src 'self' https://analytics.example.com;
               style-src 'self' 'unsafe-inline';
               img-src 'self' data: https:;">
```

### Prevent Clickjacking

```html
<meta http-equiv="X-Frame-Options" content="DENY">
```

### Use HTTPS

```html
<!-- ✅ Absolute URLs should use HTTPS -->
<script src="https://cdn.example.com/library.js"></script>

<!-- ❌ HTTP -->
<script src="http://cdn.example.com/library.js"></script>
```

### Sanitize User Input

```html
<!-- ❌ Never render raw user input -->
<div id="comments">
    <!-- User could inject <script> tags -->
</div>

<!-- ✅ Use textContent, not innerHTML -->
const comment = document.getElementById('comment-input').value;
document.getElementById('comments').textContent = comment;
```

### Protect Form Data

```html
<form action="https://example.com/submit" method="POST" autocomplete="off">
    <!-- Use POST for sensitive data (not GET) -->
    <!-- Always use HTTPS -->
</form>
```

### Avoid Inline Event Handlers

```html
<!-- ❌ Inline handlers — XSS risk, harder to maintain -->
<button onclick="doSomething()">Click</button>

<!-- ✅ JavaScript event listeners -->
<button id="my-button">Click</button>
<script>
    document.getElementById('my-button')
        .addEventListener('click', doSomething);
</script>
```

## Accessibility Checklist

| Practice | Implementation |
|----------|----------------|
| Language attribute | `<html lang="en">` |
| Unique page title | `<title>Descriptive Title</title>` |
| Heading hierarchy | `h1` → `h2` → `h3` (no skipping) |
| Alt text on images | `alt="Meaningful description"` |
| Form labels | `<label for="input-id">Label</label>` |
| Semantic HTML | `<nav>`, `<main>`, `<article>`, etc. |
| Skip navigation | `<a href="#main" class="skip-link">Skip to content</a>` |
| Focus indicators | `:focus-visible { outline: 3px solid blue; }` |
| Sufficient contrast | 4.5:1 for normal text, 3:1 for large text |

## Validation

Always validate your HTML:

```bash
# Using W3C validator
curl -H "Content-Type: text/html" \
     --data-binary @index.html \
     https://validator.w3.org/nu/?out=json
```

Or use the browser extension / `https://validator.w3.org/nu/`.

```html
<!-- ✅ Valid -->
<main>
    <article>
        <h2>Article Title</h2>
        <p>Content</p>
    </article>
</main>

<!-- ❌ Invalid — multiple <main> elements -->
<main>Content 1</main>
<main>Content 2</main>
```

## Semantic HTML Rules

```html
<!-- ✅ Use one <main> per page -->
<main>
    <article>
        <h1>Page Title</h1>
    </article>
</main>

<!-- ✅ <nav> for navigation -->
<nav aria-label="Main">
    <ul>
        <li><a href="/">Home</a></li>
    </ul>
</nav>

<!-- ✅ Use <figure> for self-contained media -->
<figure>
    <img src="diagram.svg" alt="Diagram">
    <figcaption>Figure 1: System architecture</figcaption>
</figure>
```

## Common Mistakes to Avoid

| Mistake | Why It's Wrong | Fix |
|---------|---------------|-----|
| Using `<br>` for spacing | Structural misuse | Use CSS `margin` |
| Using `<div>` for buttons | Not keyboard accessible | Use `<button>` |
| Skipping heading levels | Breaks document outline | h1 → h2 → h3 |
| Missing `alt` on images | Screen readers read file name | Add meaningful alt |
| Block-level in inline | Invalid HTML | Use `<div>` instead of `<span>` |
| Using `&` without encoding | HTML entity error | Use `&amp;` |
| Nesting `<a>` inside `<a>` | Invalid HTML | Restructure markup |
| Form without labels | Inaccessible forms | Add `<label>` for each input |
| Table for layout | Not responsive/accessible | Use CSS Grid or Flexbox |
| Inline styles | Hard to maintain | Use CSS classes |

## HTML5 Checklist

```
☐ <!DOCTYPE html>
☐ <meta charset="UTF-8">
☐ <meta name="viewport" content="width=device-width, initial-scale=1.0">
☐ <html lang="en">
☐ Descriptive <title>
☐ Semantic elements (<header>, <nav>, <main>, <footer>)
☐ Heading hierarchy (one h1, proper nesting)
☐ Alt text on all meaningful images
☐ Labels on all form inputs
☐ Valid HTML (W3C validator)
☐ Responsive design
☐ Accessible keyboard navigation
☐ Skip navigation link
☐ Visible focus indicators
☐ Proper doctype
☐ No inline event handlers
☐ Optimized images (sizes, modern formats)
☐ Deferred scripts
☐ HTTPS everywhere
```

## Key Takeaways

1. **Always** use `<!DOCTYPE html>`, `<html lang="...">`, `<meta charset="UTF-8">`.
2. **Validate** your HTML — catch errors early.
3. **Use semantic HTML** — it's the foundation of accessibility and SEO.
4. **Optimize performance** — defer scripts, specify image sizes, use modern formats.
5. **Follow security best practices** — CSP, HTTPS, sanitize input.
6. **Use consistent formatting** — 2-space indentation, lowercase, double quotes.

---

**Next:** [CheatSheet.md](CheatSheet.md) — Quick reference for all HTML tags.
