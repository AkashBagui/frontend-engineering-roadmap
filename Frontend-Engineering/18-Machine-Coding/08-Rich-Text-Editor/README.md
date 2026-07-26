# Rich Text Editor

**Difficulty:** Hard | **Est. Time:** 60–90 min

---

## Problem Statement

Build a rich text editor that supports basic formatting (bold, italic, underline), headings, lists (ordered/unordered), links, and image embedding. The editor should have a toolbar and produce clean HTML output.

---

## Requirements

### Functional
- [ ] Text formatting: Bold (Ctrl+B), Italic (Ctrl+I), Underline (Ctrl+U)
- [ ] Headings: H1, H2, H3
- [ ] Lists: Ordered (numbered), Unordered (bulleted)
- [ ] Insert links (text + URL)
- [ ] Insert images (URL input, display inline)
- [ ] Undo / Redo for formatting changes
- [ ] Toolbar with toggle buttons for each formatting option
- [ ] Output clean HTML or Markdown

### Non-Functional
- [ ] Preserve formatting across copy-paste (basic)
- [ ] Keyboard shortcuts (Ctrl+B, Ctrl+I, etc.)
- [ ] Selection must be preserved when applying formatting
- [ ] No XSS vulnerabilities

---

## contentEditable vs Rich Text Libraries

| Approach | Pros | Cons |
|----------|------|------|
| **contentEditable** | No dependencies, works natively | Inconsistent behavior across browsers; hard to control; messy HTML |
| **Slate.js** | Fully controlled, schema-based, great DX | Steep learning curve, larger bundle |
| **ProseMirror** | Robust, schema-based, collaborative-ready | Complex API, heavy |
| **TipTap** | Built on ProseMirror, simpler API | Still has ProseMirror complexity |

**For interview:** If library allowed → use Slate.js. If no libraries → contentEditable with `document.execCommand` (simple) or a custom state-based approach (advanced).

---

## Component Architecture

```
App
├── EditorToolbar
│   ├── FormatGroup
│   │   ├── BoldButton
│   │   ├── ItalicButton
│   │   └── UnderlineButton
│   ├── HeadingGroup
│   │   ├── H1Button
│   │   ├── H2Button
│   │   └── H3Button
│   ├── ListGroup
│   │   ├── OrderedListButton
│   │   └── UnorderedListButton
│   ├── LinkButton → LinkModal
│   └── ImageButton → ImageModal
├── EditorContent (contentEditable div or Slate component)
└── HTMLOutput (preview panel, read-only)
```

---

## State Management (contentEditable approach)

```js
const [editorRef, setEditorRef] = useState(null);
const [activeFormats, setActiveFormats] = useState({
  bold: false, italic: false, underline: false
});
const [activeHeading, setActiveHeading] = useState(null);
```

For **Slate**, the state is the editor's `Value` object — a JSON-compatible document tree.

---

## Command Pattern with document.execCommand

```js
function toggleFormat(format) {
  document.execCommand(format, false, null);
  editorRef.focus();
  updateActiveFormats();
}

function toggleHeading(tag) {
  document.execCommand('formatBlock', false, `<${tag}>`);
  editorRef.focus();
}

function insertLink(url) {
  const selection = window.getSelection();
  if (selection.toString()) {
    document.execCommand('createLink', false, url);
  } else {
    // Insert a link with display text
    const linkText = prompt('Link text:');
    if (linkText) {
      document.execCommand('insertHTML', false, `<a href="${url}">${linkText}</a>`);
    }
  }
}

function insertImage(url) {
  document.execCommand('insertImage', false, url);
}

function updateActiveFormats() {
  setActiveFormats({
    bold: document.queryCommandState('bold'),
    italic: document.queryCommandState('italic'),
    underline: document.queryCommandState('underline'),
  });
}
```

### Keyboard Shortcuts (already handled by browser for execCommand)
Add your own for custom actions:

```js
useEffect(() => {
  const handler = (e) => {
    if (e.ctrlKey && e.key === 'k') {
      e.preventDefault();
      openLinkModal();
    }
  };
  editorRef?.addEventListener('keydown', handler);
  return () => editorRef?.removeEventListener('keydown', handler);
}, [editorRef]);
```

