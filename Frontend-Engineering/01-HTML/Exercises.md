# HTML Exercises

## Beginner Exercises

### Exercise 1: Your First HTML Page

**Objective:** Create a basic HTML document with proper structure.

**Requirements:**
- `<!DOCTYPE html>` declaration
- `<html>`, `<head>`, `<body>` tags
- A `<title>` (your name + "Portfolio")
- A `<h1>` heading with your name
- A `<p>` paragraph about yourself

**Expected Outcome:**
```
 ┌─────────────────────────────────────┐
 │  Browser tab: "John Doe Portfolio"  │
 ├─────────────────────────────────────┤
 │                                     │
 │  John Doe                           │
 │                                     │
 │  I'm a web developer passionate     │
 │  about building accessible and      │
 │  beautiful websites.                │
 │                                     │
 └─────────────────────────────────────┘
```

**Hint:** Use the HTML skeleton from [01-Introduction.md](01-Introduction.md).

---

### Exercise 2: Text Formatting

**Objective:** Use various inline text elements to format content.

**Requirements:**
- `<h1>` to `<h6>` headings
- `<p>` with `<strong>`, `<em>`, `<mark>`, `<small>` tags
- A `<blockquote>` with `<cite>`
- `<code>` and `<pre>` for code snippets
- Subscript (`<sub>`) and superscript (`<sup>`)

**Example:**
```html
<p>The chemical formula for water is H<sub>2</sub>O.</p>
<p>Einstein's famous equation: E = mc<sup>2</sup></p>
```

**Hint:** Remember `<strong>` is for importance, `<b>` is for stylistic offset.

---

### Exercise 3: Lists and Links

**Objective:** Create a navigation menu with nested lists.

**Requirements:**
- An `<nav>` element
- A nested list: `<ul>` with `<li>` containing sub-`<ul>`
- At least 3 main categories with 2 sub-items each
- Links (`<a>`) with `href` for each item
- A second `<ol>` (ordered list) showing step-by-step instructions

**Example Structure:**
```
Navigation:
├── HTML
│   ├── Introduction
│   └── Elements
├── CSS
│   ├── Selectors
│   └── Flexbox
└── JavaScript
    ├── Variables
    └── Functions

How to learn web development:
1. Learn HTML
2. Learn CSS
3. Learn JavaScript
```

**Hint:** Use `target="_blank"` with `rel="noopener noreferrer"` for external links.

---

### Exercise 4: Image Gallery

**Objective:** Create a gallery of images with proper accessibility.

**Requirements:**
- At least 4 images using `<img>` with meaningful `alt` text
- A `<figure>` with `<figcaption>` for each image
- One decorative image with empty `alt=""`
- Use different image formats (JPG, PNG, SVG)

**Expected Outcome:**
```
┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐
│      │ │      │ │      │ │      │
│ Image│ │ Image│ │ Image│ │ Image│
│  1   │ │  2   │ │  3   │ │  4   │
│      │ │      │ │      │ │      │
└──────┘ └──────┘ └──────┘ └──────┘
Fig 1:   Fig 2:   Fig 3:   Fig 4:
Sunset   Forest   Ocean    Mountains
```

**Hint:** Use SVG inline for a simple icon (like a star or heart).

---

### Exercise 5: Simple Table

**Objective:** Create a weekly schedule table.

**Requirements:**
- `<table>` with `<caption>`, `<thead>`, `<tbody>`
- Days of the week as column headers (`<th scope="col">`)
- Time slots as row headers (`<th scope="row">`)
- At least 3 time slots and 5 days
- Use `colspan` for activities spanning multiple hours

**Example:**
```
┌──────────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│          │  Monday  │ Tuesday  │ Wednesday│ Thursday │  Friday  │
├──────────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ 9:00-10  │  Math    │  Science │  Math    │  Science │  Math    │
│ 10-11:00 │  English │  History │  English │  History │  PE      │
│ 11-12:00 │  Lunch   │  Lunch   │  Lunch   │  Lunch   │  Lunch   │
└──────────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

**Hint:** Add `scope="col"` and `scope="row"` for accessibility.

---

## Intermediate Exercises

### Exercise 6: Contact Form

**Objective:** Build a complete contact form with validation.

**Requirements:**
- `<form>` with `method="POST"`
- Fields: name (`text`), email (`email`), phone (`tel`), subject (`select`), message (`textarea`)
- All fields have `<label>` with `for` attribute
- `required` validation on name, email, message
- `pattern` validation on phone
- A `<fieldset>` with `<legend>`
- Submit and reset buttons

**Expected Outcome:**
```
┌─────────────────────────────────────────┐
│  Contact Us                              │
│                                         │
│  ┌───────────────────────────────────┐  │
│  │ Personal Information              │  │
│  │                                   │  │
│  │ Name:    [______________________] │  │
│  │ Email:   [______________________] │  │
│  │ Phone:   [______________________] │  │
│  │ Subject: [_____________▼________] │  │
│  │ Message: [______________________] │  │
│  │          [______________________] │  │
│  │                                   │  │
│  │ [Submit] [Reset]                  │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

