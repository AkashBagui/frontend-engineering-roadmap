# Projects — Frontend Mindset

## Mini Project: Google Homepage Clone

A frontend engineering project to practice HTML, CSS layout, responsive design, and basic interactivity by cloning the Google search homepage.

## Learning Objectives

| Skill | What You Will Practice |
|-------|----------------------|
| **HTML** | Semantic structure, forms, meta tags, favicon |
| **CSS** | Flexbox, Grid, positioning, hover effects, responsive design |
| **Accessibility** | ARIA labels, focus management, semantic elements |
| **Performance** | Minimal bundle, efficient CSS, image optimization |
| **DevTools** | Inspect, responsive mode, Lighthouse audit |
| **Version Control** | Git workflow, meaningful commits |

## Tech Stack

`
- HTML5 (semantic elements)
- CSS3 (no frameworks - pure CSS)
- Vanilla JavaScript (minimal - just for interactive elements)
- Git + GitHub (version control)
- Vite or live-server (dev server, optional)
`

## Requirements

### Functional Requirements

`
[ ] Page loads with Google logo centered
[ ] Search input field in the center
[ ] "Google Search" button
[ ] "I'm Feeling Lucky" button
[ ] Buttons are clickable (can link to actual Google search)
[ ] Footer with country and links (Privacy, Terms, Settings, Advertising, Business, About)
[ ] Header with Gmail, Images links, and Sign In button
[ ] Hover effects on links and buttons
[ ] Focus state on search input
[ ] Responsive: works on mobile (320px+) and desktop
`

### Technical Requirements

`
[ ] Valid HTML5 with proper doctype
[ ] CSS in external stylesheet (no inline styles)
[ ] Mobile-first responsive design
[ ] No CSS frameworks (no Tailwind, Bootstrap)
[ ] Lighthouse score > 90 for Performance, Accessibility, Best Practices
[ ] No JavaScript errors in console
[ ] Semantic HTML (header, main, footer, nav)
[ ] Proper heading hierarchy
[ ] Alt text on all images
`

## Step-by-Step Implementation Guide

### Phase 1: Project Setup

`
Step 1: Create project directory
  google-clone/
  ├── index.html
  ├── styles.css
  └── README.md

Step 2: Initialize Git
  git init
  git add .
  git commit -m "Initial commit: project structure"
`

### Phase 2: HTML Structure

`html
<!-- index.html skeleton -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Google</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <header>
    <!-- Top navigation: Gmail, Images, Sign In -->
  </header>

  <main>
    <!-- Logo -->
    <!-- Search form -->
    <!-- Buttons -->
  </main>

  <footer>
    <!-- Country -->
    <!-- Footer links -->
  </footer>
</body>
</html>
`

**Tasks:**
1. Create the HTML file with semantic structure
2. Add header with Gmail link, Images link, and Sign In button
3. Add main section with Google logo (use the actual Google logo SVG or text-based)
4. Create search form with input field
5. Add Google Search and I'm Feeling Lucky buttons
6. Add footer with country line and link groups

### Phase 3: CSS Layout

`css
/* Layout strategy */
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;  /* Full viewport height */
}

main {
  flex: 1;            /* Push footer to bottom */
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
`

**Tasks:**
1. Set up CSS reset (or normalize)
2. Implement sticky footer using flexbox
3. Center the search area vertically and horizontally
4. Style the header (links on left/right, Sign In button)
5. Style the search input (rounded corners, shadow on focus)
6. Style buttons (background, hover effect, padding)
7. Style footer (gray background, link layout)

### Phase 4: Responsive Design

`css
/* Mobile first approach */
.search-container {
  width: 90%;        /* Full width on mobile */
  max-width: 584px;  /* Max width on desktop */
}

@media (min-width: 768px) {
  /* Adjust padding, font sizes for larger screens */
  .footer-links {
    flex-direction: row;
  }
}
`

**Tasks:**
1. Make search input width responsive
2. Stack footer links vertically on mobile, horizontal on desktop
3. Adjust font sizes for readability on small screens
4. Test on 320px, 375px, 768px, 1024px, 1440px widths
5. Ensure touch targets are at least 44x44px on mobile

### Phase 5: Interactivity (Optional)