---

## Implementation Steps

1. Set up editor shell with toolbar and content area
2. Make content area contentEditable and attach ref
3. Implement toolbar buttons using `document.execCommand` (bold, italic, underline)
4. Add heading buttons (formatBlock with H1/H2/H3)
5. Add list buttons (insertOrderedList, insertUnorderedList)
6. Implement link insertion (modal → createLink)
7. Implement image insertion (modal → insertImage)
8. Track active formats using `queryCommandState` on selection change
9. Add sanitizer for HTML output (DOMPurify or regex strip)
10. Add undo/redo (browser native for contentEditable)
11. Handle edge cases: empty editor, paste sanitization, disallowed content

---

## Code Snippets

### Tracking Active Formats

```js
function handleSelectionChange() {
  // Use setTimeout to let browser apply execCommand first
  setTimeout(() => {
    setActiveFormats({
      bold: document.queryCommandState('bold'),
      italic: document.queryCommandState('italic'),
      underline: document.queryCommandState('underline'),
    });
    // Check heading
    const value = document.queryCommandValue('formatBlock');
    setActiveHeading(['H1', 'H2', 'H3'].includes(value) ? value : null);
  });
}

// Attach to editor
<div
  ref={editorRef}
  contentEditable
  suppressContentEditableWarning
  onMouseUp={handleSelectionChange}
  onKeyUp={handleSelectionChange}
/>
```

### HTML Sanitizer

```js
function sanitizeHTML(html) {
  // Strip dangerous tags and attributes
  const doc = new DOMParser().parseFromString(html, 'text/html');
  const allowedTags = ['p', 'br', 'strong', 'em', 'u', 'h1', 'h2', 'h3', 'ul', 'ol', 'li', 'a', 'img'];
  const walker = document.createTreeWalker(doc.body, NodeFilter.SHOW_ELEMENT);
  while (walker.nextNode()) {
    const node = walker.currentNode;
    if (!allowedTags.includes(node.tagName.toLowerCase())) {
      node.replaceWith(...node.childNodes);
    }
    // Remove event handlers
    for (const attr of [...node.attributes]) {
      if (attr.name.startsWith('on')) node.removeAttribute(attr.name);
    }
  }
  return doc.body.innerHTML;
}
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Paste from Word/Google Docs | On paste, intercept event, strip styling, apply sanitizer |
| Insert HTML via paste | Strip script/iframe/onclick attributes with DOMPurify |
| Selection across multiple block types | Apply format to each selected block |
| Empty editor placeholder | Show "Type here..." pseudo-element when innerHTML is empty |
| Images with no alt text | Auto-generate alt from filename |
| Nested lists | execCommand handles nested lists with Tab / Shift+Tab |

---

## Bonus Features

- [ ] **Markdown shortcuts** (`#` → H1, `**bold**`, `*italic*`)
- [ ] **Text alignment** (left, center, right)
- [ ] **Code block** with syntax highlighting
- [ ] **Blockquote**
- [ ] **Drag to reorder** blocks (dnd-kit with Slate)
- [ ] **Collaborative editing** (Yjs + Slate/ProseMirror)
- [ ] **Export to Markdown / PDF**

---

## Common Interview Questions

1. **Why is contentEditable considered problematic?** — Browsers produce inconsistent HTML for the same action. Pasting from different sources introduces unpredictable markup. Selection and cursor positioning across browsers can be buggy.

2. **How does Slate.js work?** — Slate uses a controlled document tree (JSON) where every change is a normalized operation. It separates data from rendering, making it predictable and testable.

3. **How do you handle sanitization?** — Remove all HTML tags except a whitelist of safe ones. Strip `on*` event attributes. Never render user HTML without sanitization.

4. **How would you implement a custom undo/redo?** — Store snapshots of the document state before each mutation. For contentEditable, browser handles it. For controlled editors (Slate), maintain a history stack of operations.

---

## Resources

- [document.execCommand reference](https://developer.mozilla.org/en-US/docs/Web/API/Document/execCommand)
- [Slate.js docs](https://www.slatejs.org/)
- [DOMPurify](https://github.com/cure53/DOMPurify)
