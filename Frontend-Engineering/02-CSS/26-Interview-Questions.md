# CSS Interview Questions

## Q1: What is the difference between `display: none` and `visibility: hidden`?

**Answer:** `display: none` removes the element from the document flow entirely — it takes no space and is not rendered. `visibility: hidden` hides the element visually but preserves its space in the layout, affecting surrounding elements' positioning.

```css
.hidden { display: none; }          /* No space, not rendered */
.invisible { visibility: hidden; }  /* Space preserved, not visible */
```

## Q2: Explain the CSS box model.

**Answer:** Every element is a rectangular box consisting of (inside to outside): **content area** (text/images), **padding** (space around content, background extends here), **border** (line around padding), **margin** (invisible space outside border, pushes other elements). With `box-sizing: content-box` (default), `width`/`height` only include content. With `border-box`, they include content + padding + border.

## Q3: What is specificity and how is it calculated?

**Answer:** Specificity is a 4-part weight: **(inline, IDs, classes, elements)**. Inline styles = 1,0,0,0; IDs = 0,1,0,0; classes/attributes/pseudo-classes = 0,0,1,0; elements/pseudo-elements = 0,0,0,1. Higher specificity wins. `!important` overrides specificity (avoid).

## Q4: What is the cascade in CSS?

**Answer:** The cascade is the algorithm resolving conflicting CSS declarations. It considers (in order): **importance** (`!important`), **origin** (browser < user < author), **specificity** (more specific wins), and **source order** (later overrides earlier when specificity is equal).

## Q5: Explain `position: relative`, `absolute`, `fixed`, and `sticky`.

**Answer:**
- **`static`**: Normal flow (default), z-index/offset ignored.
- **`relative`**: Normal flow offset from its own position; original space preserved.
- **`absolute`**: Removed from flow, positioned relative to nearest positioned ancestor.
- **`fixed`**: Removed from flow, positioned relative to viewport (stays on scroll).
- **`sticky`**: Normal flow until scroll threshold, then becomes fixed within parent.

## Q6: What is a stacking context and how is one created?

**Answer:** A stacking context is a group of elements painted together along the Z-axis. z-index values are relative within a context. New contexts are created by: `position` + non-auto `z-index`, `opacity < 1`, `transform`, `filter`, `perspective`, `clip-path`, `mix-blend-mode`, `isolation: isolate`, `will-change`, `contain: paint/layout`.

## Q7: Explain flexbox vs CSS grid — when to use which?

**Answer:** Flexbox is **one-dimensional** (row OR column) — best for navigation, centering, small components, content-driven layouts. Grid is **two-dimensional** (rows AND columns) — best for page layouts, card grids, dashboard panels, where you need precise control over both axes simultaneously. Use flexbox for content-out layouts; grid for layout-in.

## Q8: What is `box-sizing: border-box` and why use it?

**Answer:** With `border-box`, `width`/`height` include padding and border in the specified size. This makes sizing predictable and easier — `width: 100%` stays within the parent regardless of padding/border. Global best practice: `*, *::before, *::after { box-sizing: border-box; }`.

## Q9: What are CSS custom properties and how do they differ from Sass variables?

**Answer:** CSS custom properties (`--var`) are **live** in the browser — they respect the cascade, can be changed in media queries, manipulated with JavaScript, and inherited through the DOM. Sass variables are compiled away — static, no media query changes, no JS access. Use CSS custom properties for theming and dynamic values.

## Q10: How do you create a dark mode toggle with CSS?

**Answer:** Use CSS custom properties with a data attribute or class:

```css
:root { --bg: white; --text: black; }
[data-theme="dark"] { --bg: black; --text: white; }
body { background: var(--bg); color: var(--text); }
```

JavaScript: `document.documentElement.dataset.theme = 'dark'`. Also respect `prefers-color-scheme`.

## Q11: What is BEM and what are its benefits?

**Answer:** BEM (Block Element Modifier) is a naming convention: `.block__element--modifier`. It provides clear hierarchy, prevents naming conflicts, makes relationships visible in HTML, and scales well in teams. Example: `.card__title--large`.

## Q12: Explain the `fr` unit in CSS Grid.

**Answer:** `fr` (fraction) distributes available space proportionally. `grid-template-columns: 1fr 2fr` creates two columns where the second is twice as wide as the first. Unlike `%`, `fr` excludes gap sizes, making calculations simpler.

## Q13: What is margin collapse and how do you prevent it?

**Answer:** Vertical margins of adjacent block elements collapse to the larger value. Example: a `<div>` with `margin-bottom: 30px` followed by one with `margin-top: 20px` results in 30px gap (not 50px). Prevent with: `overflow: auto` on parent, `display: flow-root`, `display: flex`, `padding: 1px` on parent.

## Q14: How do you center a div?

**Answer:** Multiple ways:
- **Flexbox**: `display: flex; justify-content: center; align-items: center;`
- **Grid**: `display: grid; place-items: center;`
- **Absolute**: `position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);`
- **Margin auto (block)**: `margin-inline: auto; width: fit-content;`

