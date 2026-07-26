# HTML Interview Questions

## Basic Questions

### Q1: What is HTML and what does it stand for?

**HTML** stands for **HyperText Markup Language**. It is the standard markup language for creating web pages and web applications. It describes the structure of a web page using a system of tags and attributes, and it is the backbone of every website.

---

### Q2: What is the difference between HTML and HTML5?

| Feature | HTML (4.01) | HTML5 |
|---------|-------------|-------|
| Doctype | `<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">` | `<!DOCTYPE html>` |
| Multimedia | Required plugins (Flash) | Native `<audio>`, `<video>` |
| Graphics | Third-party libraries | `<canvas>`, `<svg>` |
| Semantic tags | `<div>` for everything | `<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, `<footer>` |
| Storage | Cookies | `localStorage`, `sessionStorage` |
| APIs | Limited | Geolocation, WebSockets, Web Workers, Drag & Drop |
| Mobile | Not designed for mobile | Viewport meta tag, responsive design |

---

### Q3: What is the purpose of the `<!DOCTYPE html>` declaration?

The `<!DOCTYPE html>` declaration tells the browser to render the page in **standards mode** rather than **quirks mode**. Without it, browsers may display the page using outdated rendering rules, causing layout inconsistencies.

---

### Q4: What are semantic HTML elements? Give examples.

Semantic elements clearly describe their meaning to both the browser and the developer. Examples:

- `<header>` — introductory content
- `<nav>` — navigation links
- `<main>` — primary content
- `<article>` — self-contained content
- `<section>` — thematic grouping
- `<aside>` — related content (sidebar)
- `<footer>` — closing content
- `<figure>` / `<figcaption>` — media with caption

---

### Q5: What is the difference between `<div>` and `<span>`?

| `<div>` | `<span>` |
|---------|----------|
| Block-level element | Inline element |
| Takes full width by default | Only as wide as content |
| Used for layout containers | Used for inline text styling |
| Can contain block or inline elements | Should only contain inline elements |

---

### Q6: How do you create a hyperlink? What is the target attribute?

```html
<!-- Opens in same tab (default) -->
<a href="https://example.com">Visit Example</a>

<!-- Opens in new tab -->
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
    Visit Example (new tab)
</a>
```

**`target` values:**
- `_self` — same frame (default)
- `_blank` — new tab/window
- `_parent` — parent frame
- `_top` — full window (breaks frames)

> **Security:** Always add `rel="noopener noreferrer"` with `target="_blank"` to prevent tab-napping attacks.

---

### Q7: What is the difference between `id` and `class`?

| `id` | `class` |
|------|---------|
| Unique — one element per ID | Can be used on many elements |
| Used for unique elements, anchor links | Used for styling groups of elements |
| JavaScript: `getElementById()` | JavaScript: `getElementsByClassName()` |
| Higher CSS specificity | Lower CSS specificity |

```html
<div id="header">Unique header</div>
<div class="card">Card 1</div>
<div class="card">Card 2</div>
```

---

### Q8: How do you add an image and make it accessible?

```html
<!-- Good accessibility -->
<img src="diagram.png" alt="Flowchart showing the login process" width="600" height="400">

<!-- Decorative image — empty alt -->
<img src="decoration.png" alt="" role="presentation">
```

---

### Q9: What are the different types of lists in HTML?

- **Ordered list** (`<ol>`) — numbered items
- **Unordered list** (`<ul>`) — bulleted items
- **Description list** (`<dl>`) — term-description pairs

```html
<ol>
    <li>First step</li>
    <li>Second step</li>
</ol>

<ul>
    <li>Item A</li>
    <li>Item B</li>
</ul>

<dl>
    <dt>HTML</dt>
    <dd>HyperText Markup Language</dd>
