# Kanban Board

**Difficulty:** Medium | **Est. Time:** 45–60 min

---

## Problem Statement

Build a Kanban-style project management board with columns representing task statuses (e.g., To Do, In Progress, Done). Users can create, read, update, and delete cards, and drag cards between columns.

---

## Requirements

### Functional
- [ ] Display multiple columns side by side (scrollable horizontally)
- [ ] Create a new card in any column (title + description)
- [ ] Edit card details inline
- [ ] Delete a card
- [ ] Drag a card from one column to another
- [ ] Reorder cards within the same column via drag
- [ ] Add / remove columns (optional but common)
- [ ] Persist board state to LocalStorage

### Non-Functional
- [ ] Smooth drag animations
- [ ] Accessible drag handles (keyboard drag not required but nice)
- [ ] Responsive: stack columns vertically on mobile
- [ ] Undoable actions (bonus)

---

## Component Architecture

```
App
├── BoardHeader
│   ├── Title
│   └── AddColumnButton
├── Board (horizontal scroll container)
│   └── Column (×N)
│       ├── ColumnHeader
│       │   ├── ColumnTitle (editable)
│       │   ├── CardCount
│       │   └── DeleteColumnButton
│       ├── DroppableArea
│       │   └── Card (×M) [Draggable]
│       │       ├── CardTitle
│       │       ├── CardDescription
│       │       ├── EditButton → EditCardForm
│       │       └── DeleteButton
│       └── AddCardButton → CardForm
```

---

## State Management

```js
const initialState = {
  columns: [
    { id: 'todo', title: 'To Do', cardIds: ['c1', 'c2'] },
    { id: 'progress', title: 'In Progress', cardIds: ['c3'] },
    { id: 'done', title: 'Done', cardIds: [] }
  ],
  cards: {
    c1: { id: 'c1', title: 'Task 1', description: '' },
    c2: { id: 'c2', title: 'Task 2', description: '' },
    c3: { id: 'c3', title: 'Task 3', description: '' }
  }
};
```

Normalized state (cards by ID, columns store ordered cardIds) makes drag updates O(1).

---

## DnD Library Choices

| Library | Pros | Cons |
|---------|------|------|
| **dnd-kit** | Modern, accessible, flexible, maintained | Slightly steeper learning curve |
| **react-beautiful-dnd** | Simple API, great animations | Archived (no longer maintained) |
| **HTML5 DnD API** | No dependencies, native feel | Complex, poor mobile support |

**Recommendation:** dnd-kit for new projects.

---

## Implementation Steps

1. Set up the board layout with horizontal scrolling
2. Define normalized state shape (columns + cardsById)
3. Build Column component with card list
4. Build Card component with edit/delete actions
5. Implement card creation form (inline in column)
6. Implement inline card editing (modal or inline form)
7. Add dnd-kit: `DndContext` → `SortableContext` per column
8. Wire `onDragEnd` to move card between columns / reorder
9. Persist state to LocalStorage
10. Handle empty columns, empty board, responsive layout

---

## Code Snippets

### Drag End Handler

```js
function handleDragEnd(event) {
  const { active, over } = event;
  if (!over) return;

  const activeId = active.id;
  const overId = over.id;

  const activeCol = findColumnByCardId(activeId);
  const overCol = findColumnByCardId(overId);

  if (activeCol.id === overCol.id) {
    // Reorder within same column
    const newCardIds = arrayMove(activeCol.cardIds, activeCol.cardIds.indexOf(activeId), overCol.cardIds.indexOf(overId));
    dispatch({ type: 'REORDER_CARDS', columnId: activeCol.id, cardIds: newCardIds });
  } else {
    // Move to different column
    dispatch({ type: 'MOVE_CARD', cardId: activeId, fromColumn: activeCol.id, toColumn: overCol.id, overIndex: overCol.cardIds.indexOf(overId) });
  }
}
```

### Normalized Reducer (MOVE_CARD)

```js
case 'MOVE_CARD':
  const { cardId, fromColumn, toColumn, overIndex } = action;
  const newColumns = state.columns.map(col => {
    if (col.id === fromColumn) return { ...col, cardIds: col.cardIds.filter(id => id !== cardId) };
    if (col.id === toColumn) {
      const newIds = [...col.cardIds];
      newIds.splice(overIndex, 0, cardId);
      return { ...col, cardIds: newIds };
    }
    return col;
  });
  return { ...state, columns: newColumns };
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Drop on empty column | Append card to end of column's cardIds |
| Drag handle not found | Use entire card as drag trigger or provide explicit handle |
| Very long card title | Truncate with CSS; show full on hover/expand |
| Multiple rapid drags | Debounce persistence; use reducer for consistent state |
| Column with 0 cards | Show "Drop cards here" placeholder |
| Deleting a column | Move its cards to another column first or cascade delete |
| Mobile touch | dnd-kit handles touch by default; test on touch devices |

---

## Bonus Features

- [ ] **Add / rename / delete columns**
- [ ] **Card labels** (color-coded tags)
- [ ] **Due dates** with date picker
- [ ] **Assignee avatars**
- [ ] **Scrollable column** (vertical scroll for many cards)
- [ ] **Activity log** (move, create, delete history)
- [ ] **Undo** last action

---

## Common Interview Questions

1. **Why normalized state?** — Updating a card's position or content only requires changing data in one place. Avoids nested state mutation problems.

2. **How does dnd-kit work internally?** — It uses a sensor system (pointer, touch, keyboard) to track drag events, collision detection to find drop targets, and context to manage active drag state.

3. **How do you prevent flickering during drag?** — Use `useDraggable`'s `transform` instead of changing actual DOM position. Delay state update until `onDragEnd`.

4. **How to handle real-time collaboration?** — Use WebSockets + CRDT (Conflict-free Replicated Data Types) or operational transform. Broadcast drag operations.

---

## Resources

- [dnd-kit Sortable preset](https://docs.dndkit.com/presets/sortable)
- [useReducer + Context pattern](https://react.dev/learn/scaling-up-with-reducer-and-context)