**Hint:** Use `<select>` with `<optgroup>` for subject categories.

---

### Exercise 7: Semantic HTML Refactoring

**Objective:** Convert a "div soup" page into semantic HTML.

**Starting code (refactor this):**

```html
<div class="page">
    <div class="top">
        <div class="logo">My Site</div>
        <div class="menu">
            <a href="/">Home</a>
            <a href="/blog">Blog</a>
        </div>
    </div>
    <div class="content">
        <div class="post">
            <h2>Blog Post</h2>
            <div class="meta">Posted on Jan 1, 2026</div>
            <p>Content...</p>
        </div>
        <div class="sidebar">
            <h3>About</h3>
            <p>Bio...</p>
        </div>
    </div>
    <div class="bottom">
        <p>Copyright 2026</p>
    </div>
</div>
```

**Requirements:**
- Replace all `<div>` with appropriate semantic elements
- Add ARIA landmarks where appropriate
- Ensure proper heading hierarchy
- Add missing metadata (charset, viewport, title)

**Hint:** Think about what each section represents: header, nav, main, article, aside, footer.

---

### Exercise 8: Video and Audio Player

**Objective:** Embed audio and video with fallbacks and accessibility.

**Requirements:**
- `<video>` with controls, poster, and multiple `<source>` formats
- `<audio>` with controls and multiple `<source>` formats
- `<track>` for captions on the video
- Fallback text for unsupported browsers
- Download links as a second fallback
- Use `<picture>` for a video poster image

**Expected Outcome:**
```
┌─────────────────────────────────────────┐
│  ┌─────────────────────────────────┐   │
│  │           Video Player          │   │
│  │     [poster image]             │   │
│  │                                 │   │
│  │  ▶───────────────────────[  ]  │   │
│  │  [=====───────────────] 0:32   │   │
│  │  [CC] [Fullscreen]             │   │
│  └─────────────────────────────────┘   │
│                                         │
│  ┌─────────────────────────────────┐   │
│  │  ▶ [=====───────────────] 1:15 │   │
│  │  Audio Player                    │   │
│  └─────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

**Hint:** Create a `.vtt` file for captions with at least 3 cue points.

---

### Exercise 9: Complex Table with Merged Cells

**Objective:** Create a school report card or invoice.

**Requirements:**
- `<caption>` with document title
- `<thead>` with header structure
- `<tbody>` with data rows
- `<tfoot>` with totals
- Use `colspan` and `rowspan`
- Use `scope` on all `<th>` elements
- A responsive container (for mobile)

**Example: Invoice**
```
┌────────────────────────────────────────────────┐
│                    INVOICE                      │
├──────────┬────────────┬──────┬────────┬────────┤
│ Item     │ Description│ Qty  │ Price  │ Total  │
├──────────┼────────────┼──────┼────────┼────────┤
│ Widget A │ Blue large │  2   │ $25.00 │ $50.00 │
│ Widget B │ Red small  │  3   │ $15.00 │ $45.00 │
│ Shipping │ (colspan=3)│      │        │ $10.00 │
├──────────┴────────────┴──────┴────────┼────────┤
│ Grand Total                           │$105.00 │
└───────────────────────────────────────┴────────┘
```

**Hint:** The shipping row uses `colspan` to span 4 columns for the label.

---

### Exercise 10: Registration Form with All Input Types

**Objective:** Build a comprehensive user registration form.

**Requirements:**
- Use at least 10 different input types
- `<fieldset>` for grouping (Account, Profile, Preferences)
- HTML5 validation (required, pattern, min, max, step)
- `<datalist>` for suggestions
- `<progress>` or `<meter>` element
- Checkboxes and radio button groups

**Fields to include:**
- Username (text, pattern for alphanumeric)
- Email (email)
- Password (password, minlength)
- Age (number, min/max)
- Birth date (date)
- Country (select with optgroup)
- Favorite color (color)
- Avatar upload (file, accept images)
- Bio (textarea)
- Newsletter (checkbox)
- Gender (radio)
- Experience level (range slider)
- Terms agreement (checkbox, required)

**Hint:** Use `autocomplete` attributes to help users fill the form faster.

---

## Advanced Exercises

### Exercise 11: Accessibility Audit and Fix

**Objective:** Given a page with accessibility issues, identify and fix them.

**Sample page with issues:**
```html
<html>
<head>
    <title>My Page</title>
