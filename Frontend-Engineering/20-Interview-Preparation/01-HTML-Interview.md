# HTML Interview Questions

## 1. What is semantic HTML and why is it important?

**Answer:** Semantic HTML uses HTML tags that convey meaning about the content they contain, beyond just presentation. Examples include `<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<aside>`, and `<footer>` instead of generic `<div>` elements.

**Importance:**
- **Accessibility:** Screen readers and assistive technologies use semantic elements to navigate content
- **SEO:** Search engines better understand page structure and rank content appropriately
- **Maintainability:** Code is easier to read and understand for other developers
- **Future-proofing:** Browsers can provide built-in behaviors for semantic elements

## 2. What is the difference between `<div>` and `<span>`?

**Answer:**
- `<div>` is a **block-level** element. It takes up the full width available and starts on a new line. Used for grouping larger sections of content.
- `<span>` is an **inline** element. It only takes up as much width as necessary and doesn't start on a new line. Used for styling smaller portions of text.

```html
<div>This is a block element</div>
<span>This is inline</span>
```

## 3. Explain the accessibility tree and how ARIA attributes work.

**Answer:** The accessibility tree is a subset of the DOM that browsers expose to assistive technologies. It contains accessibility-relevant information for each element.

**ARIA (Accessible Rich Internet Applications)** attributes supplement HTML when native semantics are insufficient:
- `role` - defines element purpose (e.g., `role="button"`, `role="dialog"`)
- `aria-label` - provides an accessible name
- `aria-labelledby` - references another element for labeling
- `aria-describedby` - provides additional description
- `aria-hidden` - hides elements from assistive technology
- `aria-live` - announces dynamic content changes

**First rule of ARIA:** Don't use ARIA if you can use a native HTML element that provides the semantics natively.

## 4. How do meta tags affect SEO?

**Answer:** Meta tags provide metadata about the HTML document to browsers and search engines.

Key SEO meta tags:
```html
<!-- Page title (not a meta tag but critical for SEO) -->
<title>Page Title | Site Name</title>

<!-- Description shown in search results -->
<meta name="description" content="Brief description of the page content">

<!-- Keywords (largely ignored by modern search engines) -->
<meta name="keywords" content="keyword1, keyword2">

<!-- Viewport for responsive design -->
<meta name="viewport" content="width=device-width, initial-scale=1">

<!-- Open Graph for social sharing -->
<meta property="og:title" content="Title for social shares">
<meta property="og:description" content="Description for social shares">
<meta property="og:image" content="https://example.com/image.jpg">

<!-- Twitter Cards -->
<meta name="twitter:card" content="summary_large_image">

<!-- Canonical URL to prevent duplicate content issues -->
<link rel="canonical" href="https://example.com/page">
```

## 5. What is the difference between `localStorage`, `sessionStorage`, and `cookies`?

**Answer:**

| Feature | localStorage | sessionStorage | Cookies |
|---------|-------------|---------------|---------|
| Capacity | ~5-10 MB | ~5-10 MB | ~4 KB |
| Persistence | Until manually cleared | Until tab closes | Until expiration set |
| Sent with HTTP requests | No | No | Yes |
| Access | Client-side only | Client-side only | Client & server |
| Scope | Origin-wide | Per tab/window | Per domain |

**Use cases:**
- `localStorage`: User preferences, cached data, theme settings
- `sessionStorage`: Form data during multi-step processes, temporary state
- `cookies`: Session tokens, authentication, tracking (when server needs access)

## 6. Explain the `<form>` element and its key attributes.

**Answer:** The `<form>` element collects and submits user input.

```html
<form action="/submit" method="POST" enctype="multipart/form-data" novalidate>
  <label for="name">Name:</label>
  <input type="text" id="name" name="name" required>
  
  <label for="email">Email:</label>
  <input type="email" id="email" name="email" required>
  
  <button type="submit">Submit</button>
</form>
```

**Key attributes:**
- `action` - URL where form data is sent
- `method` - HTTP method (`GET` or `POST`)
- `enctype` - Encoding type (`application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`)
- `novalidate` - Disables browser validation
- `autocomplete` - Enables/disables autocomplete (`on`/`off`)
- `target` - Where to display response (`_self`, `_blank`, `_parent`, `_top`)

## 7. How do you make a website accessible?

**Answer:** Key accessibility practices:

