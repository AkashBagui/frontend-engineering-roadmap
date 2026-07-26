# CSS Exercises

## Exercise 1: Center the Box

Recreate this layout using flexbox, grid, and absolute positioning (3 different solutions):

```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│              ┌─────────┐                │
│              │ centered │                │
│              └─────────┘                │
│                                         │
│                                         │
└─────────────────────────────────────────┘

Requirements:
- Box is 200x200px
- Always centered horizontally and vertically
- Container is full viewport
```

## Exercise 2: Holy Grail Layout

Build the Holy Grail layout using both flexbox and CSS grid:

```
┌─────────────────────────────────────────┐
│               HEADER                    │
├────────┬──────────────────┬─────────────┤
│        │                  │             │
│  NAV   │     MAIN         │   ASIDE     │
│        │                  │             │
├────────┴──────────────────┴─────────────┤
│               FOOTER                    │
└─────────────────────────────────────────┘

Requirements:
- Header and footer full width, 80px height
- Nav 200px, aside 200px
- Main fills remaining space
- min-height: 100vh
```

## Exercise 3: Card Component

Create a responsive card that works in different contexts:

```html
<div class="card">
  <img src="placeholder.jpg" alt="" class="card__image">
  <div class="card__body">
    <span class="card__tag">Technology</span>
    <h2 class="card__title">Card Title</h2>
    <p class="card__text">Description text here...</p>
    <button class="card__button">Read More</button>
  </div>
</div>
```

Requirements:
- Responsive: 300px width, full-width on mobile
- Hover effect: lift + shadow
- Image scales properly
- Button stays at bottom of card
- BEM naming

## Exercise 4: Navigation Bar

Build a responsive navigation bar:

```
Desktop:
[LOGO]    [Home] [About] [Services] [Contact]    [Login]

Mobile:
[☰] [LOGO]
  ↓ hamburger menu overlay
```

Requirements:
- Horizontal on desktop, hamburger on mobile
- Sticky at top
- Active link styling
- Smooth transition for mobile menu
- No JavaScript allowed (checkbox hack or `:target`)

## Exercise 5: CSS Art

Create a simple illustration using only CSS:

- A coffee cup with saucer
- A loading spinner
- A checkmark animation
- A profile avatar with status dot

Example — spinner:

```css
.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #ccc;
  border-top-color: #007bff;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
```

## Exercise 6: Fix This Layout

The following HTML has styling issues. Identify and fix them:

```html
<div class="parent">
  <div class="child">Child 1</div>
  <div class="child">Child 2</div>
  <div class="child">Child 3</div>
</div>
```

```css
.parent { width: 100%; background: lightgray; }
.child {
  display: inline-block;
  width: 33.33%;
  height: 100px;
  background: teal;
  color: white;
  text-align: center;
}
```

**Issues to fix:** Inline-block whitespace gap, 33.33% + gap > 100%, overflow

## Exercise 7: Responsive Table

Create a table that adapts to mobile by stacking rows:

```
Desktop:  | Name | Age | City | Score |
          | John | 25  | NYC  | 95    |

Mobile:
  Name: John
  Age: 25
  City: NYC
  Score: 95
```

Requirements:
- `data-label` attributes on each `<td>`
- `::before` shows the label on mobile
- Hide `<thead>` on mobile
- Table scrolls horizontally on medium screens

## Exercise 8: Sticky Footer

Create a page where the footer sticks to the bottom when content is short, but moves down when content is long:

```
Short page:                    Long page:
┌──────────┐                  ┌──────────┐
│ CONTENT  │                  │ CONTENT  │
│          │                  │ (lots)   │
│          │                  │          │
├──────────┤                  ├──────────┤
│ FOOTER   │                  │ FOOTER   │
└──────────┘                  └──────────┘
```

Requirements:
- Flexbox solution (no fixed height)
- Grid solution also acceptable
- Footer should never overlap content

## Exercise 9: Multi-Column Form

Create a form that adapts from 1 to 3 columns:

```
Desktop:                         Mobile:
┌───────┬───────┬───────┐      ┌─────────┐
│ Name  │ Email │ Phone │      │ Name    │
├───────┼───────┼───────┤      ├─────────┤
│  Subject                   │      │ Email   │
├────────────────────────────┤      ├─────────┤
│  Message                   │      │ Phone   │
│                            │      ├─────────┤
├────────────────────────────┤      │ Subject │
│        [ Submit ]          │      ├─────────┤
└────────────────────────────┘      │ Message │
                                    ├─────────┤
                                    │ [Submit]│
                                    └─────────┘
```

## Exercise 10: Gradient Background

Create these gradient effects:

1. **Sunset**: `linear-gradient(top to bottom)` from #ff7e5f to #feb47b
2. **Neon glow**: `radial-gradient` with bright center fading out
3. **Stripes**: `repeating-linear-gradient` angled at 45deg
4. **Rainbow**: `conic-gradient` with all hues

## Exercise 11: Responsive Typography

Implement a fluid typography system using `clamp()`:

```css
/* Create these fluid sizes */
--text-sm: clamp(0.75rem, 1vw, 0.875rem);
--text-base: clamp(1rem, 2vw, 1.125rem);
--text-lg: clamp(1.25rem, 3vw, 1.5rem);
--text-xl: clamp(1.5rem, 4vw, 2rem);
--text-2xl: clamp(2rem, 5vw, 3rem);
```

Apply to a sample page with headings, body text, and captions.

## Exercise 12: Theme Toggle

Build a dark/light theme toggle:

Requirements:
- CSS custom properties for colors
- `prefers-color-scheme` as default
- Manual toggle via checkbox or button
- Smooth transitions on color change
- Store preference (challenge: localStorage)

## Exercise 13: CSS Grid Photo Gallery

Create a gallery with varied image sizes:

```
┌───┬───┬───┬───┐
│ 1 │ 2 │ 3 │ 4 │
├───┼───┴───┼───┤
│ 5 │   6   │ 7 │
├───┼───┬───┼───┤
│ 8 │ 9 │10 │11 │
└───┴───┴───┴───┘

Cell 6 is 2x1, the rest are 1x1.
```

Requirements:
- Grid with 4 columns
- Featured image spans 2 columns
- Responsive: collapses to 2 columns on tablet, 1 on mobile
- Hover effect on images

## Exercise 14: Tooltip

Create a CSS-only tooltip that appears on hover:

```
        ┌──────────┐
        │ Tooltip  │
        └──────────┘
            ↓
    [ Hover me ]
```

Requirements:
- `::before` and `::after` pseudo-elements
- Positioned above the trigger
- Arrow at bottom of tooltip
- Appears with fade-in transition
- No JavaScript

## Exercise 15: Animations

Create these animations:

1. **Bounce**: Ball that bounces up and down
2. **Shimmer**: Loading skeleton with moving highlight
3. **Fade-in + slide-up**: Element enters from below while fading in
4. **Pulse**: Heartbeat-like scale pulse
5. **Typing**: Text appears character by character

Each animation should:
- Use `@keyframes`
- Have appropriate timing functions
- Respect `prefers-reduced-motion`

## Exercise 16: CSS-Only Accordion

Build an FAQ accordion using only CSS:

```
▼ Question 1?          (open)
    Answer text here...
▶ Question 2?          (closed)
▶ Question 3?          (closed)
```

Requirements:
- Use `<details>` / `<summary>` elements
- Custom chevron icon using `::after`
- Smooth animation on open/close
- Styled transitions

## Exercise 17: Z-Index Puzzle

Given this HTML, determine what z-index values make each element visible:

```html
<div class="a">
  <div class="a1">Text A1</div>
  <div class="a2">Text A2</div>
</div>
<div class="b">
  <div class="b1">Text B1</div>
  <div class="b2">Text B2</div>
</div>
```

Current styles:
```css
.a { position: relative; z-index: 10; }
.b { position: relative; z-index: 1; }
.a1 { position: absolute; z-index: 999; }
.b1 { position: absolute; z-index: 100; }
```

**Question:** Which text appears on top? **Answer:** B1 (z:100 relative to B which has higher stacking context than A in the root). A1's z-index 999 is within A's context.

**Challenge:** Modify it so that A1 appears above everything.

## Exercise 18: Recreate a UI

Pick a real UI element from a popular website and recreate it using only CSS:

- **Google Search button** (hover/focus states)
- **Apple product card** (image + gradient text)
- **GitHub contribution graph** (grid of colored squares)
- **Spotify playlist card** (rounded cover + green play button on hover)
- **Twitter/X profile card** (header image, avatar, bio, stats)

## Exercise 19: Container Query Component

Create a component that adapts based on container size:

```html
<div class="container narrow">
  <div class="component">...</div>
</div>
<div class="container wide">
  <div class="component">...</div>
</div>
```

Requirements:
- Component uses `@container` queries
- Adapts layout (column vs row) based on container width
- Uses container query units (`cqi`)
- Test by resizing containers

## Exercise 20: Print Stylesheet

Create a print-friendly stylesheet that:

- Hides navigation, ads, sidebar, buttons
- Shows URLs after links
- Breaks pages appropriately
- Optimizes font sizes for print
- Ensures backgrounds print

Apply to an article page and test with Print Preview.

## Exercise 21: Media Query Refactor

Take a non-responsive page and make it responsive:

```css
/* Given this desktop-first CSS */
.sidebar { width: 300px; float: left; }
.main { margin-left: 300px; }
.header { height: 80px; }
.grid-item { width: 25%; float: left; }
```

Refactor to be mobile-first. Use modern CSS (flexbox/grid). Add appropriate breakpoints.

## Exercise 22: CSS Architecture Refactor

Take a messy CSS file and organize it using BEM methodology:

```css
/* Messy */
.red-title { color: red; font-size: 24px; }
.blue-btn { background: blue; color: white; padding: 10px 20px; border-radius: 5px; }
.content-box { border: 1px solid gray; padding: 20px; }
.content-box h2 { margin: 0; color: #333; }
```

Refactor to:
- BEM naming (`.card__title`, `.button--primary`)
- CSS custom properties
- Proper cascade ordering
- Remove redundant selectors