## Q15: What is `:has()` and how is it useful?

**Answer:** `:has()` is a CSS relational pseudo-class (the "parent selector"). It selects elements that contain a specific child or descendant. Example: `.card:has(img)` selects cards containing an image. Useful for styling parents based on children, avoiding JavaScript.

## Q16: Explain `prefers-reduced-motion` and why it matters.

**Answer:** It's a media query that detects if the user has requested reduced motion in their OS settings. Websites should respect this by disabling or minimizing animations for accessibility (vestibular disorders, seizure prevention):

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation-duration: 0.01ms !important; }
}
```

## Q17: What are container queries and what problem do they solve?

**Answer:** Container queries (`@container`) allow styling elements based on their **parent container's** size rather than the viewport. This solves the problem of reusable components that need to adapt to different containers (sidebar vs full-width), which media queries cannot address because they only know viewport size.

## Q18: How do you create a responsive grid without media queries?

**Answer:** Use `auto-fill`/`auto-fit` with `minmax()`:

```css
.grid { grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); }
```

This automatically adjusts column count based on available space.

## Q19: What is the difference between `em` and `rem`?

**Answer:** `rem` (root em) is relative to the root element's (`html`) font-size — consistent everywhere. `em` is relative to the **parent** font-size — compound, can lead to unexpected sizing with nesting. Use `rem` for most sizing, `em` for local scaling (like buttons relative to their container).

## Q20: Explain `will-change` and its uses.

**Answer:** `will-change` is a hint to the browser that an element will change a specific property, allowing it to optimize ahead of time. Example: `will-change: transform`. Use sparingly — can consume GPU memory if overused or applied to too many elements. Don't use on elements that won't actually change.

## Q21: What is critical CSS and how do you implement it?

**Answer:** Critical CSS refers to above-the-fold styles needed for initial render. Inline them in `<head>` to eliminate render-blocking requests, then load the full CSS asynchronously: `<link rel="stylesheet" href="styles.css" media="print" onload="this.media='all'">`.

## Q22: How do CSS Modules scope styles?

**Answer:** CSS Modules generate unique class names at build time (e.g., `._button_1a2b3`). When you import `styles from './button.module.css'` and use `styles.button`, the compiled class name includes a hash. This prevents global conflicts without runtime cost.

## Q23: Explain `clamp()`, `min()`, and `max()`.

**Answer:** `clamp(MIN, PREFERRED, MAX)` clamps a value between min and max. `min(A, B)` chooses the smaller. `max(A, B)` chooses the larger. Used for fluid typography: `font-size: clamp(1rem, 2vw + 1rem, 3rem)`.

## Q24: What is the difference between `overflow: hidden` and `overflow: clip`?

**Answer:** Both clip content. `overflow: hidden` allows programmatic scrolling (`scrollTop`/`scrollLeft`), while `clip` **completely forbids** scrolling, providing better performance guarantees. Use `clip` when you want to prevent all scrolling.

## Q25: How do you handle long words breaking layout?

**Answer:** Use `overflow-wrap: break-word` to break long words that would overflow, or `word-break: break-all` for aggressive breaking (useful for URLs). Multi-line truncation: `-webkit-line-clamp: 2` with `display: -webkit-box` and `overflow: hidden`.

## Q26: Explain `isolation: isolate` in CSS.

**Answer:** `isolation: isolate` creates a new stacking context without any visual side effects. Unlike `transform`, `opacity`, or `filter` which create contexts but also change appearance, `isolation` is a clean way to contain child elements within their own stacking context.

## Q27: What is the `contain` property and when would you use it?

**Answer:** `contain` tells the browser that an element's subtree is independent from the rest of the page. Values: `contain: layout` (internal layout changes don't affect outside), `contain: paint` (clips to bounds), `contain: size` (element can be sized without calculating children), `contain: content` (layout + paint + style), `contain: strict` (all). Improves performance for widgets, off-screen content.

## Q28: What is `@layer` and how does it solve cascade issues?

**Answer:** `@layer` allows explicit control over the cascade order by grouping styles into named layers. Layers defined later override those defined earlier, regardless of specificity. This prevents third-party CSS from overriding your styles:

```css
@layer reset, base, components, utilities;
@layer base { h1 { margin: 0; } }
@layer utilities { h1 { margin: 1rem; } } /* wins */
```

## Q29: Explain the difference between `auto-fill` and `auto-fit` in CSS Grid.

**Answer:** Both create as many tracks as fit. `auto-fill` keeps empty tracks (preserving track space). `auto-fit` collapses empty tracks to 0, allowing items to expand. With 4 items in space for 6 tracks: `auto-fill` shows 6 tracks (2 empty), `auto-fit` shows 4 tracks (items grow).

## Q30: How do you optimize CSS for performance?

**Answer:** Use `transform`/`opacity` for animations (GPU-composited), avoid expensive selectors (universal key selectors), minimize layout thrashing, use `contain` for widgets, inline critical CSS, purge unused CSS, use `content-visibility: auto` for off-screen sections, keep specificity low, and minimize repaint areas.