1. **Semantic HTML:** Use proper heading hierarchy (`h1`-`h6`), landmark elements
2. **Keyboard navigation:** All interactive elements must be reachable and operable via keyboard
3. **Focus management:** Visible focus indicators, logical tab order (`tabindex`)
4. **Color contrast:** Minimum 4.5:1 for normal text, 3:1 for large text (WCAG AA)
5. **Alternative text:** `alt` attributes on images, transcripts for audio/video
6. **ARIA attributes:** When native HTML isn't sufficient
7. **Responsive design:** Content must be accessible at all viewport sizes
8. **Form labeling:** Every input needs an associated `<label>`
9. **Error identification:** Clear error messages for form validation
10. **Testing:** Use tools like axe, Lighthouse, WAVE, screen readers

## 8. What is the `<canvas>` element and how is it different from SVG?

**Answer:**

| Feature | Canvas | SVG |
|---------|--------|-----|
| Type | Raster-based (pixel) | Vector-based (shapes) |
| Rendering | Immediate mode | Retained mode |
| DOM access | No individual element access | Each shape is a DOM node |
| Resolution | Depends on pixel resolution | Resolution-independent |
| Performance | Better for many elements | Better for fewer, complex shapes |
| Event handling | Manual hit detection | Built-in event handlers |
| Animation | Frame-based with JavaScript | CSS animations or SMIL |

**Use Canvas for:** Games, image processing, data visualization with many points, real-time rendering

**Use SVG for:** Icons, logos, illustrations, interactive diagrams, responsive graphics

```html
<!-- Canvas - pixel-based drawing -->
<canvas id="myCanvas" width="500" height="300"></canvas>
<script>
  const ctx = document.getElementById('myCanvas').getContext('2d');
  ctx.fillStyle = 'red';
  ctx.fillRect(10, 10, 100, 100);
</script>

<!-- SVG - vector-based shapes -->
<svg width="500" height="300">
  <rect x="10" y="10" width="100" height="100" fill="red"/>
</svg>
```

## 9. Explain HTML5 form validation.

**Answer:** HTML5 provides built-in form validation without JavaScript.

**Input types for validation:**
```html
<input type="email">       <!-- Validates email format -->
<input type="url">         <!-- Validates URL format -->
<input type="number">      <!-- Only accepts numbers -->
<input type="tel">         <!-- Telephone (pattern-based) -->
<input type="date">        <!-- Date picker with validation -->
```

**Validation attributes:**
```html
<input type="text" required>                    <!-- Must be filled -->
<input type="number" min="1" max="100">          <!-- Range validation -->
<input type="text" pattern="[A-Za-z]+">           <!-- Regex pattern -->
<input type="number" minlength="3" maxlength="10"> <!-- Length limits -->
```

**CSS pseudo-classes for validation states:**
```css
input:valid { border-color: green; }
input:invalid { border-color: red; }
input:required { border-left: 3px solid orange; }
```

**Constraint Validation API (JavaScript):**
```javascript
const input = document.getElementById('email');
input.checkValidity();       // Returns boolean
input.validationMessage;     // Returns error message
input.setCustomValidity('Custom error');  // Set custom error
```

## 10. What are data attributes and how do you use them?

**Answer:** Data attributes store custom data on HTML elements. They are prefixed with `data-`.

```html
<div data-user-id="12345" data-role="admin" data-active="true">
  John Doe
</div>
```

**JavaScript access:**
```javascript
const div = document.querySelector('[data-user-id="12345"]');

// Using dataset API
div.dataset.userId;       // "12345" (camelCase conversion)
div.dataset.role;         // "admin"
div.dataset.active;       // "true" (always string)

// Setting values
div.dataset.userId = '67890';
div.dataset.newAttribute = 'value'; // Creates data-new-attribute
```

**CSS access:**
```css
[data-role="admin"] {
  background-color: gold;
}

/* Attribute selectors for partial matching */
[data-role*="admin"] { }  /* Contains */
[data-role^="admin"] { }  /* Starts with */
[data-role$="admin"] { }  /* Ends with */
```

## 11. Explain the `<picture>` element and responsive images.

**Answer:** The `<picture>` element provides art direction and resolution switching for responsive images.

```html
<picture>
  <!-- Art direction: different images for different viewports -->
  <source media="(min-width: 1200px)" srcset="large.jpg">
  <source media="(min-width: 768px)" srcset="medium.jpg">
  <source media="(min-width: 480px)" srcset="small.jpg">
  
  <!-- Resolution switching: different pixel densities -->
  <source srcset="image-1x.jpg 1x, image-2x.jpg 2x, image-3x.jpg 3x">
  
  <!-- Fallback for browsers that don't support <picture> -->
  <img src="fallback.jpg" alt="Description of image">
</picture>
```

