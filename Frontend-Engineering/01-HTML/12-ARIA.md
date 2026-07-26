# ARIA (Accessible Rich Internet Applications)

## What is ARIA?

ARIA is a set of attributes that supplement HTML to improve accessibility for assistive technologies. It provides **roles**, **states**, and **properties** that describe elements when native HTML semantics are insufficient.

```
┌─────────────────────────────────────────────────────────────┐
│  ARIA Rule of Thumb:                                        │
│                                                             │
│  "Use native HTML semantics first.                          │
│   Only use ARIA if the HTML element doesn't exist           │
│   or can't convey the necessary semantics."                 │
│                                                             │
│  ✅ <button>Click me</button>                                │
│                    (instead of role="button" on a <div>)     │
│  ✅ <nav>...</nav>                                            │
│                    (instead of role="navigation")            │
│  ✅ <input type="checkbox">                                  │
│                    (instead of role="checkbox")              │
│  ❌ <div role="button" tabindex="0">Click me</div>           │
│                    (use native <button>!)                    │
└─────────────────────────────────────────────────────────────┘
```

## ARIA Roles

### Landmark Roles

| Role | Native HTML Equivalent | When to Use |
|------|----------------------|-------------|
| `banner` | `<header>` (in body context) | Page header |
| `navigation` | `<nav>` | Navigation sections |
| `main` | `<main>` | Primary content |
| `complementary` | `<aside>` | Supporting content |
| `contentinfo` | `<footer>` (in body context) | Page footer |
| `region` | `<section>` (with accessible name) | Notable section |
| `form` | `<form>` | Form container |
| `search` | (none) | Search functionality |

### Widget Roles

| Role | Purpose | Requires |
|------|---------|----------|
| `button` | Clickable element | Keyboard handling |
| `link` | Navigational element | `href` or click handler |
| `checkbox` | Toggle option | `aria-checked` |
| `radio` | Radio button | `aria-checked`, group |
| `switch` | On/off toggle | `aria-checked` |
| `slider` | Range input | `aria-valuenow`, `aria-valuemin`, `aria-valuemax` |
| `tab` | Tab in tab list | `aria-selected`, `tabindex` |
| `tabpanel` | Content panel for tab | `aria-labelledby` |
| `tooltip` | Contextual help | Shown on hover/focus |

### Document Structure Roles

| Role | Description |
|------|-------------|
| `heading` | Heading (use `<h1>`-`<h6>` instead) |
| `list` | List (use `<ul>`/`<ol>` instead) |
| `listitem` | List item (use `<li>` instead) |
| `table` | Table (use `<table>` instead) |
| `row` | Table row (use `<tr>` instead) |
| `cell` | Table cell (use `<td>` instead) |
| `gridcell` | Cell in a grid |
| `toolbar` | Toolbar container |

### Live Region Roles

| Role | Description | Behavior |
|------|-------------|----------|
| `alert` | Important message | Announces immediately |
| `status` | Status bar | Announces when idle |
| `log` | Chat log, updates | Announces new entries |
| `marquee` | Scrolling text | Not typically announced |
| `timer` | Countdown timer | Announces at intervals |
| `progressbar` | Progress indicator | Announces percentage changes |

## ARIA States and Properties

### States (change with interaction)

| Attribute | Values | Used On |
|-----------|--------|---------|
| `aria-checked` | `true`, `false`, `mixed` | Checkbox, radio, switch |
| `aria-disabled` | `true`, `false` | Disabled elements |
| `aria-expanded` | `true`, `false` | Accordion, dropdown |
| `aria-hidden` | `true`, `false` | Hidden content |
| `aria-pressed` | `true`, `false`, `mixed` | Toggle buttons |
| `aria-selected` | `true`, `false` | Tab, gridcell |
| `aria-current` | `page`, `step`, `location`, `date`, `time`, `true`, `false` | Active link |

### Properties (describe characteristics)

| Attribute | Purpose |
|-----------|---------|
| `aria-label` | Provides an accessible name (overrides visible text) |
| `aria-labelledby` | References another element as the label |
| `aria-describedby` | References another element as the description |
| `aria-controls` | References element(s) this controls |
| `aria-owns` | References child elements (parent-child relationship) |
| `aria-activedescendant` | Currently active child in a composite widget |
| `aria-placeholder` | Hint text (use HTML `placeholder` instead) |
| `aria-valuenow` | Current value of a range widget |
| `aria-valuemin` | Minimum value |
| `aria-valuemax` | Maximum value |
| `aria-valuetext` | Human-readable value text |
| `aria-modal` | Whether a dialog is modal |
| `aria-haspopup` | `true`, `menu`, `listbox`, `tree`, `grid`, `dialog` |
| `aria-sort` | `ascending`, `descending`, `none`, `other` |
| `aria-roledescription` | Human-readable role description |
| `aria-live` | `off`, `polite`, `assertive` |

## When to Use (and NOT Use) ARIA

### ✅ When to Use ARIA

```html
<!-- 1. Add labels when no visible label exists -->
<button aria-label="Close" onclick="closeDialog()">
    ✕
</button>

<!-- 2. Describe relationships -->
<input type="password" id="password"
       aria-describedby="password-rules">
<p id="password-rules">Must be at least 8 characters</p>

<!-- 3. Live regions for dynamic content -->
<div aria-live="polite" aria-atomic="true"
     id="notifications">
    <!-- New notifications appear here -->
</div>

<!-- 4. Complex widgets (tabs, accordions) -->
<div role="tablist" aria-label="Product information">
    <button role="tab" aria-selected="true"
            aria-controls="panel-1" id="tab-1">
        Description
    </button>
    <button role="tab" aria-selected="false"
            aria-controls="panel-2" id="tab-2">
        Reviews
    </button>
</div>
<div role="tabpanel" id="panel-1"
     aria-labelledby="tab-1">
    Product description content...
</div>

<!-- 5. Progress indicators -->
<div role="progressbar"
     aria-valuenow="60"
     aria-valuemin="0"
     aria-valuemax="100"
     aria-label="Loading progress">
    60%
</div>
```

