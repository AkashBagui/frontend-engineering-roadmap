# CRM Dashboard

## Project Overview

Build a customer relationship management (CRM) dashboard with kanban board, charts, contacts management, deals pipeline, activity tracking, and reports. This project focuses on complex UI patterns (kanban, charts, data tables), state management with Zustand, server state with TanStack Query, and dashboard analytics.

## Learning Objectives

- Complex dashboard UI patterns (kanban, data tables, charts)
- Zustand for lightweight global state management
- TanStack Query for server state with real-time updates
- Recharts for interactive data visualization
- Drag and drop (kanban pipeline)
- Advanced filtering, sorting, and search
- Role-based view (admin, manager, agent)
- Export and reporting (CSV, PDF)
- Responsive data-heavy interfaces

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| React + Vite | Framework | Fast HMR, no SSR needed for dashboard |
| TypeScript | Language | Type safety |
| Zustand | State management | Minimal boilerplate, great for dashboard state |
| TanStack Query | Server state | Caching, pagination, optimistic updates |
| Recharts | Charts | Declarative, responsive, React-native |
| Tailwind CSS | Styling | Utility-first, dashboard component libraries |
| Auth.js | Authentication | Session management, roles |
| Prisma + Postgres | Database | Type-safe ORM |
| React Router v6 | Routing | Layout routes, nested routing |
| dnd-kit | Drag & drop | Kanban pipeline, sortable lists |

## Feature List

### MVP Features
- Login/register with role-based access (admin, manager, agent)
- Dashboard homepage with key metrics widgets
- Contacts management (CRUD, search, filter, import/export)
- Deals pipeline (kanban board with draggable cards)
- Deal stages (lead → qualified → proposal → negotiation → closed)
- Activity log (calls, emails, meetings linked to contacts)
- Charts and analytics (revenue pipeline, conversion rates, activity trends)
- User profile and settings
- Search across contacts, deals, activities

### Advanced Features
- Email integration (send/receive via Gmail API)
- Call logging with duration tracking
- Reports module (custom date range, export to PDF/CSV)
- Automated email sequences
- Contact segmentation / tags
- Meeting scheduler with calendar integration
- Team performance dashboard
- Import contacts from CSV/XLSX
- Real-time notifications (socket.io)
- Multi-workspace support

## Architecture Diagram

```
src/
├── main.tsx                       # Entry point
├── App.tsx                        # Router setup
├── layouts/
│   ├── AuthLayout.tsx             # Login/register layout
│   ├── DashboardLayout.tsx        # Sidebar + header + content
│   └── AdminLayout.tsx            # Admin-specific layout
├── pages/
│   ├── auth/
│   │   ├── LoginPage.tsx
│   │   └── RegisterPage.tsx
│   ├── dashboard/
│   │   └── DashboardPage.tsx      # Metrics overview
│   ├── contacts/
│   │   ├── ContactListPage.tsx
│   │   └── ContactDetailPage.tsx
│   ├── deals/
│   │   ├── DealBoardPage.tsx      # Kanban view
│   │   └── DealDetailPage.tsx
│   ├── activities/
│   │   └── ActivityLogPage.tsx
│   ├── reports/
│   │   └── ReportsPage.tsx
│   └── settings/
│       ├── ProfilePage.tsx
│       └── TeamPage.tsx
├── components/
│   ├── layout/
│   │   ├── Sidebar.tsx
│   │   ├── Header.tsx
│   │   ├── Breadcrumbs.tsx
│   │   └── NavItem.tsx
│   ├── dashboard/
│   │   ├── MetricCard.tsx
│   │   ├── RevenueChart.tsx
│   │   ├── ConversionFunnel.tsx
│   │   ├── ActivityTimeline.tsx
│   │   └── RecentDeals.tsx
│   ├── contacts/
│   │   ├── ContactTable.tsx
│   │   ├── ContactCard.tsx
│   │   ├── ContactForm.tsx
│   │   └── ContactSearch.tsx
│   ├── deals/
│   │   ├── KanbanBoard.tsx
│   │   ├── KanbanColumn.tsx
│   │   ├── DealCard.tsx
│   │   ├── DealForm.tsx
│   │   └── DealPipelineChart.tsx
│   ├── activities/
│   │   ├── ActivityList.tsx
│   │   ├── ActivityItem.tsx
│   │   └── ActivityForm.tsx
│   └── ui/
│       ├── DataTable.tsx          # Sortable, filterable table
│       ├── Modal.tsx
│       ├── Dropdown.tsx
│       ├── Badge.tsx
│       ├── Avatar.tsx
│       └── Button.tsx
├── stores/
│   ├── useAuthStore.ts
│   ├── useUIStore.ts
│   ├── useFilterStore.ts
│   └── useDealBoardStore.ts      # Kanban state (columns, order)
├── hooks/
│   ├── useContacts.ts
│   ├── useDeals.ts
│   ├── useActivities.ts
│   ├── useMetrics.ts
│   └── useDebounce.ts
├── lib/
│   ├── api/
│   │   ├── contacts.ts
│   │   ├── deals.ts
│   │   ├── activities.ts
│   │   └── reports.ts
│   ├── utils.ts
│   ├── constants.ts
│   └── csv.ts                    # CSV export
├── types/
│   ├── contact.ts
│   ├── deal.ts
│   ├── activity.ts
│   └── user.ts
└── styles/
    └── globals.css
```