**With `srcset` and `sizes` (without `<picture>`):**
```html
<img src="default.jpg"
     srcset="small.jpg 480w, medium.jpg 768w, large.jpg 1200w"
     sizes="(max-width: 480px) 100vw, (max-width: 768px) 50vw, 33vw"
     alt="Responsive image">
```

## 12. What is the Shadow DOM?

**Answer:** Shadow DOM is a web standard that provides encapsulation for HTML elements, CSS, and JavaScript. It creates a scoped subtree within a DOM element.

```javascript
// Creating a Shadow DOM
const host = document.getElementById('host');
const shadow = host.attachShadow({ mode: 'open' }); // or 'closed'

shadow.innerHTML = `
  <style>
    p { color: red; }  /* Scoped - won't affect outside */
  </style>
  <p>Shadow content</p>
  <slot></slot>  <!-- Projects light DOM content -->
`;
```

**Key concepts:**
- **Shadow host:** The element that hosts the shadow tree
- **Shadow root:** The root of the shadow tree
- **Shadow boundary:** Where regular DOM ends and shadow DOM begins
- **Mode:** `open` (accessible via `element.shadowRoot`) or `closed` (inaccessible)
- **`<slot>`:** Projects light DOM content into shadow DOM

**Benefits:**
- CSS encapsulation (no style leakage)
- DOM encapsulation (simplifies querying)
- Used extensively in Web Components

## 13. How does the HTML `<head>` element work and what goes in it?

**Answer:** The `<head>` element contains metadata about the document that isn't displayed as content.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <!-- Required -->
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Page Title</title>
  
  <!-- SEO -->
  <meta name="description" content="Page description">
  <meta name="robots" content="index, follow">
  <link rel="canonical" href="https://example.com/page">
  
  <!-- Social -->
  <meta property="og:title" content="...">
  <meta name="twitter:card" content="summary">
  
  <!-- Resources -->
  <link rel="stylesheet" href="styles.css">
  <link rel="preload" href="font.woff2" as="font" crossorigin>
  <link rel="preconnect" href="https://api.example.com">
  <script src="script.js" defer></script>
  
  <!-- Icons -->
  <link rel="icon" href="favicon.ico" type="image/x-icon">
  <link rel="apple-touch-icon" href="icon-180.png">
  
  <!-- Web App -->
  <meta name="theme-color" content="#ffffff">
  <meta name="apple-mobile-web-app-capable" content="yes">
</head>
```

## 14. What is the difference between `defer` and `async` on script tags?

**Answer:**

```html
<!-- Normal - blocks parsing -->
<script src="script.js"></script>

<!-- Async - downloads in parallel, executes as soon as downloaded (blocks parsing) -->
<script src="script.js" async></script>

