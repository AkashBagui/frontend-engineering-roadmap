# Google Docs Clone — Collaborative Document Editor

## Project Overview

Build a real-time collaborative document editor with rich text formatting, cursor presence, comments, version history, and sharing. This is one of the most technically challenging projects, introducing CRDT (Conflict-free Replicated Data Types), Operational Transformation (OT), WebSocket-based collaboration, and Slate.js for rich text editing.

## Learning Objectives

- Slate.js for custom rich text editing
- CRDT with Yjs for conflict-free collaboration
- Operational Transform (OT) fundamentals vs CRDT
- WebSocket real-time communication with Socket.io
- Cursor presence (multi-user cursor positions)
- Selection awareness (who selected what text)
- Comment/annotation threading
- Version history and diffing
- Document permissions and sharing
- Undo/redo with collaborative awareness

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| React + Vite | Framework | Client-heavy, real-time app |
| TypeScript | Language | Type safety |
| Slate.js | Rich text editor | Customizable, plugin architecture, schema-less |
| Yjs | CRDT | Conflict-free collaboration, offline support |
| Socket.io | WebSocket | Real-time events, reconnection, room management |
| y-websocket | Yjs sync | WebSocket provider for Yjs document sync |
| y-slate | Yjs + Slate | Yjs binding for Slate.js |
| Prisma + Postgres | Database | Document metadata, users, comments |
| Auth.js | Authentication | Session + sharing permissions |
| Tailwind CSS | Styling | UI components, editor chrome |
| Zustand | State management | UI state, collaboration state |

## CRDT vs OT Comparison

| Aspect | CRDT (Yjs) | OT (Google Docs) |
|--------|-----------|------------------|
| Architecture | Peer-to-peer / broadcast | Central server transforms ops |
| Conflict resolution | Mathematical merge (LWW, RGA) | Transform function per operation |
| Offline support | Native — works offline | Requires server |
| Complexity | Simpler to implement | Complex transform logic |
| Consistency | Eventual consistency | Strong consistency |
| Scalability | Peer-to-peer (no server bottleneck) | Server must process all operations |
| Undo | Tricky without awareness | Well-understood |
| Adoption | Yjs, Automerge | Google Docs, Etherpad |

**This project uses Yjs (CRDT) for its simpler collaboration model and offline support.**

## Feature List

### MVP Features
- Rich text editing (bold, italic, underline, strikethrough)
- Headings, lists (ordered/unordered), blockquotes
- Font size, color, highlight
- Text alignment (left, center, right)
- Real-time collaborative editing (2+ users)
- Multi-cursor presence (name labels on cursors)
- Selection awareness (highlight who selected what)
- Document sharing with permissions (view/edit/comment)
- Comments and suggestions mode
- Document title and metadata

### Advanced Features
- Version history with restore
- Image embedding
- Table editing
- Link insertion and preview
- Find and replace
- Word count and statistics
- Export to PDF/Word/Markdown
- Import from Word/Markdown
- Offline editing with sync on reconnect
- Activity log (who changed what)
- Templates (resume, report, letter)
- Mobile editing experience

## Architecture Diagram

```
src/
├── main.tsx
├── App.tsx
├── layouts/
│   ├── AuthLayout.tsx
│   └── AppLayout.tsx                # Document list / editor
├── pages/
│   ├── auth/
│   │   └── LoginPage.tsx
│   ├── documents/
│   │   ├── DocumentListPage.tsx     # "My Documents"
│   │   ├── DocumentEditorPage.tsx   # The editor
│   │   └── DocumentHistoryPage.tsx  # Version history
│   └── sharing/
│       └── ShareDialog.tsx
├── components/
│   ├── layout/
│   │   ├── TopBar.tsx               # Title, share button, presence
│   │   ├── MenuBar.tsx              # File, Edit, View, Insert, Format
│   │   └── Toolbar.tsx              # Bold, italic, headings, etc.
│   ├── editor/
│   │   ├── SlateEditor.tsx          # Main Slate.js editor
│   │   ├── EditorLeaf.tsx           # Custom leaf rendering (bold, etc.)
│   │   ├── EditorElement.tsx        # Custom element rendering (heading, list)
│   │   ├── HoveringToolbar.tsx      # Floating toolbar on selection
│   │   ├── CursorOverlay.tsx        # Remote cursors overlay
│   │   ├── SelectionOverlay.tsx     # Remote selections
│   │   └── Placeholder.tsx          # Empty document placeholder
│   ├── collaboration/
│   │   ├── CollaborationProvider.tsx # Yjs + Socket.io provider
│   │   ├── CursorPresence.tsx       # Cursor position tracking
│   │   ├── AwarenessState.tsx       # Connected users list
│   │   └── PresenceAvatars.tsx      # User avatars in toolbar
│   ├── comments/
│   │   ├── CommentSidebar.tsx
│   │   ├── CommentThread.tsx
│   │   ├── CommentInput.tsx
│   │   └── CommentBadge.tsx         # Inline comment markers
│   ├── version-history/
│   │   ├── VersionTimeline.tsx
│   │   ├── VersionDiff.tsx          # Diff view between versions
│   │   └── RestoreButton.tsx
│   ├── sharing/
│   │   ├── ShareDialog.tsx
│   │   ├── ShareLink.tsx
│   │   └── PermissionSelect.tsx
│   └── ui/
│       ├── Button.tsx
│       ├── Input.tsx
│       ├── Tooltip.tsx
│       ├── Avatar.tsx
│       ├── Badge.tsx
│       └── Modal.tsx
├── lib/
│   ├── collaboration/
│   │   ├── yjs.ts                   # Yjs document setup
│   │   ├── provider.ts              # y-websocket provider
│   │   └── awareness.ts            # Cursor awareness protocol
│   ├── editor/
│   │   ├── config.ts                # Slate.js plugins
│   │   ├── serialization.ts         # HTML/Markdown conversion
│   │   ├── shortcuts.ts             # Keyboard shortcuts
│   │   └── commands.ts              # Custom Slate commands
│   ├── permissions.ts
│   └── utils.ts
├── hooks/
│   ├── useEditor.ts                 # Slate editor instance
│   ├── useCollaboration.ts          # Yjs + Socket.io hooks
│   ├── useCursor.ts                 # Cursor tracking
│   └── useComments.ts
├── stores/
│   ├── useDocumentStore.ts
│   └── useUIStore.ts
├── types/
│   ├── editor.ts
│   ├── document.ts
│   └── collaboration.ts
└── styles/
    └── globals.css
```