</dl>
```

---

### Q10: What is the difference between `post` and `get` in forms?

| GET | POST |
|-----|------|
| Data in URL | Data in body |
| Limited size (~2000 chars) | Large size |
| Not secure (visible) | More secure |
| Can be bookmarked | Cannot be bookmarked |
| Use for searches | Use for mutations, logins |

---

## Intermediate Questions

### Q11: What is the `<picture>` element and when would you use it?

The `<picture>` element provides **art direction** and **resolution switching** for responsive images.

```html
<picture>
    <source media="(min-width: 1200px)" srcset="hero-wide.webp" type="image/webp">
    <source media="(min-width: 800px)" srcset="hero-tablet.webp" type="image/webp">
    <source media="(min-width: 400px)" srcset="hero-mobile.webp" type="image/webp">
    <img src="hero-fallback.jpg" alt="Hero banner" loading="lazy">
</picture>
```

**Use cases:**
- Different image crops for different screen sizes
- Different image formats (WebP, AVIF) with fallbacks
- High-resolution (Retina) displays

---

### Q12: Explain the `defer` and `async` attributes on `<script>`.

| (none) | `async` | `defer` |
|--------|---------|---------|
| HTML parsing pauses | HTML parsing continues | HTML parsing continues |
| Script fetched & executed immediately | Script executes when downloaded | Script executes after parsing |
| Blocks rendering | Order not guaranteed | Order preserved |

```html
<script src="critical.js"></script>       <!-- Blocks -->
<script src="analytics.js" async></script> <!-- Non-blocking, independent -->
<script src="app.js" defer></script>       <!-- Non-blocking, runs after HTML -->
```

---

### Q13: What is the `<template>` element?

The `<template>` element holds HTML that is not rendered immediately. It can be cloned and inserted with JavaScript.

```html
<template id="card-template">
    <div class="card">
        <img src="" alt="">
        <h3></h3>
        <p></p>
    </div>
</template>

<script>
    const template = document.getElementById('card-template');
    const clone = template.content.cloneNode(true);
    clone.querySelector('h3').textContent = 'Card Title';
    document.body.appendChild(clone);
</script>
```

---

### Q14: What are Data Attributes (`data-*`)?

Custom attributes for storing extra information on HTML elements.

```html
<button data-user-id="123" data-role="admin" onclick="showUser(this)">
    John Doe
</button>

<script>
    function showUser(button) {
        const userId = button.dataset.userId;  // "123"
        const role = button.dataset.role;      // "admin"
    }
</script>
```

---

### Q15: Explain the `contenteditable` attribute.

`contenteditable` makes an element editable by the user.

```html
<div contenteditable="true">
    This text can be edited by the user.
</div>

<h2 contenteditable="plaintext-only">
    Edit this heading (plain text only)
</h2>
```

**Values:** `true`, `false`, `plaintext-only`

---

### Q16: What is the difference between `<strong>` and `<b>`? `<em>` and `<i>`?

| Tag | Meaning | Use |
|-----|---------|-----|
| `<strong>` | Strong **importance** | Important content, warnings |
| `<b>` | Stylistic offset | Keywords, product names (no extra importance) |
| `<em>` | Emphasized text | Stress emphasis (changes sentence meaning) |
| `<i>` | Alternative voice | Technical terms, foreign words, thoughts |

`<strong>` and `<em>` have **semantic meaning** and affect screen reader intonation. `<b>` and `<i>` are purely presentational.

---

### Q17: How do you create a table with merged cells?

```html
<table>
    <caption>Team Schedule</caption>
    <thead>
        <tr>
            <th rowspan="2">Team</th>
            <th colspan="3">Matches</th>
        </tr>
        <tr>
            <th>Mon</th>
            <th>Tue</th>
            <th>Wed</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Alpha</td>
            <td>vs Beta</td>
            <td>vs Gamma</td>
            <td>-</td>
        </tr>
    </tbody>
</table>
```

---

## Advanced Questions

### Q18: What are Web Components?

Web Components are a set of browser APIs for creating reusable custom elements.

```html
<script>
    class MyCounter extends HTMLElement {
        constructor() {
            super();
            this.count = 0;
            this.attachShadow({ mode: 'open' });
        }

        connectedCallback() {
            this.shadowRoot.innerHTML = `
                <button id="dec">-</button>
                <span id="count">${this.count}</span>
                <button id="inc">+</button>
            `;
            this.shadowRoot.getElementById('inc')
                .addEventListener('click', () => this.count++);
        }
    }
    customElements.define('my-counter', MyCounter);