<!-- Defer - downloads in parallel, executes after parsing (in order) -->
<script src="script.js" defer></script>
```

| | Normal | async | defer |
|---|--------|-------|-------|
| Download | Starts when tag is encountered | In parallel with parsing | In parallel with parsing |
| Execution | Immediately when downloaded | Immediately when downloaded (may block parsing) | After parsing completes |
| Order | Sequential | Not guaranteed | Sequential (document order) |
| DOMContentLoaded | Blocked | Not blocked (may be blocked if executing) | Fires after deferred scripts |
| Best for | Small scripts, inline | Analytics, independent scripts | Scripts that need DOM |

## 15. Explain HTML entities and common special characters.

**Answer:** HTML entities encode characters that have special meaning in HTML or are not available on the keyboard.

```html
<!-- Common entities -->
&lt;  <!-- < (less-than) -->
&gt;  <!-- > (greater-than) -->
&amp; <!-- & (ampersand) -->
&quot; <!-- " (double quote) -->
&apos; <!-- ' (apostrophe / single quote) -->
&nbsp; <!-- Non-breaking space -->
&copy; <!-- © (copyright) -->
&reg;  <!-- ® (registered trademark) -->
&trade; <!-- ™ (trademark) -->
&euro; <!-- € (euro) -->
&pound; <!-- £ (pound) -->
&yen;  <!-- ¥ (yen) -->
&mdash; <!-- — (em dash) -->
&ndash; <!-- – (en dash) -->
&hellip; <!-- … (ellipsis) -->
&#60;  <!-- Decimal: < -->
&#x3C; <!-- Hex: < -->
```

**Usage example:**
```html
<p>HTML tags use &lt;angle brackets&gt;</p>
<p>Copyright &copy; 2025</p>
<p>Price: &pound;10 &amp; &euro;12</p>
```

## 16. What is the `<template>` element?

**Answer:** The `<template>` element holds HTML fragments that are not rendered when the page loads but can be instantiated with JavaScript.

```html
<template id="user-card-template">
  <style>
    .card { border: 1px solid #ccc; padding: 1rem; }
  </style>
  <div class="card">
    <h3 class="name"></h3>
    <p class="email"></p>
    <slot name="actions"></slot>
  </div>
</template>

<div id="container"></div>

<script>
  const template = document.getElementById('user-card-template');
  const clone = template.content.cloneNode(true);
  
  clone.querySelector('.name').textContent = 'John Doe';
  clone.querySelector('.email').textContent = 'john@example.com';
  
  document.getElementById('container').appendChild(clone);
</script>
```

**Key features:**
- Content is **inert** (scripts don't run, images don't load)
- Content is **not rendered** until cloned and appended
- Supports `<slot>` elements for content projection
- Used in Web Components
- `template.content` returns a `DocumentFragment`

## 17. How do you create accessible tables?

**Answer:** Use proper table structure with semantic elements and associations.

```html
<table>
  <!-- Caption provides a title for the table -->
  <caption>Monthly Sales Report 2025</caption>
  
  <!-- Column groups for styling/meaningful groups -->
  <colgroup>
    <col class="product">
    <col class="q1" span="3">
    <col class="total">
  </colgroup>
  
  <!-- Header section -->
  <thead>
    <tr>
      <th scope="col">Product</th>
      <th scope="col">Jan</th>
      <th scope="col">Feb</th>
      <th scope="col">Mar</th>
      <th scope="col">Total</th>
    </tr>
  </thead>
  
  <!-- Body section -->
  <tbody>
    <tr>
      <th scope="row">Widget A</th>
      <td>100</td>
      <td>150</td>
      <td>200</td>
      <td>450</td>
    </tr>
  </tbody>
  
  <!-- Footer section -->
  <tfoot>
    <tr>
      <th scope="row">Grand Total</th>
      <td colspan="4">950</td>
    </tr>
  </tfoot>
</table>
```

**Accessibility best practices:**
- Use `scope="col"` on header cells for columns
- Use `scope="row"` on header cells for rows
- Use `<caption>` or `aria-label` for table description
- Use `<thead>`, `<tbody>`, `<tfoot>` for structure
- Use `colspan` and `rowspan` correctly
- Avoid complex nested tables

## 18. What is the difference between `em`, `rem`, `px`, and `%`?

**Answer:**

| Unit | Relative to | Use case |
|------|-------------|----------|
| `px` | Nothing (absolute) | Borders, shadows, precise sizing |
| `em` | Parent element's font-size | Component-relative sizing |
| `rem` | Root element's font-size (`html`) | Global spacing, typography |
| `%` | Parent element's size | Layout, widths, heights |
| `vw` | 1% of viewport width | Full-width sections, hero images |
| `vh` | 1% of viewport height | Full-height sections, modals |
| `ch` | Width of character "0" | Line length limits |
| `ex` | Height of character "x" | Typographic sizing |

```css
html { font-size: 16px; } /* 1rem = 16px */

.parent { font-size: 20px; }
.child {
  font-size: 2em;   /* 40px (relative to parent's 20px) */
  font-size: 2rem;  /* 32px (relative to root's 16px) */
  font-size: 150%;  /* 30px (relative to parent's 20px) */
  width: 50vw;      /* 50% of viewport width */
}
```

## 19. What are Web Workers and Service Workers?

**Answer:**

**Web Workers** run scripts in background threads, allowing parallel execution without blocking the UI.

```javascript
// main.js
const worker = new Worker('worker.js');
worker.postMessage({ data: 'Hello' });
worker.onmessage = (e) => console.log('Received:', e.data);

// worker.js
self.onmessage = (e) => {
  const result = heavyComputation(e.data);
  self.postMessage(result);
};
```

**Service Workers** act as proxy servers between web applications, the browser, and the network. They enable offline experiences, push notifications, and background sync.

```javascript
// Registration
if ('serviceWorker' in navigator) {
  navigator.serviceWorker.register('/sw.js')
    .then(reg => console.log('SW registered:', reg.scope));
}

// sw.js - Cache-first strategy
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open('v1').then(cache => cache.addAll(['/', '/styles.css', '/app.js']))
  );
});