## Component Tree

```
<AppLayout>
  <TopBar>
    <DocumentTitle />                {/* Editable */}
    <MenuBar>
      <FileMenu />
      <EditMenu />
      <InsertMenu />
      <FormatMenu />
    </MenuBar>
    <Toolbar>
      <UndoRedoButtons />
      <TextStyleButtons />           {/* Bold, italic, underline */}
      <HeadingSelect />
      <ListButtons />
      <AlignButtons />
      <MoreOptions />
    </Toolbar>
    <RightSection>
      <PresenceAvatars />            {/* Who's editing */}
      <ShareButton />
      <HistoryButton />
      <UserMenu />
    </RightSection>
  </TopBar>
  <EditorArea>
    <CollaborationProvider>          {/* Yjs + Socket.io */}
      <SlateEditor>
        <EditorLeaf />*              {/* Character-level rendering */}
        <EditorElement />*           {/* Block-level rendering */}
        <CursorOverlay />            {/* Remote cursors */}
        <SelectionOverlay />         {/* Remote selections */}
        <HoveringToolbar />          {/* Appears on text selection */}
      </SlateEditor>
    </CollaborationProvider>
    <CommentSidebar>                 {/* Threaded comments */}
      <CommentThread />*
    </CommentSidebar>
  </EditorArea>
</AppLayout>
```

## Data Flow

### Collaboration Architecture
```
┌─────────────────────────────────────────────────────────────────────┐
│                        Yjs Document (CRDT)                         │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                │
│  │   Client A   │  │   Client B   │  │   Client C   │              │
│  │  (Slate.js)  │  │  (Slate.js)  │  │  (Slate.js)  │              │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘              │
│         │                 │                 │                        │
│         ▼                 ▼                 ▼                        │
│  ┌──────────────────────────────────────────────────────────┐       │
│  │              y-websocket Provider (sync)                  │       │
│  │  ┌─────────────────────────────────────────────────┐    │       │
│  │  │           Socket.io Server (Room-based)          │    │       │
│  │  │  Each document = room, broadcasts Yjs updates    │    │       │
│  │  └─────────────────────────────────────────────────┘    │       │
│  └──────────────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────────────┘
```

### Edit Flow
```
User types → Slate.js onChange → Yjs doc update (local)
                                          ↓
                  y-websocket broadcasts to other clients
                                          ↓
                  Remote clients apply CRDT merge → UI updates
                                          ↓
                  Cursor positions → Awareness protocol → Other clients see cursor
```

### Save Flow
```
Periodic save (every 30s) → Yjs document snapshot → Database
                                    ↓
                    Version created if significant change detected
                                    ↓
                    Title, metadata saved immediately on change
```

## Route Structure

| Route | Component | Auth | Description |
|-------|-----------|------|-------------|
| `/login` | LoginPage | — | Login |
| `/` | DocumentListPage | Required | All documents |
| `/documents/:id` | DocumentEditorPage | Required | Edit document |
| `/documents/:id/history` | DocumentHistoryPage | Required | Version history |
| `/documents/:id/comments` | Editor (with sidebar) | Required | Comments view |
| `/api/documents` | API | Required | Document CRUD |
| `/api/documents/:id` | API | Required | Single document |
| `/api/documents/:id/versions` | API | Required | Version list |
| `/api/documents/:id/sharing` | API | Required | Sharing settings |