</head>
<body>
    <div class="header">My Site</div>
    <div class="content">
        <div class="section">
            <span class="title" style="font-size: 24px;">Section 1</span>
            <img src="photo.jpg">
            <div onclick="submitForm()" class="button">Submit</div>
        </div>
        <div style="color: #999; font-size: 11px;">
            This text has low contrast
        </div>
    </div>
</body>
</html>
```

**Issues to find and fix:**
1. Missing `<html lang="">` attribute
2. No viewport meta tag
3. No charset meta tag
4. `<div>` used instead of semantic elements
5. Heading-like text using `<span>` instead of `<h1>`-`<h6>`
6. Image missing `alt` text
7. `<div>` used as button (not keyboard accessible)
8. Low contrast text
9. No skip navigation link
10. No heading hierarchy

**Hint:** Run the page through WAVE or axe DevTools to identify issues.

---

### Exercise 12: SEO-Optimized Blog Post

**Objective:** Create an SEO-optimized blog post page.

**Requirements:**
- Complete meta tags (charset, viewport, description, robots, canonical)
- Open Graph tags (title, description, image, type, url)
- Twitter Card tags
- JSON-LD structured data (Article schema)
- Proper heading hierarchy
- Semantic elements
- Internal links with descriptive anchor text
- Image alt text
- Breadcrumb navigation
- Related posts section with links

**Structured data to include:**
```json
{
    "@context": "https://schema.org",
    "@type": "BlogPosting",
    "headline": "10 Tips for Better HTML",
    "description": "Learn 10 practical tips to improve your HTML coding.",
    "author": {
        "@type": "Person",
        "name": "Jane Developer"
    },
    "datePublished": "2026-03-15",
    "dateModified": "2026-03-20",
    "image": "https://example.com/images/html-tips.jpg"
}
```

**Hint:** Add `<link rel="canonical">` and include breadcrumb structured data too.

---

### Exercise 13: Interactive Tab Component (ARIA)

**Objective:** Build an accessible tabbed interface.

**Requirements:**
- Use `role="tablist"`, `role="tab"`, `role="tabpanel"`
- `aria-selected` on the active tab
- `aria-controls` linking tabs to panels
- `aria-labelledby` on panels linking back to tabs
- `tabindex` management (only active tab is focusable)
- Keyboard navigation (Arrow keys to switch tabs)
- At least 3 tabs with different content

**Expected Structure:**
```html
<div class="tabs">
    <div role="tablist" aria-label="Product Information">
        <button role="tab" aria-selected="true"
                aria-controls="panel-1" id="tab-1"
                tabindex="0">Description</button>
        <button role="tab" aria-selected="false"
                aria-controls="panel-2" id="tab-2"
                tabindex="-1">Specifications</button>
        <button role="tab" aria-selected="false"
                aria-controls="panel-3" id="tab-3"
                tabindex="-1">Reviews</button>
    </div>
    <div role="tabpanel" id="panel-1"
         aria-labelledby="tab-1">
        <!-- Description content -->
    </div>
    <div role="tabpanel" id="panel-2"
         aria-labelledby="tab-2" hidden>
        <!-- Specifications content -->
    </div>
    <div role="tabpanel" id="panel-3"
         aria-labelledby="tab-3" hidden>
        <!-- Reviews content -->
    </div>
</div>
```

**Hint:** Use `hidden` attribute to show/hide tab panels. Only one should be visible at a time.

---

### Exercise 14: Web Component (Custom Element)

**Objective:** Create a custom HTML element using Web Components.

**Requirements:**
- Define a custom element using `customElements.define()`
- Use Shadow DOM for style encapsulation
- Accept attributes (like `title`, `type`, `color`)
- Use `<slot>` for content projection
- Style the host element with `:host`

**Example: Custom Alert Component**
```html
<custom-alert type="success" title="Done!">
    Your changes have been saved.
</custom-alert>
<custom-alert type="error" title="Error!">
    Something went wrong.