self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request).then(cached => cached || fetch(event.request))
  );
});
```

| | Web Workers | Service Workers |
|---|------------|-----------------|
| Purpose | Background computation | Network proxy, caching |
| Lifecycle | Created/destroyed per task | Persistent, event-driven |
| DOM access | No | No |
| Use cases | Image processing, data sorting | Offline support, push notifications |
| Terminate | Yes, via `worker.terminate()` | Managed by browser |

## 20. Explain HTML5 semantic elements for page structure.

**Answer:**

```html
<!-- Full page structure with semantic elements -->
<body>
  <header>
    <nav>
      <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
      </ul>
    </nav>
  </header>
  
  <main>
    <article>
      <header>
        <h1>Article Title</h1>
        <time datetime="2025-01-15">January 15, 2025</time>
      </header>
      
      <section>
        <h2>Section Heading</h2>
        <p>Content here...</p>
      </section>
      
      <section>
        <h2>Another Section</h2>
        <figure>
          <img src="image.jpg" alt="Description">
          <figcaption>Image caption</figcaption>
        </figure>
      </section>
    </article>
    
    <aside>
      <h2>Related Content</h2>
      <ul><!-- sidebar links --></ul>
    </aside>
  </main>
  
  <footer>
    <p>&copy; 2025 Company Name</p>
  </footer>
</body>
```

**Element purposes:**
- `<header>` - Introductory content, navigation, logo
- `<nav>` - Navigation links
- `<main>` - Primary content (only one per page)
- `<article>` - Self-contained content (blog post, news story)
- `<section>` - Thematic grouping of content
- `<aside>` - Content indirectly related to main content
- `<footer>` - Footer information, copyright, links
- `<figure>` / `<figcaption>` - Self-contained content with caption
- `<time>` - Machine-readable date/time

## 21. How do you handle form accessibility?

**Answer:**

```html
<!-- Accessible form -->
<form action="/submit" method="POST">
  <!-- 1. Explicit labels -->
  <div>
    <label for="name">Full Name:</label>
    <input type="text" id="name" name="name" required
           autocomplete="name"
           aria-describedby="name-hint">
    <span id="name-hint">Enter your first and last name</span>
  </div>
  
  <!-- 2. Grouped radio buttons -->
  <fieldset>
    <legend>Preferred Contact Method</legend>
    
    <input type="radio" id="contact-email" name="contact" value="email">
    <label for="contact-email">Email</label>
    
    <input type="radio" id="contact-phone" name="contact" value="phone">
    <label for="contact-phone">Phone</label>
  </fieldset>
  
  <!-- 3. Error handling with aria-invalid and aria-errormessage -->
  <div>
    <label for="email">Email Address:</label>
    <input type="email" id="email" name="email" required
           aria-invalid="true"
           aria-errormessage="email-error">
    <span id="email-error" role="alert">Please enter a valid email address</span>
  </div>
  
  <!-- 4. Required field indication -->
  <div>
    <label for="password">
      Password <span aria-label="required">*</span>:
    </label>
    <input type="password" id="password" name="password" required
           aria-required="true"
           autocomplete="new-password">
  </div>
  
  <button type="submit">Submit</button>
</form>
```

**Best practices:**
- Every `<input>` needs an associated `<label>`
- Use `<fieldset>` and `<legend>` for grouping related inputs
- Provide clear error messages with `aria-describedby` or `aria-errormessage`
- Use `aria-invalid` to indicate invalid fields
- Announce dynamic changes with `role="alert"` or `aria-live`
- Ensure keyboard navigation works with logical tab order

## 22. What is the `loading` attribute for images and iframes?

**Answer:** The `loading` attribute enables lazy loading, deferring the loading of off-screen images and iframes until the user scrolls near them.

```html
<!-- Eager (default) - load immediately -->
<img src="hero.jpg" loading="eager" alt="Hero image">

<!-- Lazy - load when near the viewport -->
<img src="gallery-image.jpg" loading="lazy" alt="Gallery image">

<!-- Auto - browser decides loading strategy -->
<img src="banner.jpg" loading="auto" alt="Banner">