### ❌ When NOT to Use ARIA

```html
<!-- 1. Don't override native semantics -->
<a role="button" href="/page">Link styled as button</a>
<!-- ✅ Use: <a href="/page">Link</a> or <button type="button">Button</button> -->

<!-- 2. Don't remove semantics unnecessarily -->
<h2 role="presentation">Heading</h2>
<!-- ✅ Use CSS to change appearance -->

<!-- 3. Don't use aria-hidden on focusable elements -->
<button aria-hidden="true">Submit</button>
<!-- ❌ aria-hidden elements should not be focusable -->

<!-- 4. Don't add redundant roles -->
<nav role="navigation">...</nav>
<!-- ✅ <nav> already has role="navigation" -->
<footer role="contentinfo">...</footer>
<!-- ✅ <footer> already has role="contentinfo" -->

<!-- 5. Don't use role="none" or role="presentation" on interactive elements -->
<button role="none">Click</button>
<!-- Removes button semantics — screen readers won't know it's a button -->
```

## aria-label vs aria-labelledby vs aria-describedby

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                     │
│  aria-label        "Close"          ─── Direct label on element     │
│  ───────────                                                       │
│  <button aria-label="Close">✕</button>                             │
│                                                                     │
│  aria-labelledby   Points to element ─── Uses existing text as label│
│  ────────────────────────────────                                   │
│  <h2 id="section-title">Pricing</h2>                               │
│  <section aria-labelledby="section-title">...</section>             │
│                                                                     │
│  aria-describedby  Points to element ─── Additional description     │
│  ──────────────────────────────────────                             │
│  <input aria-describedby="password-hint">                          │
│  <p id="password-hint">Must include a number</p>                   │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

```html
<!-- Priority: aria-labelledby > aria-label > native label -->
<button aria-label="Search" aria-labelledby="search-label">
    <!-- aria-labelledby wins, speaks "Find Products" -->
</button>
<p id="search-label">Find Products</p>
```

## Live Regions

Live regions announce dynamic content changes to screen readers.

```html
<!-- Polite (waits for idle) — use for non-critical updates -->
<div aria-live="polite" id="chat-messages">
    <!-- New messages will be announced when screen reader is idle -->
</div>

<!-- Assertive (interrupts) — use for critical updates -->
<div aria-live="assertive" id="error-messages">
    <!-- "Error: Invalid email format" — announced immediately -->
</div>

<!-- Off (default) — no announcements -->
<div aria-live="off" id="static-content">
    <!-- Changes are not announced -->
</div>
```

### `aria-atomic`

Controls whether the entire live region or just the changed part is announced.

```html
<!-- Announce entire region content -->
<div aria-live="polite" aria-atomic="true">
    5 items in cart
</div>

<!-- Announce only changed content (default) -->
<div aria-live="polite" aria-atomic="false">
    New message: Hello!
</div>
```

### `aria-relevant`

Controls which types of changes trigger announcements.

```html
<div aria-live="polite" aria-relevant="additions removals text">
    <!-- Announces: additions, removals, and text changes -->
</div>
```

| Value | Meaning |
|-------|---------|
| `additions` | Elements added to the DOM |
| `removals` | Elements removed from the DOM |
| `text` | Text content changes |
| `all` | All of the above |

## Common ARIA Patterns

### Accordion

```html
<div class="accordion">
    <h3>
        <button aria-expanded="false"
                aria-controls="section-1"
                id="accordion-header-1">
            Section 1
        </button>
    </h3>
    <div id="section-1"
         role="region"
         aria-labelledby="accordion-header-1"
         hidden>
        <p>Content for section 1...</p>
    </div>
</div>
```

### Modal Dialog

```html
<div role="dialog"
     aria-modal="true"
     aria-labelledby="dialog-title"
     aria-describedby="dialog-desc"
     id="my-dialog">
    <h2 id="dialog-title">Confirm Delete</h2>
    <p id="dialog-desc">Are you sure you want to delete this item?</p>
    <button onclick="closeDialog()">Cancel</button>
    <button onclick="deleteItem()">Delete</button>
</div>
```

### Error Summary

```html
<div role="alert" aria-live="assertive" id="form-errors">
    <h2>Please fix the following errors:</h2>
    <ul>
        <li><a href="#email">Email is required</a></li>
        <li><a href="#password">Password must be at least 8 characters</a></li>
    </ul>
</div>
```

## ARIA Authoring Practices Guide

The [W3C ARIA Authoring Practices Guide (APG)](https://www.w3.org/WAI/ARIA/apg/) provides complete patterns for:

- Accordions
- Tabs
- Carousels
- Dialogs (modals)
- Menus
- Tooltips
- Tree views
- Grids
- Sliders
- and more...

## Key Takeaways

1. **Use native HTML first** — ARIA is for when HTML can't express the semantics.
2. **Don't change native semantics** — `<a role="button">` removes link behavior.
3. **Don't add aria-hidden to focusable elements** — it's ignored.
4. **Use `aria-label`** for icon-only buttons and links.
5. **Use `aria-labelledby`** to associate sections with their headings.
6. **Use `aria-describedby`** for additional help text.
7. **Use `aria-live`** for dynamic content updates.
8. **Test with real screen readers** — ARIA bugs are common.

---

**Next:** [Best-Practices.md](Best-Practices.md) — HTML best practices and standards.
