# HTML Cheat Sheet

## Document Structure

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title</title>
</head>
<body>
    <!-- visible content -->
</body>
</html>
```

## Tags by Category

### Document Metadata

| Tag | Description |
|-----|-------------|
| `<base>` | Base URL for relative links |
| `<head>` | Container for metadata |
| `<link>` | External resource (CSS, favicon) |
| `<meta>` | Metadata (charset, viewport, SEO) |
| `<style>` | Internal CSS |
| `<title>` | Page title (browser tab, search results) |

### Content Sectioning

| Tag | Description |
|-----|-------------|
| `<address>` | Contact information |
| `<article>` | Self-contained content |
| `<aside>` | Related content (sidebar) |
| `<footer>` | Page/section footer |
| `<header>` | Page/section header |
| `<h1>`-`<h6>` | Heading levels (h1 highest) |
| `<main>` | Primary content (use once) |
| `<nav>` | Navigation links |
| `<section>` | Thematic grouping |

### Text Content

| Tag | Description |
|-----|-------------|
| `<blockquote>` | Block quotation |
| `<dd>` | Description definition |
| `<div>` | Generic container (block) |
| `<dl>` | Description list |
| `<dt>` | Description term |
| `<figcaption>` | Figure caption |
| `<figure>` | Self-contained media |
| `<hr>` | Thematic break |
| `<li>` | List item |
| `<menu>` | Menu list (alternative to `<ul>`) |
| `<ol>` | Ordered list |
| `<p>` | Paragraph |
| `<pre>` | Preformatted text |
| `<ul>` | Unordered list |

### Inline Text

| Tag | Description |
|-----|-------------|
| `<a>` | Hyperlink |
| `<abbr>` | Abbreviation |
| `<b>` | Stylistic offset (bold) |
| `<bdi>` | Bi-directional text isolation |
| `<bdo>` | Bi-directional text override |
| `<br>` | Line break |
| `<cite>` | Citation |
| `<code>` | Code snippet |
| `<data>` | Machine-readable data |
| `<dfn>` | Definition |
| `<em>` | Emphasized text |
| `<i>` | Voice/technical term |
| `<kbd>` | Keyboard input |
| `<mark>` | Highlighted text |
| `<q>` | Inline quotation |
| `<rp>` | Ruby parentheses |
| `<rt>` | Ruby text annotation |
| `<ruby>` | Ruby annotation |
| `<s>` | Strikethrough |
| `<samp>` | Sample output |
| `<small>` | Side comments (fine print) |
| `<span>` | Generic container (inline) |
| `<strong>` | Strong importance |
| `<sub>` | Subscript |
| `<sup>` | Superscript |
| `<time>` | Date/time |
| `<u>` | Unarticulated annotation |
| `<var>` | Variable |
| `<wbr>` | Word break opportunity |

### Image & Multimedia

| Tag | Description |
|-----|-------------|
| `<area>` | Area in image map |
| `<audio>` | Sound content |
| `<canvas>` | Drawing surface (JS) |
| `<img>` | Image |
| `<map>` | Image map |
| `<picture>` | Responsive images |
| `<source>` | Media source (for audio, video, picture) |
| `<svg>` | Vector graphics |
| `<track>` | Text track (captions, subtitles) |
| `<video>` | Video content |

### Embedded Content

| Tag | Description |
|-----|-------------|
| `<embed>` | External plugin |
| `<iframe>` | Inline frame |
| `<object>` | External object |
| `<param>` | Parameter for `<object>` |

### Scripting

| Tag | Description |
|-----|-------------|
| `<canvas>` | Scriptable graphics |
| `<noscript>` | Fallback when JS disabled |
| `<script>` | JavaScript |

### Forms

| Tag | Description |
|-----|-------------|
| `<button>` | Clickable button |
| `<datalist>` | Input suggestions |
| `<fieldset>` | Group of form controls |
| `<form>` | Form container |
| `<input>` | Input control (many types) |
| `<label>` | Input label |
| `<legend>` | Fieldset caption |
| `<meter>` | Scalar measurement |
| `<optgroup>` | Option group |
| `<option>` | Dropdown option |
| `<output>` | Calculation result |
| `<progress>` | Progress bar |
| `<select>` | Dropdown list |
| `<textarea>` | Multi-line text |

### Tabular Data

| Tag | Description |
|-----|-------------|
| `<caption>` | Table title |
| `<col>` | Column properties |
| `<colgroup>` | Column group |
| `<table>` | Table container |
| `<tbody>` | Table body |
| `<td>` | Table data cell |
| `<tfoot>` | Table footer |
| `<th>` | Table header cell |
| `<thead>` | Table header |
| `<tr>` | Table row |

## Global Attributes

Available on **all** HTML elements:

| Attribute | Description |
|-----------|-------------|
| `class` | CSS class name(s) |
| `id` | Unique identifier |
| `style` | Inline CSS |
| `title` | Tooltip text |
| `lang` | Language code |
| `dir` | Text direction (`ltr`, `rtl`) |
| `hidden` | Hide element |
| `tabindex` | Tab order |
| `accesskey` | Keyboard shortcut |
| `contenteditable` | Make editable |
| `draggable` | Enable drag |
| `spellcheck` | Enable spell check |
| `translate` | Enable translation |
| `data-*` | Custom data attributes |
| `role` | ARIA role |
| `aria-*` | ARIA attributes |

## Input Types

| Type | Description |
|------|-------------|
| `text` | Single-line text |
| `password` | Masked text |
| `email` | Email (validates) |
| `number` | Numeric (with stepper) |
| `tel` | Telephone |
| `url` | URL (validates) |
| `search` | Search field |
| `date` | Date picker |
| `datetime-local` | Date + time |
| `time` | Time picker |
| `month` | Month picker |
| `week` | Week picker |
| `color` | Color picker |
| `file` | File upload |
| `range` | Slider |
| `checkbox` | Multiple choices |
| `radio` | Single choice |
| `hidden` | Non-visible data |
| `submit` | Submit button |
| `reset` | Reset button |
| `button` | Generic button |

## Form Attributes

### Input Attributes

| Attribute | Description |
|-----------|-------------|
| `type` | Input type |
| `name` | Field name (submitted with form) |
| `value` | Default value |
| `placeholder` | Hint text |
| `required` | Must be filled |
| `readonly` | Cannot edit |
| `disabled` | Cannot interact |
| `minlength`/`maxlength` | Character limits |
| `min`/`max` | Numeric/date limits |
| `step` | Increment step |
| `pattern` | Regex validation |
| `autocomplete` | Autofill hint |
| `autofocus` | Auto-focus on load |
| `multiple` | Allow multiple values |
| `accept` | File type filter (for file inputs) |

### `<form>` Attributes

| Attribute | Description |
|-----------|-------------|
| `action` | Submission URL |
| `method` | `GET` or `POST` |
| `enctype` | Encoding (use `multipart/form-data` for files) |
| `novalidate` | Disable validation |
| `autocomplete` | `on`/`off` |
| `target` | Response window |

## HTML Entities

| Character | Entity | Decimal |
|-----------|--------|---------|
| `&` | `&amp;` | `&#38;` |
| `<` | `&lt;` | `&#60;` |
| `>` | `&gt;` | `&#62;` |
| `"` | `&quot;` | `&#34;` |
| `'` | `&apos;` | `&#39;` |
| ` ` (non-breaking) | `&nbsp;` | `&#160;` |
| `©` | `&copy;` | `&#169;` |
| `®` | `&reg;` | `&#174;` |
| `™` | `&trade;` | `&#8482;` |
| `€` | `&euro;` | `&#8364;` |
| `£` | `&pound;` | `&#163;` |
| `¥` | `&yen;` | `&#165;` |
| `•` | `&bull;` | `&#8226;` |
| `←` | `&larr;` | `&#8592;` |
| `→` | `&rarr;` | `&#8594;` |
| `↑` | `&uarr;` | `&#8593;` |
| `↓` | `&darr;` | `&#8595;` |
| `✓` | `&check;` | `&#10003;` |

