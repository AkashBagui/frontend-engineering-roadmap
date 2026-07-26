# HTML Mini Projects

## Project 1: Resume Website

### Requirements
- Personal information (name, title, contact)
- Work experience (chronological)
- Education
- Skills (with progress bars or tags)
- Portfolio links
- Semantic HTML structure

### Learning Objectives
- ✅ Document structure (`<!DOCTYPE>`, `<head>`, `<body>`)
- ✅ Semantic elements (`<header>`, `<main>`, `<section>`, `<footer>`)
- ✅ Heading hierarchy
- ✅ Lists (`<ul>`, `<ol>`, `<dl>`)
- ✅ Links and navigation
- ✅ Basic accessibility (alt text, ARIA labels)

### Tech Stack
- HTML5 only (no CSS required)

### Step-by-Step Guide

```
Step 1: Create document skeleton
    └── <!DOCTYPE html>, <html>, <head>, <body>

Step 2: Add metadata
    └── <meta charset>, <meta viewport>, <title>

Step 3: Create <header>
    └── Name, title, navigation links

Step 4: Create <main>
    └── <section> for Experience
    │   └── <article> for each job
    ├── <section> for Education
    ├── <section> for Skills
    └── <section> for Portfolio

Step 5: Create <footer>
    └── Contact info, copyright

Step 6: Add images
    └── Profile photo, project screenshots

Step 7: Validate HTML
    └── W3C validator
```

### Expected Structure

```
├── Profile photo
├── Name & Title
├── Navigation (About, Experience, Education, Skills, Contact)
├── About Me
│   └── Brief introduction
├── Work Experience
│   ├── Job 1 (Company, role, dates, description)
│   └── Job 2 (Company, role, dates, description)
├── Education
│   └── Degree, school, year
├── Skills
│   ├── HTML, CSS, JavaScript, etc.
├── Portfolio
│   ├── Project 1 (name, description, link)
│   └── Project 2 (name, description, link)
└── Footer
    ├── Email, LinkedIn, GitHub
    └── Copyright
```

---

## Project 2: Portfolio Homepage

### Requirements
- Hero section with tagline
- About section
- Project gallery (grid layout)
- Skills section
- Testimonials (blockquotes)
- Contact form
- Smooth scrolling navigation

### Learning Objectives
- ✅ Forms with validation
- ✅ Embedded media (images, video)
- ✅ HTML5 input types
- ✅ Semantic layout
- ✅ Tables (for skills or pricing)
- ✅ Accessibility best practices

### Tech Stack
- HTML5 + Basic CSS (optional)

### Step-by-Step Guide

```
Step 1: Set up HTML skeleton

Step 2: Create navigation
    └── <nav> with links to sections

Step 3: Build Hero Section
    └── <header> with h1, tagline, CTA button

Step 4: About Section
    └── <section> with image and bio

Step 5: Project Gallery
    └── <section> with <figure>/<figcaption> for each project
    └── Use <picture> for responsive project images

Step 6: Skills Section
    └── <table> or definition list of skills

Step 7: Testimonials
    └── <blockquote> with <cite>

Step 8: Contact Section
    └── <form> with name, email, subject, message
    └── Required fields, email type validation

Step 9: Footer
    └── Social links, copyright

Step 10: Add skip navigation link
```

### Form Fields

| Field | Type | Validation |
|-------|------|------------|
| Name | `text` | `required` |
| Email | `email` | `required` |
| Subject | `select` | `required` |
| Message | `textarea` | `required`, `minlength="10"` |
| Budget | `range` | Min/max |
| Submit | `submit` | — |

---

## Project 3: Product Landing Page

### Requirements
- Product showcase with images/video
- Features section with icons
- Pricing table
- FAQ accordion (using `<details>`)
- Customer reviews
- Call-to-action section
- Newsletter signup form

### Learning Objectives
- ✅ Tables (`<thead>`, `<tbody>`, `<tfoot>`, `colspan`)
- ✅ Media (`<video>`, `<audio>`, `<picture>`)
- ✅ Interactive elements (`<details>`, `<summary>`)
- ✅ Structured data (JSON-LD)
- ✅ Open Graph meta tags
- ✅ Advanced forms

### Tech Stack
- HTML5 + CSS (styling tables, responsive images)

### Step-by-Step Guide

```
Step 1: HTML skeleton + meta tags (OG, Twitter)

Step 2: Hero Section
    └── Product image with <picture> for responsive sizes
    └── Headline, sub-headline, CTA button

Step 3: Features
    └── <section> with <article> for each feature
    └── Icons using SVG inline

Step 4: Product Demo
    └── <video> with controls and captions

Step 5: Pricing Table
    └── <table> with <thead>, <tbody>, <tfoot>
    └── colspan/rowspan for feature comparison
    └── scope attributes for accessibility

Step 6: Customer Reviews
    └── <blockquote> with customer images

Step 7: FAQ
    └── <details>/<summary> for each question

Step 8: Newsletter Form
    └── Email input with validation
    └── Name, preferences (checkboxes)

Step 9: Structured Data
    └── JSON-LD for Product schema

Step 10: Footer with links
```

### Pricing Table Columns

