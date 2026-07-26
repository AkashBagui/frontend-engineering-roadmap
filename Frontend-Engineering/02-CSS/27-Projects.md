# CSS Projects

## Project 1: Personal Portfolio

### Requirements

Create a personal portfolio website with responsive design, smooth animations, and a clean layout.

### Sections

1. **Navigation** — Sticky header with smooth scroll, hamburger menu on mobile
2. **Hero** — Full-viewport hero with name, tagline, CTA button, animated background
3. **About** — Photo + bio with CSS shape around image
4. **Skills** — Skill bars with animated width on scroll
5. **Projects** — Card grid with hover effects
6. **Contact** — Form with styled inputs, focus states
7. **Footer** — Social links, copyright

### CSS Concepts to Practice

```css
/* Sticky nav */
.header { position: sticky; top: 0; z-index: 100; }

/* Hero full-viewport */
.hero { min-height: 100vh; display: grid; place-items: center; }

/* Skill bar animation */
@keyframes fill-bar {
  from { width: 0; }
  to { width: var(--skill-level); }
}

.skill-bar::after {
  content: '';
  display: block;
  height: 100%;
  background: var(--primary);
  animation: fill-bar 1.5s ease-out forwards;
}

/* Project card grid */
.project-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 2rem;
}
```

### Bonus

- Dark/light mode toggle
- Scroll-triggered animations (use Intersection Observer or `@keyframes`)
- CSS-only hamburger menu animation

---

## Project 2: Product Landing Page

### Requirements

Build a marketing landing page for a fictional product. Focus on visual hierarchy, typography, and conversion-focused design.

### Sections

1. **Header** — Logo, nav links, CTA button
2. **Hero** — Product image, headline, subtext, sign-up form
3. **Features** — 3-column grid with icons (use emoji or CSS shapes)
4. **Testimonials** — Card carousel with CSS-only scroll snap
5. **Pricing** — 3-tier pricing cards with featured plan highlighted
6. **FAQ** — Accordion using `details`/`summary` + CSS
7. **Footer** — Multi-column footer

### CSS Concepts to Practice

```css
/* Pricing card — featured plan */
.pricing-card--featured {
  transform: scale(1.05);
  position: relative;
}
.pricing-card--featured::before {
  content: 'Popular';
  position: absolute;
  top: -12px;
  left: 50%;
  transform: translateX(-50%);
  background: var(--primary);
  color: white;
  padding: 4px 16px;
  border-radius: 999px;
  font-size: 0.875rem;
}

/* Scroll snap for testimonials */
.testimonial-carousel {
  display: flex;
  overflow-x: auto;
  scroll-snap-type: x mandatory;
  scroll-behavior: smooth;
}
.testimonial-carousel > * {
  scroll-snap-align: start;
  flex: 0 0 100%;
}

/* FAQ accordion */
details { border-bottom: 1px solid #ddd; padding: 1rem 0; }
details summary { cursor: pointer; font-weight: 600; }
details[open] summary { color: var(--primary); }
details[open] ~ details { /* style open state */ }
```

### Bonus

- Animated CTA button (pulse/glow effect)
- CSS-only mobile nav toggle (checkbox hack)
- Custom scrollbar styling

---

## Project 3: Restaurant Website

### Requirements

A visually rich restaurant website showcasing menu, ambiance photos, and reservation flow.

### Sections

1. **Header** — Full-bleed hero with background image, overlay, restaurant name
2. **About** — Story section with floated image, drop cap
3. **Menu** — Grid with food cards, category tabs (CSS-only)
4. **Gallery** — Masonry layout using CSS grid
5. **Reviews** — Star ratings, customer quotes
6. **Reservation** — Form with date/time picker (styled selects)
7. **Map/Contact** — Embedded map, address, hours
8. **Footer** — Hours, location, social

### CSS Concepts to Practice

```css
/* Hero with overlay */
.hero {
  position: relative;
  background: url('hero.jpg') center/cover;
  min-height: 80vh;
}
.hero::after {
  content: '';
  position: absolute;
  inset: 0;
  background: rgba(0,0,0,0.4);
}
.hero-content { position: relative; z-index: 1; }

/* Drop cap */
.story p:first-of-type::first-letter {
  font-size: 4em;
  float: left;
  line-height: 1;
  margin-right: 0.5rem;
  color: var(--accent);
  font-family: serif;
}

/* Masonry grid */
.masonry {
  columns: 3;
  gap: 1rem;
}
.masonry > * {
  break-inside: avoid;
  margin-bottom: 1rem;
}
@media (max-width: 768px) { .masonry { columns: 2; } }
@media (max-width: 480px) { .masonry { columns: 1; } }
```