</script>

<my-counter></my-counter>
```

**Three technologies:**
1. **Custom Elements** — define new HTML elements
2. **Shadow DOM** — encapsulated DOM and styles
3. **HTML Templates** — `<template>` element

---

### Q19: How does the browser render HTML? (Critical Rendering Path)

```
HTML ──► DOM (Document Object Model)
          │
CSS  ──► CSSOM (CSS Object Model)
          │
          ▼
    Render Tree (visible nodes only)
          │
          ▼
    Layout (calculate position/size)
          │
          ▼
    Paint (pixels to screen)

Optimization tips:
- Minify HTML, CSS, JS
- Defer non-critical scripts
- Inline critical CSS
- Compress images
- Use `loading="lazy"` for below-fold images
```

---

### Q20: How do you make a website accessible?

1. **Semantic HTML** — use `<nav>`, `<main>`, etc.
2. **Alt text** on all images
3. **Labels** on all form fields
4. **Heading hierarchy** — one `h1`, properly nested
5. **Keyboard navigation** — all interactive elements reachable
6. **Sufficient contrast** — 4.5:1 for text
7. **ARIA** — only when HTML is insufficient
8. **Captions** for video, transcripts for audio
9. **Skip navigation link**
10. **Test** with screen readers and keyboard-only

---

### Q21: What is the difference between `localStorage`, `sessionStorage`, and cookies?

| Feature | `localStorage` | `sessionStorage` | Cookies |
|---------|---------------|-----------------|---------|
| Capacity | 5-10 MB | 5-10 MB | 4 KB |
| Expires | Never | On tab close | Set by server |
| Sent to server | No | No | Yes (every request) |
| Access | Client only | Client only | Client + Server |
| Use case | App state, cache | Form data | Auth tokens, tracking |

```javascript
// localStorage (persists)
localStorage.setItem('theme', 'dark');
const theme = localStorage.getItem('theme');

// sessionStorage (per tab)
sessionStorage.setItem('formData', JSON.stringify(data));

// Cookies
document.cookie = "sessionId=abc123; path=/; max-age=3600";
```

---

### Q22: How do you optimize HTML for SEO?

1. **Unique title tags** (under 60 chars)
2. **Meta descriptions** (120-158 chars)
3. **Semantic HTML** structure
4. **Heading hierarchy** (h1 → h2 → h3)
5. **Alt text** on images
6. **Open Graph tags** for social sharing
7. **Canonical URLs** to prevent duplicate content
8. **Structured data** (JSON-LD)
9. **Fast page speed** (Core Web Vitals)
10. **Mobile-friendly** design
11. **XML sitemap**
12. **Internal linking** with descriptive anchor text

---

### Q23: What is the Shadow DOM and how does it differ from the regular DOM?

**Shadow DOM** is a web standard that encapsulates DOM trees and styles within a component, preventing style leakage.

```javascript
const host = document.getElementById('host');
const shadow = host.attachShadow({ mode: 'open' });
shadow.innerHTML = `
    <style>p { color: red; }</style>
    <p>This text is red, but doesn't affect outside.</p>
`;
```

| Regular DOM | Shadow DOM |
|-------------|------------|
| Global styles affect everything | Styles are scoped |
| IDs must be unique globally | IDs are scoped to shadow root |
| No encapsulation | Full encapsulation |
| Used for whole page | Used for components |

---

### Q24: Explain the concept of "progressive enhancement" in HTML.

**Progressive enhancement** starts with a solid, accessible HTML foundation (works everywhere) and layers CSS for presentation and JavaScript for interactivity.

```
Layer 3: JavaScript (enhancements)
    └── Layer 2: CSS (styling, layout)
         └── Layer 1: HTML (content, structure)
              └── Works everywhere, even with JS/CSS disabled
```

**Example:**
```html
<!-- Base: works without CSS or JS -->
<a href="/products" class="button">
    View Products
</a>

<!-- CSS adds styling -->
<style>
    .button { /* styles */ }
</style>