`javascript
// Vanilla JS for search functionality
document.querySelector('.search-form').addEventListener('submit', (e) => {
  e.preventDefault();
  const query = document.querySelector('.search-input').value;
  if (query.trim()) {
    window.location.href = https://www.google.com/search?q=;
  }
});

// I'm Feeling Lucky button
document.querySelector('.lucky-btn').addEventListener('click', () => {
  const query = document.querySelector('.search-input').value;
  if (query.trim()) {
    window.location.href = https://www.google.com/search?q=&btnI=1;
  }
});
`

**Tasks:**
1. Add form submission handling
2. Implement "I'm Feeling Lucky" redirect
3. Add keyboard shortcut (Escape to clear input, Enter to search)
4. Add focus management for accessibility

### Phase 6: Polish

**Tasks:**
1. Run Lighthouse audit - target 90+ in all categories
2. Add meta tags for SEO (description, Open Graph)
3. Add favicon (Google-style colored "g" or simple icon)
4. Test keyboard navigation (Tab through all elements)
5. Test with screen reader (NVDA or VoiceOver)
6. Test in Chrome, Firefox, Safari, Edge
7. Add smooth transitions on hover/focus
8. Final Git commit with descriptive message

## Expected Output Description

`
┌─────────────────────────────────────────────────────────┐
│  [Gmail] [Images]  [Sign In]                            │
│                                                         │
│                                                         │
│                         Google                          │
│                    (Google logo)                        │
│                                                         │
│             ┌──────────────────────────┐                │
│             │  Search Google or type a │                │
│             │  URL               [mic]│                │
│             └──────────────────────────┘                │
│                                                         │
│          [Google Search]  [I'm Feeling Lucky]           │
│                                                         │
│                                                         │
│                                                         │
│  ───────────────────────────────────────────────────    │
│  India                   Advertising Business           │
│                          How Search Works               │
│                          Privacy Terms Settings         │
└─────────────────────────────────────────────────────────┘
`

The final page should look visually similar to google.com with:
- Proper spacing and alignment
- Hover effects on all clickable elements
- Responsive layout that works on all screen sizes
- Clean, accessible markup
- 90+ Lighthouse scores

## Evaluation Criteria

| Criteria | Weight | What We Look For |
|----------|--------|------------------|
| **HTML Semantics** | 15% | Proper use of header, main, footer, nav, form, button, section |
| **CSS Layout** | 25% | Flexbox/Grid usage, no hacks, responsive without media query abuse |
| **Visual Fidelity** | 20% | Reasonable resemblance to Google (spacing, colors, alignment) |
| **Responsiveness** | 15% | Works on mobile through desktop, no horizontal scroll |
| **Accessibility** | 10% | Keyboard navigation, focus states, semantic HTML, alt text |
| **Performance** | 10% | Lighthouse score > 90, no render-blocking resources, efficient CSS |
| **Code Quality** | 5% | Clean formatting, meaningful comments, consistent naming |

### Submission Checklist

`
[ ] All HTML is semantic and valid
[ ] CSS is in external file
[ ] No JavaScript errors
[ ] Lighthouse: Performance > 90
[ ] Lighthouse: Accessibility > 90
[ ] Lighthouse: Best Practices > 90
[ ] Lighthouse: SEO > 90
[ ] Works on mobile (320px+) and desktop
[ ] All interactive elements have hover/focus states
[ ] Keyboard navigable
[ ] Git repo with meaningful commit messages
[ ] README with setup instructions and screenshot
`

## Bonus Challenges

1. **Dark mode**: Add prefers-color-scheme support
2. **Search suggestions**: Hard-code a few suggestions that appear as user types
3. **Google Doodle**: Replace logo with a simple CSS drawing or animated doodle
4. **Keyboard shortcuts**: Ctrl+K focuses search, Escape clears
5. **Micro-animations**: Logo scales slightly on page load, buttons have subtle press effect
6. **Accessibility**: Add aria-live region for search results count (even if simulated)

## Real-World Context

This project mirrors a common frontend interview task at companies like Google, Meta, and startups. It tests:
- Can you replicate a real interface pixel-perfect?
- Do you understand layout and responsive design?
- Do you care about accessibility and performance?
- Can you write clean, maintainable code?

The Google homepage looks simple, but its implementation reveals deep understanding of frontend fundamentals.