## Component Tree

```
<DashboardLayout>
  <Sidebar>
    <Logo />
    <NavItem />*                  {/* Dashboard, Contacts, Deals, Activities, Reports, Settings */}
    <UserMenu />
  </Sidebar>
  <MainContent>
    <Header>
      <SearchBar />               {/* Global search */}
      <Breadcrumbs />
      <NotificationsBell />
      <UserAvatar />
    </Header>
    <PageContent>
      {/* Route-based content */}
    </PageContent>
  </MainContent>
</DashboardLayout>

DashboardPage:
  <MetricRow>
    <MetricCard />*               {/* Total contacts, active deals, revenue, conversion rate */}
  </MetricRow>
  <ChartRow>
    <RevenueChart />              {/* Bar/line chart */}
    <ConversionFunnel />          {/* Funnel chart */}
  </ChartRow>
  <TableRow>
    <RecentDeals />               {/* Mini table */}
    <ActivityTimeline />          {/* Vertical timeline */}
  </TableRow>

ContactListPage:
  <ContactSearch />               {/* Search + filters */}
  <ContactTable />               {/* Sortable, paginated */}
  <AddContactButton />
  <ContactForm />                 {/* Modal on edit/create */}

DealBoardPage:
  <PipelineHeader>
    <FilterBar />
    <AddDealButton />
    <ViewToggle />                {/* Kanban vs list */}
  </PipelineHeader>
  <KanbanBoard>
    <KanbanColumn />*             {/* Per stage */}
      <DealCard />*               {/* Draggable */}
  </KanbanBoard>
```

## Data Flow

### State Architecture
```
┌─────────────────────────────────────────────────────────────────────┐
│                         ZUSTAND STORES                             │
├────────────────────┬────────────────────┬──────────────────────────┤
│   useAuthStore     │    useUIStore      │   useDealBoardStore      │
│ - user             │ - sidebarOpen      │ - columns: {             │
│ - session          │ - activeModal      │     [stage]: Deal[]      │
│ - login()          │ - toastQueue       │   }                      │
│ - logout()         │ - theme            │ - moveDeal()             │
│ - updateProfile()  │                    │ - addDeal()              │
│                    │                    │ - reorderDeals()         │
└────────────────────┴────────────────────┴──────────────────────────┘
         │                        │                          │
         ▼                        ▼                          ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      TanStack QUERY (Server State)                 │
├──────────────┬──────────────┬──────────────┬───────────────────────┤
│  Contacts    │   Deals      │  Activities  │     Reports            │
│  Queries     │   Queries    │  Queries     │     Queries            │
│  Mutations   │   Mutations  │  Mutations   │     (aggregated)       │
└──────────────┴──────────────┴──────────────┴───────────────────────┘
```

### Data Flow Patterns
- Contacts, Deals, Activities: TanStack Query with `staleTime: 30000`
- Kanban board: Zustand for column/card order, TanStack Query for data
- Optimistic updates: move deal → update Zustand immediately → sync via mutation
- Charts: aggregated data from separate API endpoint, cached for 5 minutes
- Search: debounced (300ms) → TanStack Query with enabled flag
- Export: client-side CSV generation from current filtered data

## Route Structure

| Route | Component | Auth | Description |
|-------|-----------|------|-------------|
| `/login` | LoginPage | — | Login |
| `/register` | RegisterPage | — | Register |
| `/` | DashboardPage | Required | Metrics overview |
| `/contacts` | ContactListPage | Required | Contact list |
| `/contacts/new` | ContactDetailPage | Required | New contact form |
| `/contacts/:id` | ContactDetailPage | Required | Contact detail |
| `/deals` | DealBoardPage | Required | Kanban board |
| `/deals/:id` | DealDetailPage | Required | Deal detail |
| `/activities` | ActivityLogPage | Required | Activity log |
| `/reports` | ReportsPage | Required | Analytics reports |
| `/settings/profile` | ProfilePage | Required | User profile |
| `/settings/team` | TeamPage | Admin | Team management |