### Bonus

- Animated menu item hover (scale + shadow)
- CSS-only tab switching (radio button hack)
- Responsive typography scale

---

## Project 4: Blog Layout

### Requirements

A content-focused blog with article layout, sidebar, and reading experience optimization.

### Sections

1. **Header** — Blog name, categories, search (styled input)
2. **Featured Post** — Large feature card with gradient overlay
3. **Article Grid** — Mixed layout (featured grid + list)
4. **Single Article** — Optimal reading experience, typography
5. **Sidebar** — Author card, recent posts, categories, tags
6. **Comments** — Threaded comments with indentation
7. **Pagination** — Page numbers with active state
8. **Footer** — Minimal footer

### CSS Concepts to Practice

```css
/* Optimal reading experience */
.article {
  max-width: 65ch;           /* Ideal line length */
  margin-inline: auto;
  font-size: clamp(1rem, 1.125rem, 1.25rem);
  line-height: 1.8;
}

/* Article images */
.article img {
  width: 100%;
  border-radius: 8px;
  margin-block: 2rem;
}

/* Pull quote */
.pull-quote {
  font-size: 1.5rem;
  font-style: italic;
  border-inline-start: 4px solid var(--primary);
  padding-inline-start: 1.5rem;
  margin: 2rem 0;
  float: inline-start;      /* CSS logical float */
  width: 40%;
  margin-inline-end: 1.5rem;
}

/* Tag styling */
.tags {
  display: flex;
  flex-wrap: wrap;
  gap: 0.5rem;
}
.tag {
  background: #f0f0f0;
  padding: 0.25rem 0.75rem;
  border-radius: 999px;
  font-size: 0.875rem;
  transition: background 0.2s;
}
.tag:hover { background: var(--primary); color: white; }
```

### Bonus

- Reading progress bar (fixed top)
- Code block syntax highlighting simulation
- Sticky sidebar with `position: sticky`

---

## Project 5: Dashboard UI

### Requirements

A functional admin dashboard with data visualizations, tables, and controls.

### Sections

1. **Sidebar** — Collapsible navigation with icons, active states
2. **Header** — Search, notifications (badge), user avatar dropdown
3. **Stats Cards** — 4 KPI cards with trend indicators (up/down arrows)
4. **Charts** — Bar chart and line chart (CSS-only, no JS library)
5. **Data Table** — Responsive table with sticky header
6. **Activity Feed** — Timeline layout with dots and lines
7. **Quick Actions** — Button group
8. **Calendar** — Mini calendar (CSS grid dates)

### CSS Concepts to Practice

```css
/* Sidebar */
.sidebar {
  width: 250px;
  height: 100vh;
  position: fixed;
  left: 0;
  transition: transform 0.3s;
}
.sidebar.collapsed { transform: translateX(-250px); }

/* Stats card */
.stat-card {
  display: flex;
  justify-content: space-between;
  padding: 1.5rem;
  border-radius: 12px;
  background: white;
  box-shadow: 0 2px 8px rgba(0,0,0,0.06);
}
.stat-card--up { border-top: 3px solid green; }
.stat-card--down { border-top: 3px solid red; }

/* CSS-only bar chart */
.bar-chart {
  display: flex;
  align-items: flex-end;
  gap: 0.5rem;
  height: 200px;
}
.bar {
  flex: 1;
  background: var(--primary);
  height: var(--value);           /* Set in HTML as inline style */
  border-radius: 4px 4px 0 0;
  transition: height 0.5s ease;
  position: relative;
}
.bar:hover { opacity: 0.8; }
.bar::after {
  content: attr(data-value);
  position: absolute;
  top: -1.5rem;
  left: 50%;
  transform: translateX(-50%);
  font-size: 0.75rem;
}

/* Responsive table */
.table-wrapper { overflow-x: auto; }
table { width: 100%; min-width: 600px; border-collapse: collapse; }
thead { position: sticky; top: 0; background: white; }
th, td { padding: 0.75rem 1rem; text-align: left; border-bottom: 1px solid #eee; }

/* Badge */
.badge {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: red;
  color: white;
  font-size: 0.75rem;
}
```

### Bonus

- Dark mode for dashboard
- Animated chart bars on load
- Skeleton loading states
- Tooltip on hover for data points