<!-- Lazy iframe -->
<iframe src="video-player.html" loading="lazy" title="Video"></iframe>
```

**Browser behavior:**
- `<img loading="lazy">` - Defers loading until the image is close to the viewport (typically within 1250px on desktop, 2500px on mobile)
- `<iframe loading="lazy">` - Same lazy behavior for iframes
- **Fallback:** Browsers that don't support `loading` will load resources immediately

**Best practices:**
- Lazy load below-the-fold images
- Eager-load above-the-fold images (LCP candidates)
- Always provide explicit `width` and `height` to prevent layout shift
- Use `loading="lazy"` on decorative images, not critical images

## 23. What is the difference between `src` and `srcset`?

**Answer:**

- `src` - Single image source (fallback)
- `srcset` - Multiple image candidates for different conditions

```html
<!-- src alone - one image for all scenarios -->
<img src="image.jpg" alt="Description">

<!-- srcset with x-descriptors (pixel density) -->
<img src="image.jpg" 
     srcset="image-1x.jpg 1x, image-2x.jpg 2x, image-3x.jpg 3x"
     alt="Description">

<!-- srcset with w-descriptors (viewport width) - requires sizes -->
<img src="image.jpg"
     srcset="small.jpg 480w, medium.jpg 768w, large.jpg 1200w"
     sizes="(max-width: 600px) 100vw, (max-width: 1200px) 50vw, 33vw"
     alt="Description">
```

**Descriptors:**
- **x-descriptor:** Defines pixel density (1x = standard, 2x = retina)
- **w-descriptor:** Defines actual image width in pixels (used with `sizes` attribute)

**When to use each:**
- `srcset` with `x` - Same image displayed at same size, different densities
- `srcset` with `w` and `sizes` - Different image sizes depending on viewport

## 24. Explain the `contenteditable` attribute.

**Answer:** The `contenteditable` attribute makes HTML elements editable by the user directly in the browser.

```html
<!-- Make an element editable -->
<div contenteditable="true">
  This text can be edited directly by the user.
</div>

<!-- Heading that can be edited -->
<h1 contenteditable="true">Click to Edit Title</h1>

<!-- Entire section editable -->
<section contenteditable="true">
  <h2>Editable Section</h2>
  <p>All content here can be modified.</p>
</section>
```

**JavaScript interaction:**
```javascript
const editable = document.querySelector('[contenteditable]');

// Get content
editable.textContent;       // Plain text
editable.innerHTML;         // HTML content

// Save on blur
editable.addEventListener('blur', () => {
  localStorage.setItem('savedContent', editable.innerHTML);
});

// Restore
editable.innerHTML = localStorage.getItem('savedContent') || '';

// Check for changes
editable.addEventListener('input', () => {
  console.log('Content changed:', editable.innerHTML);
});
```

**Use cases:** Rich text editors, inline editing, WYSIWYG editors, collaborative editing

**Limitations:**
- Browser inconsistencies in how content is structured
- Limited formatting controls without custom toolbars
- Security consideration: filter input to prevent XSS

## 25. What is the `dialog` element?

**Answer:** The `<dialog>` element creates a modal or non-modal dialog box natively without requiring JavaScript libraries.

```html
<!-- Basic dialog (non-modal) -->
<dialog id="info-dialog">
  <p>This is a dialog</p>
  <button id="close-info">Close</button>
</dialog>
<button id="show-info">Show Info</button>

<!-- Modal dialog (blocks interaction with rest of page) -->
<dialog id="confirm-dialog">
  <form method="dialog">
    <p>Are you sure you want to delete this item?</p>
    <button value="confirm">Yes, Delete</button>
    <button value="cancel" autofocus>Cancel</button>
  </form>
</dialog>
<button id="show-confirm">Delete Item</button>

<script>
  const infoDialog = document.getElementById('info-dialog');
  const confirmDialog = document.getElementById('confirm-dialog');
  
  document.getElementById('show-info').onclick = () => infoDialog.show();
  document.getElementById('close-info').onclick = () => infoDialog.close();
  
  document.getElementById('show-confirm').onclick = () => confirmDialog.showModal();
  
  confirmDialog.addEventListener('close', () => {
    if (confirmDialog.returnValue === 'confirm') {
      // Proceed with deletion
    }
  });
</script>
```

**Key methods and properties:**
- `.show()` - Opens as non-modal
- `.showModal()` - Opens as modal (with backdrop)
- `.close(returnValue)` - Closes the dialog
- `.returnValue` - Value passed to `close()` or from `<form method="dialog">`
- `.open` - Boolean, whether dialog is open

**Styling:**
```css
dialog::backdrop {
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(2px);
}

dialog {
  border: none;
  border-radius: 8px;
  padding: 2rem;
  box-shadow: 0 4px 20px rgba(0,0,0,0.2);
}
```