</custom-alert>
```

**Hint:** Create different visual styles based on the `type` attribute (success, warning, error, info).

---

### Exercise 15: Complete Single-Page Portfolio

**Objective:** Build a complete portfolio page combining all skills learned.

**Requirements:**
- Navigation with smooth-scrolling anchor links
- Hero section with name and tagline
- About section with bio and skills table
- Project gallery with `<figure>`/`<figcaption>`
- Testimonials with `<blockquote>`
- Contact form with all input types
- Footer with links
- Complete SEO setup (meta tags, OG, JSON-LD)
- Accessibility features (skip link, ARIA, semantic HTML)
- Responsive images with `<picture>`
- Embedded video (project demo)

**Structure:**
```
┌────────────────────────────────────────┐
│  Skip to content link                  │
├────────────────────────────────────────┤
│  <header> <nav> Home | About |         │
│  Projects | Testimonials | Contact     │
├────────────────────────────────────────┤
│  <main>                                │
│  ├── Hero Section                      │
│  ├── About Section (skills table)      │
│  ├── Projects Section (gallery)        │
│  ├── Testimonials Section              │
│  └── Contact Section (form)            │
├────────────────────────────────────────┤
│  <aside> (optional: latest blog posts) │
├────────────────────────────────────────┤
│  <footer> Social, copyright            │
└────────────────────────────────────────┘
```

**Hint:** This exercise combines everything from all previous exercises. Make sure to validate your HTML and test it with a screen reader.

---

### Exercise 16: FAQ Page with JSON-LD

**Objective:** Create an SEO-optimized FAQ page with structured data.

**Requirements:**
- Use `<details>` and `<summary>` for collapsible questions
- At least 5 FAQ items
- JSON-LD `FAQPage` structured data
- Proper meta tags for SEO
- Breadcrumb navigation
- Search functionality (basic text input that filters questions)

**JSON-LD Schema:**
```json
{
    "@context": "https://schema.org",
    "@type": "FAQPage",
    "mainEntity": [
        {
            "@type": "Question",
            "name": "What is HTML?",
            "acceptedAnswer": {
                "@type": "Answer",
                "text": "HTML is HyperText Markup Language..."
            }
        }
    ]
}
```

**Hint:** Each `<details>` item corresponds to one entry in the `mainEntity` array.

---

### Exercise 17: Responsive Email Template

**Objective:** Build an HTML email template (a unique challenge since email HTML has quirks).

**Requirements:**
- Table-based layout (email clients need `<table>` for layout)
- Inline CSS (most email clients strip `<style>`)
- A header with logo
- Main content section with image and text
- A CTA button (using `<a>` styled as button)
- A footer with unsubscribe link
- Alt text on all images

**Note:** HTML email development is very different from web HTML. This exercise teaches the legacy table-based approach.

**Hint:** Use `<!--[if mso]>` conditional comments for Outlook-specific fixes.

---

## Exercise Checklist

| # | Exercise | Skills | Difficulty | Est. Time |
|---|----------|--------|------------|-----------|
| 1 | First HTML Page | Structure, DOCTYPE | ⭐ | 10 min |
| 2 | Text Formatting | Inline tags | ⭐ | 15 min |
| 3 | Lists and Links | `<ul>`, `<ol>`, `<a>` | ⭐ | 15 min |
| 4 | Image Gallery | `<img>`, `<figure>`, accessibility | ⭐⭐ | 20 min |
| 5 | Simple Table | `<table>` basics | ⭐⭐ | 20 min |
| 6 | Contact Form | Forms, validation | ⭐⭐ | 30 min |
| 7 | Semantic Refactoring | Semantic elements | ⭐⭐ | 20 min |
| 8 | Video/Audio Player | Media elements | ⭐⭐ | 25 min |
| 9 | Complex Table | Colspan, rowspan | ⭐⭐⭐ | 30 min |
| 10 | Registration Form | All input types | ⭐⭐⭐ | 35 min |
| 11 | Accessibility Fix | WCAG, ARIA | ⭐⭐⭐ | 30 min |
| 12 | SEO Blog Post | Meta tags, JSON-LD | ⭐⭐⭐ | 40 min |
| 13 | Tab Component | ARIA, keyboard | ⭐⭐⭐⭐ | 45 min |
| 14 | Web Component | Shadow DOM, custom elements | ⭐⭐⭐⭐ | 45 min |
| 15 | Portfolio Page | All skills combined | ⭐⭐⭐⭐⭐ | 60 min |
| 16 | FAQ Page | `<details>`, structured data | ⭐⭐⭐ | 30 min |
| 17 | Email Template | Table layout, inline CSS | ⭐⭐⭐⭐ | 40 min |