## Database Schema

```prisma
model User {
  id         String   @id @default(cuid())
  email      String   @unique
  name       String
  avatar     String?
  role       Role     @default(AGENT)
  deals      Deal[]
  activities Activity[]
  createdAt  DateTime @default(now())
}

enum Role { ADMIN MANAGER AGENT }

model Contact {
  id          String     @id @default(cuid())
  name        String
  email       String?
  phone       String?
  company     String?
  position    String?
  tags        String[]   // ["vip", "new", "enterprise"]
  source      String?    // Referral, Website, Cold Call
  assignedTo  String?
  assignee    User?      @relation(fields: [assignedTo], references: [id])
  deals       Deal[]
  activities  Activity[]
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt
}

model Deal {
  id          String     @id @default(cuid())
  title       String
  value       Decimal
  stage       DealStage  @default(LEAD)
  probability Int        @default(10) // 0-100
  contactId   String
  contact     Contact    @relation(fields: [contactId], references: [id])
  ownerId     String
  owner       User       @relation(fields: [ownerId], references: [id])
  activities  Activity[]
  closeDate   DateTime?
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt
}

enum DealStage { LEAD QUALIFIED PROPOSAL NEGOTIATION CLOSED_WON CLOSED_LOST }

model Activity {
  id        String         @id @default(cuid())
  type      ActivityType
  subject   String
  content   String?        // Notes or description
  dueDate   DateTime?
  completed Boolean        @default(false)
  contactId String
  contact   Contact        @relation(fields: [contactId], references: [id])
  dealId    String?
  deal      Deal?          @relation(fields: [dealId], references: [id])
  ownerId   String
  owner     User           @relation(fields: [ownerId], references: [id])
  createdAt DateTime       @default(now())
}

enum ActivityType { CALL EMAIL MEETING NOTE TASK }
```

## Key Implementation Considerations

- Use Zustand for UI state (sidebar, modals, toasts) and kanban board column state
- Use TanStack Query for all server data — configure `staleTime` per resource
- Kanban board: use `@dnd-kit/core` and `@dnd-kit/sortable` for drag and drop
- Optimistic updates for deal stage changes — revert on API error
- Implement proper loading states for each page section independently
- Use ErrorBoundary per route to prevent whole app crash
- Data table: implement client-side sorting, server-side pagination for large datasets
- Charts: use Recharts responsive containers for mobile support
- Search: debounced input → API call → results with highlighted matches

## Performance Considerations

- Virtualize contact table for 1000+ contacts (`@tanstack/react-virtual`)
- Lazy load report components (charts are heavy)
- Debounce search input (300ms)
- Cache aggregated metrics for 5 minutes
- Use `React.memo` for KanbanColumn, DealCard, ContactRow
- Implement infinite scroll for activity log
- Bundle analysis — ensure chart libraries are tree-shaken
- Use `Suspense` with TanStack Query for data-dependent sections

## Deployment Strategy

1. **Vite build** → deploy to Vercel or Netlify
2. **Neon.tech** for Postgres database
3. **API server** (separate Node/Express API or Next.js API routes)
4. **Environment variables**: database URL, auth secret
5. **Sentry** for error tracking
6. **CI/CD**: GitHub Actions → lint → test → build → deploy

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Data model, wireframes, user flows | 1.5 |
| Foundation | Vite setup, routing, layouts, auth | 1.5 |
| Contacts | CRUD, table, search, filters | 2 |
| Deals | Kanban board, drag & drop, stages | 3 |
| Dashboard | Metric cards, charts, timeline | 2 |
| Activities | Activity log, forms, filtering | 1.5 |
| Reports | Aggregation queries, charts, export | 2 |
| Polish | Responsive, loading, empty states, error | 1.5 |
| Deploy | CI/CD, database, environment config | 1 |
| **Total** | | **~10-15 days** |

## Learning Resources

- [Zustand Documentation](https://docs.pmnd.rs/zustand/getting-started/introduction)
- [TanStack Query](https://tanstack.com/query/latest/docs/react/overview)
- [Recharts Guide](https://recharts.org/en-US/guide)
- [dnd-kit Documentation](https://docs.dndkit.com/)
- [React Router v6](https://reactrouter.com/en/main)
- [Prisma with Postgres](https://www.prisma.io/docs/orm/overview/databases/postgresql)
- [TanStack Virtual](https://tanstack.com/virtual/latest/docs/react/overview)
