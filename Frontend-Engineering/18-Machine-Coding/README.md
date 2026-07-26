# Machine Coding in Frontend Interviews

Machine Coding rounds test your ability to build a functional, clean, and well-architected UI application from scratch within a limited time. You are expected to write production-quality code while handling edge cases, state management, and user interactions.

---

## How to Approach a Machine Coding Round

```
1. READ THE PROBLEM (2 min)
   └─ Understand every requirement. Note constraints (no libraries? no CSS framework?).

2. CLARIFY (2 min)
   └─ Ask about tech stack, browser targets, persistence, accessibility needs.

3. PLAN (5 min)
   └─ Component tree, data flow, state shape, folder structure. Draft on paper/whiteboard.

4. IMPLEMENT (remaining time - 10 min)
   └─ Build incrementally: static UI → state → interactions → edge cases.

5. TEST & REFINE (5 min)
   └─ Click through all flows. Fix bugs. Handle empty/loading/error states.
```

---

## Time Management Strategies

| Duration | Approach |
|----------|----------|
| **30 min** | Build only core functionality. Skip animations, advanced styling, persistence. Use simple state (no context/redux). |
| **45 min** | Core + 1-2 bonus features. Light styling. LocalStorage for persistence. Use context for shared state. |
| **60 min** | Full implementation with polish. Multiple bonus features. Proper error handling, accessibility, responsive design. Custom hooks. |

---

## Common Machine Coding Patterns

| Pattern | Examples | Key Techniques |
|---------|----------|----------------|
| **CRUD** | Todo App, Notes, Contacts | Array state, immutable updates, form handling |
| **Drag & Drop** | Kanban, File Explorer, Dashboard | HTML5 DnD API, dnd-kit, react-beautiful-dnd |
| **Real-time** | Chat, Notifications, Collaborative | WebSocket, polling, optimistic updates |
| **Data-heavy** | Data Grid, Spreadsheet, Calendar | Virtualization (react-window), pagination, memoization |
| **Drawing** | Whiteboard, Image Editor | Canvas API, SVG, pointer events |
| **Form-heavy** | Form Builder, Survey | Dynamic field registry, validation engines, JSON serialization |

---

## Evaluation Criteria

| Criteria | Weight | What Interviewers Look For |
|----------|--------|----------------------------|
| **Functionality** | 30% | All requirements work. No major bugs. Features match spec. |
| **Code Quality** | 25% | Clean components, proper naming, separation of concerns, no duplication. |
| **Edge Cases** | 15% | Empty states, loading states, error boundaries, boundary values. |
| **Performance** | 15% | No unnecessary re-renders, efficient list rendering, debounce/throttle. |
| **UI/UX** | 15% | Responsive, accessible (a11y), smooth interactions, visual polish. |

---

## Checklist for Every Round

### Before You Start
- [ ] Read the problem statement twice
- [ ] Clarify ambiguous requirements
- [ ] Decide on state management approach
- [ ] Sketch component tree + data flow
- [ ] Plan the implementation order

### During Implementation
- [ ] Create project with chosen stack (Vite/CRA/Next.js)
- [ ] Build static component shell first
- [ ] Wire up state and data flow
- [ ] Add interactions and event handlers
- [ ] Handle edge cases (empty, error, loading)

### Before Submission
- [ ] Test all user flows end-to-end
- [ ] Fix console errors and warnings
- [ ] Check responsiveness (if required)
- [ ] Add basic accessibility attributes
- [ ] Clean up unused imports / dead code
- [ ] Ensure no hardcoded test data in final build