## Semantic Landmarks

| Element | Default Role | Key |
|---------|-------------|-----|
| `<header>` | `banner` | (in body context) |
| `<footer>` | `contentinfo` | (in body context) |
| `<main>` | `main` | One per page |
| `<nav>` | `navigation` | Primary navigation |
| `<aside>` | `complementary` | Sidebar |
| `<article>` | `article` | Self-contained |
| `<section>` | `region` | (with accessible name) |
| `<form>` | `form` | |
| `<table>` | `table` | |

## Responsive Images

```html
<!-- Art direction -->
<picture>
    <source media="(min-width: 800px)" srcset="large.jpg">
    <source media="(min-width: 400px)" srcset="medium.jpg">
    <img src="small.jpg" alt="Description">
</picture>

<!-- Resolution switching -->
<img src="image-800.jpg"
     srcset="image-400.jpg 400w, image-800.jpg 800w, image-1200.jpg 1200w"
     sizes="(max-width: 600px) 100vw, 800px"
     alt="Description"
     loading="lazy">
```

## Media Elements

```html
<audio controls>
    <source src="audio.mp3" type="audio/mpeg">
</audio>

<video controls width="640" height="360" poster="thumb.jpg">
    <source src="video.mp4" type="video/mp4">
    <track kind="captions" src="captions.vtt" srclang="en">
</video>
```

## ARIA Quick Reference

```html
<!-- Labels -->
aria-label="Description"
aria-labelledby="element-id"

<!-- Descriptions -->
aria-describedby="desc-id"

<!-- States -->
aria-expanded="true/false"
aria-selected="true/false"
aria-checked="true/false"
aria-disabled="true/false"
aria-hidden="true/false"
aria-current="page"

<!-- Live regions -->
aria-live="polite/assertive/off"
aria-atomic="true/false"
aria-relevant="additions removals text"

<!-- Relationships -->
aria-controls="id"
aria-owns="id"

<!-- Roles -->
role="button dialog alert navigation main ..."
```

## Key SEO Meta Tags

```html
<meta name="description" content="Page description (max 160 chars)">
<meta name="robots" content="index, follow">
<link rel="canonical" href="https://example.com/page">

<!-- Open Graph -->
<meta property="og:title" content="Title">
<meta property="og:description" content="Description">
<meta property="og:image" content="https://example.com/image.jpg">
<meta property="og:url" content="https://example.com/page">

<!-- Twitter -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Title">
<meta name="twitter:description" content="Description">
<meta name="twitter:image" content="https://example.com/image.jpg">
```

## Deprecated (Avoid)

| Tag | Use Instead |
|-----|-------------|
| `<center>` | CSS `text-align: center` |
| `<font>` | CSS `font-*` properties |
| `<big>` | CSS `font-size` |
| `<strike>` | `<s>` or CSS `text-decoration` |
| `<tt>` | CSS `font-family: monospace` |
| `<u>` | CSS `text-decoration: underline` |
| `<frame>`/`<frameset>` | Use `<iframe>` |
| `<nobr>` | CSS `white-space: nowrap` |
| `<marquee>` | CSS animations |
| `<blink>` | CSS animations |
| `align`, `bgcolor`, `border` (on tables) | CSS |