<!-- JS adds enhancement -->
<script>
    // Lazy-load more products via AJAX
    document.querySelector('.button').addEventListener('click', e => {
        e.preventDefault();
        // AJAX load
    });
</script>
```

---

### Q25: What is the `rel="noopener noreferrer"` and why should you use it?

When using `target="_blank"`, the opened page can access the original page via `window.opener`, enabling **tab-napping** attacks.

```html
<!-- Vulnerable -->
<a href="https://malicious.com" target="_blank">Click me</a>

<!-- Secure -->
<a href="https://example.com" target="_blank" rel="noopener noreferrer">
    Click me
</a>
```

- `noopener` — prevents `window.opener` access
- `noreferrer` — prevents the Referer header from being sent
- Modern browsers treat `_blank` with `rel="noopener"` as default, but explicitly adding it is still best practice.

---

### Q26: What are validations in HTML5 forms?

HTML5 provides built-in form validation:

```html
<input type="email" required>
<input type="text" pattern="[A-Za-z]{3,}" minlength="3" maxlength="20">
<input type="number" min="1" max="100">
```

```
┌──────────────────────────────┐
│  Browser Validation Flow     │
│                              │
│  User submits form           │
│       │                      │
│  Browser checks constraints  │
│       │                      │
│  Valid? ──Yes──► Submit      │
│       │                      │
│       No                     │
│       │                      │
│  Show error messages         │
│  (CSS :invalid, Validity API)│
└──────────────────────────────┘
```

JavaScript validation:
```javascript
const email = document.getElementById('email');
if (email.validity.typeMismatch) {
    email.setCustomValidity('Please enter a valid email');
}
```

---

### Q27: How do you make a custom element with encapsulated styles?

Using **Web Components** with **Shadow DOM**:

```html
<custom-card title="Hello" image="pic.jpg">
    This is card content
</custom-card>

<script>
    class CustomCard extends HTMLElement {
        constructor() {
            super();
            this.attachShadow({ mode: 'open' });
        }

        connectedCallback() {
            this.shadowRoot.innerHTML = `
                <style>
                    :host { display: block; border: 1px solid #ddd;
                            border-radius: 8px; padding: 16px; }
                    h2 { margin: 0 0 8px; }
                    ::slotted(p) { color: #666; }
                </style>
                <h2>${this.getAttribute('title')}</h2>
                <img src="${this.getAttribute('image')}" alt="">
                <slot></slot>
            `;
        }
    }
    customElements.define('custom-card', CustomCard);
</script>
```

---

### Q28: What is the difference between `src` and `href`?

| Attribute | Used On | Purpose |
|-----------|---------|---------|
| `src` | `<img>`, `<script>`, `<video>`, `<audio>`, `<iframe>`, `<source>` | **Embed** the resource into the document |
| `href` | `<a>`, `<link>` | **Link to** a resource (optional to load) |

The browser **loads and executes** `src` content immediately. `href` is a reference — the browser can choose whether to load it.

---

### Q29: How would you handle browser history with the HTML5 History API?

```html
<button onclick="goToPage('about')">About</button>
<button onclick="goBack()">Back</button>

<script>
    // Push a new state
    function goToPage(page) {
        history.pushState({ page }, '', `/${page}`);
        // Update content via AJAX
    }

    // Handle back/forward buttons
    window.addEventListener('popstate', (event) => {
        if (event.state) {
            // Load content for event.state.page
        }
    });

    // Replace current state (no new history entry)
    history.replaceState({ page: 'home' }, '', '/home');
</script>
```

---

### Q30: Explain the purpose of `meta name="viewport"`.

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

**Purpose:** Controls how the page is displayed on mobile devices.

**Without it:** Mobile browsers render the page at a typical desktop width (980px) then zoom out, making text tiny.

**With it:** The page width matches the device width, enabling proper responsive design.

**Components:**
- `width=device-width` — sets width to device screen width
- `initial-scale=1.0` — sets initial zoom level
- `minimum-scale`, `maximum-scale` — zoom limits (avoid restricting — accessibility issue)
- `user-scalable=yes/no` — allow zoom (avoid `no` — accessibility issue)