## Database Schema

```prisma
model Document {
  id        String      @id @default(cuid())
  title     String      @default("Untitled")
  content   Bytes?      // Yjs snapshot (binary)
  ownerId   String
  owner     User        @relation(fields: [ownerId], references: [id])
  versions  Version[]
  comments  Comment[]
  shares    DocumentShare[]
  createdAt DateTime    @default(now())
  updatedAt DateTime    @updatedAt
}

model User {
  id       String        @id @default(cuid())
  email    String        @unique
  name     String
  avatar   String?
  documents Document[]
  comments  Comment[]
  shares    DocumentShare[]
}

model DocumentShare {
  id         String       @id @default(cuid())
  documentId String
  document   Document     @relation(fields: [documentId], references: [id])
  userId     String
  user       User         @relation(fields: [userId], references: [id])
  permission Permission   @default(VIEW)
  @@unique([documentId, userId])
}

enum Permission { VIEW EDIT COMMENT }

model Version {
  id         String   @id @default(cuid())
  documentId String
  document   Document @relation(fields: [documentId], references: [id])
  snapshot   Bytes    // Yjs snapshot at this version
  label      String?  // User-provided or auto-generated
  createdAt  DateTime @default(now())
}

model Comment {
  id         String   @id @default(cuid())
  content    String
  selection  Json?    // { anchor: {path, offset}, focus: {path, offset} }
  resolved   Boolean  @default(false)
  documentId String
  document   Document @relation(fields: [documentId], references: [id])
  authorId   String
  author     User     @relation(fields: [authorId], references: [id])
  parentId   String?  // Thread reply
  createdAt  DateTime @default(now())
  updatedAt  DateTime @updatedAt
}
```

## Key Implementation Considerations

- Use Yjs with y-websocket provider for real-time collaboration
- Use y-slate for Yjs ↔ Slate.js binding (syncs Slate value to Yjs document)
- Slate.js uses a normalized JSON structure — Yjs transforms it to a shared type
- Cursor/selection awareness uses Yjs Awareness protocol (not part of document state)
- Implement debounced saves (30s) to avoid excessive database writes
- Version history: store Yjs snapshots on significant changes, allow restore
- Comments: store selection range to anchor comment to specific text
- Implement undo/redo carefully — Yjs has its own undo manager that works across users
- Handling conflicts: CRDT handles automatically — just merge and render

## Performance Considerations

- Yjs is extremely efficient — binary encoding, incremental updates
- Cursor positions: throttle updates (every 100ms) to avoid network spam
- Slate.js: use `React.memo` for leaf and element components
- Virtualize comment sidebar if many comments
- Lazy load version history (only fetch when user opens panel)
- Store Yjs snapshots as binary (Buffer/Blob) — more compact than JSON
- Bundle analysis: Slate + Yjs + Socket.io is heavy — use code splitting
- Consider Web Workers for Yjs document processing in large documents

## Deployment Strategy

1. **Frontend**: Vite build → Vercel or Netlify
2. **Socket.io server**: Separate Node.js server on Railway/Render (or Next.js custom server)
3. **Database**: Neon.tech or Supabase (Postgres)
4. **y-websocket**: Can run alongside Socket.io server
5. **Environment variables**: database URL, Socket.io server URL, auth secret
6. **CI/CD**: GitHub Actions → lint → test → build → deploy

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | CRDT vs OT research, data model, wireframes | 2 |
| Foundation | Vite setup, Slate.js editor, basic formatting | 2 |
| Rich Text | Headings, lists, links, images, toolbars | 2 |
| Collaboration | Yjs setup, y-websocket, y-slate binding | 3 |
| Cursor Presence | Awareness, cursor rendering, selection overlay | 2 |
| Comments | Selection anchoring, threads, sidebar | 2 |
| Version History | Snapshots, restore, diff view | 2 |
| Sharing | Permissions, invite flow, link sharing | 1.5 |
| Polish | Undo/redo, error handling, mobile, offline | 2 |
| Deploy | Socket.io server, database, environment config | 1 |
| **Total** | | **~14-22 days** |

## Learning Resources

- [Slate.js Documentation](https://docs.slatejs.org/)
- [Yjs Documentation](https://docs.yjs.dev/)
- [y-websocket Provider](https://github.com/yjs/y-websocket)
- [y-slate Integration](https://github.com/BitPhinix/y-slate)
- [Socket.io Documentation](https://socket.io/docs/v4/)
- [CRDT Explained (Martin Kleppmann)](https://martin.kleppmann.com/papers/crdt-paper.pdf)
- [Operational Transform vs CRDT](https://www.tiny.cloud/blog/real-time-collaboration-ot-vs-crdt/)
- [Prisma with Postgres](https://www.prisma.io/docs/orm/overview/databases/postgresql)