```
┌──────────────┬─────────┬──────────┬────────────┐
│  Features    │ Basic   │ Pro      │ Enterprise │
├──────────────┼─────────┼──────────┼────────────┤
│  Price       │ $9/mo   │ $29/mo   │ $99/mo     │
│  Users       │ 1       │ 5        │ Unlimited  │
│  Storage     │ 5GB     │ 50GB     │ 500GB      │
│  Support     │ Email   │ Chat     │ Phone 24/7 │
│  CTA         │ [Sign]  │ [Sign]   │ [Sign]     │
└──────────────┴─────────┴──────────┴────────────┘
```

---

## Project 4: Restaurant Website

### Requirements
- Hero section with restaurant name and ambiance image
- Menu with categories (starters, mains, desserts, drinks)
- Reservation form
- Contact info with embedded map
- Photo gallery
- Business hours table
- Chef's special section

### Learning Objectives
- ✅ Nested lists for complex menus
- ✅ Tables for business hours
- ✅ Form with radio buttons and checkboxes
- ✅ `<address>` for contact info
- ✅ Embedding external content (map)
- ✅ Accessibility for forms and tables

### Tech Stack
- HTML5 only

### Step-by-Step Guide

```
Step 1: Document setup

Step 2: Navigation
    └── Menu, Reservation, Gallery, Contact

Step 3: Hero
    └── Restaurant name, tagline

Step 4: About
    └── History, chef bio with <figure>

Step 5: Menu Section
    └── <section> for each category
    │   └── <dl>/<ul> of dishes with prices
    ├── Chef's special with <mark>

Step 6: Business Hours
    └── <table> with <thead> and <tbody>
    └── scope attributes

Step 7: Photo Gallery
    └── Grid of <figure>/<figcaption>

Step 8: Reservation Form
    └── Date, time (input types), party size
    └── Special requests (textarea)
    └── Radio buttons for seating preference
    └── Required fields with validation

Step 9: Contact Section
    └── <address> for physical address
    └── Phone, email links
    └── Embedded Google Maps iframe

Step 10: Footer
    └── Hours, social links, copyright
```

### Reservation Form Fields

| Field | Type | Purpose |
|-------|------|---------|
| Name | `text` | Guest name |
| Phone | `tel` | Contact number |
| Email | `email` | Confirmation |
| Date | `date` | Reservation date |
| Time | `time` | Reservation time |
| Party Size | `number` | Number of guests |
| Seating | `radio` | Indoor, Outdoor, Bar |
| Special Requests | `textarea` | Allergies, celebrations |
| Submit | `submit` | Send reservation |

---

## Project 5: Blog Website

### Requirements
- Blog post listing with pagination
- Individual blog post with comments section
- Categories and tags
- Author bio sidebar
- Search functionality
- RSS feed link
- Related posts section

### Learning Objectives
- ✅ Complete SEO setup (meta tags, structured data)
- ✅ `article` and nested semantic elements
- ✅ Comments section with form validation
- ✅ Responsive images with `<picture>`
- ✅ Accessibility (skip links, landmarks, ARIA)
- ✅ Structured data (JSON-LD Article schema)

### Tech Stack
- HTML5 (multi-page site)

### Pages to Create

```
blog/
├── index.html          (Blog listing)
├── post.html           (Single blog post)
├── about.html          (About the author)
└── contact.html        (Contact form)
```

### Step-by-Step Guide: index.html

```
Step 1: HTML skeleton with SEO meta tags
Step 2: <header> with blog name, nav, search form
Step 3: <main> with blog post cards
    └── <article> for each post preview
    │   └── Image, title, excerpt, date, tags, "Read more"
Step 4: <aside> with sidebar
    └── Author bio, categories list, tag cloud
Step 5: <footer>
    └── RSS link, social links, copyright
```

### Step-by-Step Guide: post.html

```
Step 1: Head with Article-specific meta tags
Step 2: <header> with nav
Step 3: <main> with <article>
    └── <header> with title, author, date
    ├── Content with sections, images, blockquotes
    ├── <footer> with tags and share links
Step 4: Comments Section
    └── Existing comments (article per comment)
    └── Comment form (name, email, website, comment)
Step 5: Related Posts
    └── <section> with <article> cards
Step 6: <aside> (same as index)
Step 7: <footer>
Step 8: JSON-LD structured data (Article schema)
```

### SEO Setup Per Page

```html
<title>Blog Post Title - Blog Name</title>
<meta name="description" content="Post excerpt...">
<meta property="og:title" content="Blog Post Title">
<meta property="og:type" content="article">
<meta property="article:published_time" content="2026-03-15">
<link rel="canonical" href="https://example.com/blog/post-slug">
```

### JSON-LD for Blog Post

```html
<script type="application/ld+json">
{
    "@context": "https://schema.org",
    "@type": "BlogPosting",
    "headline": "Blog Post Title",
    "author": { "@type": "Person", "name": "Author Name" },
    "datePublished": "2026-03-15T10:00:00+00:00",
    "image": "https://example.com/images/post-image.jpg",
    "description": "Post excerpt..."
}
</script>
```

---

## Project Evaluation Criteria

| Criteria | Weight | What to Check |
|----------|--------|---------------|
| Valid HTML | 20% | W3C validator passes |
| Semantic Elements | 20% | Proper use of `<header>`, `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>` |
| Accessibility | 15% | Alt text, labels, headings hierarchy, ARIA |
| Forms | 15% | Correct types, validation, labels |
| Media | 10% | Responsive images, video/audio, alt text |
| Tables | 10% | Proper structure, scope attributes, caption |
| SEO | 10% | Meta tags, title, description, structured data |
