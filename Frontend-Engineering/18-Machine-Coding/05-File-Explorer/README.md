# File Explorer

**Difficulty:** Medium | **Est. Time:** 45–60 min

---

## Problem Statement

Build a file explorer UI that displays a hierarchical directory tree, allows users to expand/collapse folders, and supports basic file/folder operations (create, delete, rename) with a context menu.

---

## Requirements

### Functional
- [ ] Tree view display with folders and files
- [ ] Expand / collapse folders (animated)
- [ ] Create new folder or file (right-click context menu or button)
- [ ] Delete file or folder (with confirmation)
- [ ] Rename file or folder (inline edit or context menu)
- [ ] Context menu on right-click (New Folder, New File, Rename, Delete)
- [ ] Show file icons based on extension
- [ ] Search / filter by name (bonus)

### Non-Functional
- [ ] Handle deep nesting (10+ levels) without performance issues
- [ ] Keyboard navigation (arrow keys, Enter to open)
- [ ] Lazy load children if large dataset (bonus)

---

## Data Structure (Tree)

```js
const fileTree = {
  id: 'root',
  name: 'Root',
  type: 'folder',
  children: [
    {
      id: 'src',
      name: 'src',
      type: 'folder',
      children: [
        { id: 'index.js', name: 'index.js', type: 'file' },
        { id: 'app.js', name: 'app.js', type: 'file' }
      ]
    },
    {
      id: 'readme.md',
      name: 'readme.md',
      type: 'file'
    }
  ]
};
```

---

## Component Architecture

```
App
├── Toolbar
│   ├── NewFolderButton
│   ├── NewFileButton
│   └── SearchInput
├── FileExplorer
│   └── TreeNode (recursive, ×N)
│       ├── FolderRow
│       │   ├── ExpandIcon (▶ / ▼)
│       │   ├── FolderIcon
│       │   ├── FolderName (editable on rename)
│       │   └── ContextMenuTrigger
│       └── ChildrenContainer (collapsible)
│           └── TreeNode (recursive children)
│       └── FileRow
│           ├── FileIcon (by extension)
│           ├── FileName (editable on rename)
│           └── ContextMenuTrigger
├── ContextMenu (positioned absolutely)
│   ├── New Folder
│   ├── New File
│   ├── Rename
│   ├── Delete
│   └── (optional) Copy / Cut / Paste
└── ConfirmDialog (for delete)
```

---

## State Management

```js
const [tree, setTree] = useState(initialTree);
const [expandedIds, setExpandedIds] = useState(new Set());
const [contextMenu, setContextMenu] = useState(null);  // { x, y, nodeId }
const [renamingId, setRenamingId] = useState(null);
const [selectedId, setSelectedId] = useState(null);
const [searchTerm, setSearchTerm] = useState('');
```

Use **Immer** or manual immutable updates for tree mutations (deep cloning is expensive).

---

## Implementation Steps

1. Build the recursive TreeNode component
2. Implement expand/collapse toggle (toggle ID in `expandedIds` set)
3. Add folder/file icons (use emoji or simple SVG icons)
4. Implement context menu (right-click handler, position menu, close on outside click)
5. Implement "New Folder" / "New File": prompt for name, insert into tree at selected node's children
6. Implement rename: double-click or context menu → input replaces name → blur/Enter saves
7. Implement delete: confirm dialog → recursive remove from tree
8. Add search / filter (recursive match, show matching nodes + expand parents)
9. Handle edge cases: empty directory, root node protection, duplicate names

---

## Code Snippets

### Recursive TreeNode Component

```jsx
function TreeNode({ node, depth = 0 }) {
  const isExpanded = expandedIds.has(node.id);
  const children = node.type === 'folder' ? getFilteredChildren(node, searchTerm) : [];

  return (
    <div>
      <div
        className="tree-row"
        style={{ paddingLeft: `${depth * 20}px` }}
        onContextMenu={(e) => handleContextMenu(e, node.id)}
        onClick={() => node.type === 'folder' && toggleExpand(node.id)}
      >
        {node.type === 'folder' && (
          <span className="expand-icon">{isExpanded ? '▼' : '▶'}</span>
        )}
        <FileIcon extension={getExtension(node.name)} />
        {renamingId === node.id ? (
          <RenameInput node={node} onComplete={finishRename} />
        ) : (
          <span className="node-name">{node.name}</span>
        )}
      </div>
      {node.type === 'folder' && isExpanded && children.map(child => (
        <TreeNode key={child.id} node={child} depth={depth + 1} />
      ))}
    </div>
  );
}
```

### Immutable Tree Insert

```js
function insertNode(tree, parentId, newNode) {
  if (tree.id === parentId) {
    return { ...tree, children: [...(tree.children || []), newNode] };
  }
  if (tree.children) {
    return { ...tree, children: tree.children.map(child => insertNode(child, parentId, newNode)) };
  }
  return tree;
}
```

### Recursive Tree Delete

```js
function removeNode(tree, nodeId) {
  if (tree.children) {
    return { ...tree, children: tree.children.filter(child => child.id !== nodeId).map(child => removeNode(child, nodeId)) };
  }
  return tree;
}
```

### Filter Tree for Search

```js
function filterTree(node, search) {
  if (!search) return node;
  const nameMatch = node.name.toLowerCase().includes(search.toLowerCase());
  const filteredChildren = node.children?.map(c => filterTree(c, search)).filter(Boolean);
  if (nameMatch || (filteredChildren && filteredChildren.length > 0)) {
    return { ...node, children: (nameMatch && node.children) ? node.children : filteredChildren };
  }
  return null;
}
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Empty folder | Show folder with no children; still expandable (shows empty state msg) |
| Duplicate names | Append `(1)`, `(2)` suffix automatically on create |
| Root node | Cannot delete or rename root |
| Deeply nested (10+ levels) | Recursion is fine; ensure expand/collapse doesn't re-render entire tree |
| Name with invalid chars | Strip or reject `/`, `\`, `:`, `*`, `?`, `"`, `<`, `>`, `|` |
| Rename to empty string | Revert to original name |
| Context menu outside viewport | Flip menu position to fit within window bounds |

---

## Bonus Features

- [ ] **Drag & drop** files between folders (dnd-kit sortable tree)
- [ ] **Copy / Cut / Paste** with clipboard
- [ ] **Multi-select** (Ctrl+click, Shift+click)
- [ ] **Breadcrumb navigation** bar
- [ ] **File preview** (click file → show content panel)
- [ ] **Lazy load** children from API instead of all-at-once
- [ ] **Undo / Redo** for tree mutations

---

## Common Interview Questions

1. **Why a recursive component for tree view?** — Trees are naturally recursive structures. A TreeNode component that renders itself for children keeps the code self-similar and easy to maintain.

2. **How do you handle immutable updates on nested data?** — Spread operator for each level, or use Immer. For performance, use normalized state (flat map + parent references).

3. **How do you prevent re-rendering the entire tree on expand/collapse?** — Memoize TreeNode with `React.memo` and keep expanded state as a separate Set (not inside the tree data).

4. **How to handle large directories (10k files)?** — Virtualize the tree (react-vtree), lazy load children on expand, use windowing for visible nodes.

---

## Resources

- [React recursive components](https://react.dev/learn/rendering-lists#keeping-list-items-in-order-with-key)
- [react-vtree](https://github.com/Lodin/react-vtree) (virtualized tree)
