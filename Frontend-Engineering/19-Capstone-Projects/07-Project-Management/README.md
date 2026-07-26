# Project Management Tool

## Project Overview

Build a comprehensive project management tool with projects, tasks, kanban board, Gantt chart, team management, time tracking, and notifications. This project combines complex UI interactions (drag and drop, Gantt chart rendering), Redux Toolkit for state management, and real-time collaboration features.

## Learning Objectives

- Drag and drop with dnd-kit (kanban, sortable lists)
- Gantt chart rendering with custom SVG/CSS
- Complex state management with Redux Toolkit (normalized state)
- Real-time notifications with WebSockets or SSE
- Task dependency graph management
- Time tracking and reporting
- Team collaboration features (comments, assignments)
- File attachments and preview
- Project timeline and milestone tracking

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| React + Vite | Framework | Fast builds, client-heavy app |
| TypeScript | Language | Type safety |
| Redux Toolkit | State management | Normalized state for complex entities |
| TanStack Query | Server state | Caching, pagination |
| dnd-kit | Drag & drop | Kanban, sortable lists, modern API |
| Tailwind CSS | Styling | Utility-first, fast UI development |
| Auth.js | Authentication | Session + roles |
| Prisma + Postgres | Database | Type-safe ORM |
| React Router v6 | Routing | Layout routes, nested routing |
| React Hook Form + Zod | Forms | Complex form validation |
| date-fns | Dates | Gantt chart date calculations |

## Feature List

### MVP Features
- Project CRUD with details, description, dates
- Task CRUD with assignee, priority, due date, status
- Kanban board with drag and drop (columns: To Do, In Progress, Review, Done)
- Task detail view (comments, attachments, activity log)
- Team member management (invite, roles: owner, admin, member)
- Gantt chart view (task timeline, dependencies, milestones)
- Time tracking (log hours per task)
- Dashboard (project overview, task stats, burndown chart)
- Activity feed and notifications
- Search across projects and tasks

### Advanced Features
- Task dependencies (blocked by, blocks)
- Sprint management (Scrum: backlog, sprint planning, velocity)
- File attachments with drag and drop upload
- Task templates and recurring tasks
- Custom fields per project
- Workload view (team member allocation)
- Export reports (CSV, PDF)
- Calendar integration (Google Calendar sync)
- API for third-party integrations
- Automation rules (when status changes → notify assignee)

## Architecture Diagram

```
src/
├── main.tsx
├── App.tsx
├── layouts/
│   ├── AuthLayout.tsx
│   ├── AppLayout.tsx               # Sidebar + topbar
│   └── ProjectLayout.tsx           # Project-specific nav
├── pages/
│   ├── auth/
│   │   ├── LoginPage.tsx
│   │   └── RegisterPage.tsx
│   ├── dashboard/
│   │   └── DashboardPage.tsx       # Cross-project overview
│   ├── projects/
│   │   ├── ProjectListPage.tsx
│   │   ├── ProjectDetailPage.tsx   # Project overview
│   │   ├── ProjectBoardPage.tsx    # Kanban
│   │   ├── ProjectGanttPage.tsx    # Gantt chart
│   │   ├── ProjectTimelinePage.tsx
│   │   ├── ProjectTasksPage.tsx    # List view
│   │   └── ProjectSettingsPage.tsx
│   ├── tasks/
│   │   └── TaskDetailPage.tsx
│   ├── team/
│   │   ├── TeamPage.tsx
│   │   └── MemberProfilePage.tsx
│   ├── reports/
│   │   └── ReportsPage.tsx
│   └── settings/
│       ├── ProfilePage.tsx
│       └── NotificationsPage.tsx
├── components/
│   ├── layout/
│   │   ├── AppSidebar.tsx
│   │   ├── ProjectSidebar.tsx
│   │   ├── TopBar.tsx
│   │   ├── Breadcrumbs.tsx
│   │   └── SearchCommand.tsx       # Cmd+K search
│   ├── projects/
│   │   ├── ProjectCard.tsx
│   │   ├── ProjectForm.tsx
│   │   ├── ProjectStats.tsx
│   │   └── ProjectMembers.tsx
│   ├── board/
│   │   ├── KanbanBoard.tsx
│   │   ├── KanbanColumn.tsx
│   │   ├── KanbanCard.tsx
│   │   ├── ColumnHeader.tsx
│   │   └── AddTaskButton.tsx
│   ├── tasks/
│   │   ├── TaskList.tsx
│   │   ├── TaskRow.tsx
│   │   ├── TaskDetail.tsx
│   │   ├── TaskForm.tsx
│   │   ├── TaskComments.tsx
│   │   ├── TaskAttachments.tsx
│   │   ├── TaskActivity.tsx
│   │   └── TimeLogForm.tsx
│   ├── gantt/
│   │   ├── GanttChart.tsx
│   │   ├── GanttHeader.tsx         // Date headers
│   │   ├── GanttRow.tsx            // Task bars
│   │   ├── GanttDependency.tsx     // Lines between tasks
│   │   └── GanttControls.tsx       // Zoom, scroll to today
│   ├── team/
│   │   ├── MemberList.tsx
│   │   ├── MemberCard.tsx
│   │   ├── InviteForm.tsx
│   │   └── WorkloadView.tsx
│   ├── dashboard/
│   │   ├── StatsGrid.tsx
│   │   ├── BurndownChart.tsx
│   │   ├── TaskDistribution.tsx
│   │   └── RecentActivity.tsx
│   └── ui/
│       ├── Avatar.tsx
│       ├── Badge.tsx
│       ├── DataTable.tsx
│       ├── Modal.tsx
│       ├── Dropdown.tsx
│       ├── CommandPalette.tsx
│       └── Toast.tsx
├── store/
│   ├── index.ts
│   ├── slices/
│   │   ├── authSlice.ts
│   │   ├── projectSlice.ts         # Current project state
│   │   ├── boardSlice.ts           # Kanban column order
│   │   └── uiSlice.ts
│   └── api/
│       ├── projectsApi.ts
│       ├── tasksApi.ts
│       ├── teamApi.ts
│       └── notificationsApi.ts
├── hooks/
│   ├── useProjects.ts
│   ├── useBoard.ts
│   ├── useTasks.ts
│   ├── useGantt.ts
│   └── useNotifications.ts
├── lib/
│   ├── utils.ts
│   ├── constants.ts
│   ├── permissions.ts              # Project role permissions
│   └── gantt-utils.ts              # Date calculations
├── types/
│   ├── project.ts
│   ├── task.ts
│   ├── board.ts
│   └── team.ts
└── styles/
    └── globals.css
```

## Component Tree

```
<AppLayout>
  <AppSidebar>
    <Logo />
    <NavItem icon={Home} />         {/* Dashboard */}
    <NavItem icon={Folder} />       {/* Projects */}
    <NavItem icon={Users} />        {/* Team */}
    <NavItem icon={BarChart} />     {/* Reports */}
    <ProjectList />                 {/* Quick project switcher */}
    <UserMenu />
  </AppSidebar>
  <MainArea>
    <TopBar>
      <Breadcrumbs />
      <SearchCommand />             {/* Cmd+K */}
      <NotificationBell />
      <CreateButton />              {/* Quick create task/project */}
    </TopBar>
    <Content>
      {children}
    </Content>
  </MainArea>
</AppLayout>

ProjectBoardPage:
  <ProjectHeader>
    <ProjectName />
    <ProjectNav tabs={Board, List, Gantt, Timeline} />
    <InviteButton />
  </ProjectHeader>
  <KanbanBoard>                     {/* dnd-kit context */}
    <KanbanColumn status="todo">    {/* Droppable */}
      <ColumnHeader count={3} />
      <KanbanCard task={...}>       {/* Draggable */}
        <TaskTitle />
        <Assignee />
        <Priority />
        <DueDate />
      </KanbanCard>
    </KanbanColumn>
    <KanbanColumn status="in_progress">
      ...
    </KanbanColumn>
    <KanbanColumn status="review">
      ...
    </KanbanColumn>
    <KanbanColumn status="done">
      ...
    </KanbanColumn>
  </KanbanBoard>

ProjectGanttPage:
  <GanttControls />                 {/* Zoom: day/week/month */}
  <GanttChart>
    <GanttHeader />                 {/* Date axis */}
    <GanttBody>
      <GanttRow task={...}>         {/* Task bar + name */}
        <TaskBar />
        <ProgressBar />
        <DependencyArrow />         {/* SVG line to dependent task */}
      </GanttRow>
    </GanttBody>
  </GanttChart>

TaskDetailPage:
  <TaskDetail>
    <TaskTitle />
    <TaskDescription />
    <PropertiesPanel>
      <AssigneeSelect />
      <PrioritySelect />
      <StatusSelect />
      <DueDatePicker />
      <TimeEstimate />
    </PropertiesPanel>
    <Tabs>
      <Tab label="Comments">
        <CommentList />
        <CommentForm />
      </Tab>
      <Tab label="Attachments">
        <FileUpload />
        <AttachmentList />
      </Tab>
      <Tab label="Activity">
        <ActivityLog />
      </Tab>
      <Tab label="Time">
        <TimeLogForm />
        <TimeLogList />
      </Tab>
    </Tabs>
  </TaskDetail>
```

## Database Schema

```prisma
model User {
  id              String   @id @default(cuid())
  email           String   @unique
  name            String
  avatar          String?
  projectRoles    ProjectRole[]
  tasks           Task[]
  comments        Comment[]
  timeLogs        TimeLog[]
  notifications   Notification[]
  createdAt       DateTime @default(now())
}

model Project {
  id          String        @id @default(cuid())
  name        String
  description String?
  key         String        @unique // "PROJ"
  startDate   DateTime?
  endDate     DateTime?
  status      ProjectStatus @default(ACTIVE)
  members     ProjectRole[]
  tasks       Task[]
  milestones  Milestone[]
  createdAt   DateTime      @default(now())
  updatedAt   DateTime      @updatedAt
}

enum ProjectStatus { ACTIVE ARCHIVED COMPLETED ON_HOLD }

model ProjectRole {
  id        String      @id @default(cuid())
  userId    String
  user      User        @relation(fields: [userId], references: [id])
  projectId String
  project   Project     @relation(fields: [projectId], references: [id])
  role      MemberRole  @default(MEMBER)
  @@unique([userId, projectId])
}

enum MemberRole { OWNER ADMIN MEMBER GUEST }

model Task {
  id          String     @id @default(cuid())
  title       String
  description String?
  status      TaskStatus @default(TODO)
  priority    Priority   @default(MEDIUM)
  storyPoints Int?
  estimate    Int?       // hours
  actualHours Float?     // logged hours
  startDate   DateTime?
  dueDate     DateTime?
  sortOrder   Float      // For kanban position
  projectId   String
  project     Project    @relation(fields: [projectId], references: [id])
  assigneeId  String?
  assignee    User?      @relation(fields: [assigneeId], references: [id])
  parentId    String?
  parent      Task?      @relation("TaskSubtasks", fields: [parentId], references: [id])
  subtasks    Task[]     @relation("TaskSubtasks")
  dependsOn   TaskDependency[] @relation("TaskDependsOn")
  dependedBy  TaskDependency[] @relation("TaskDependedBy")
  milestoneId String?
  milestone   Milestone? @relation(fields: [milestoneId], references: [id])
  comments    Comment[]
  timeLogs    TimeLog[]
  attachments Attachment[]
  labels      Label[]
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt
}

enum TaskStatus { TODO IN_PROGRESS REVIEW DONE CANCELLED }
enum Priority { URGENT HIGH MEDIUM LOW }

model TaskDependency {
  id            String @id @default(cuid())
  taskId        String
  task          Task   @relation("TaskDependsOn", fields: [taskId], references: [id])
  dependsOnId   String
  dependsOn     Task   @relation("TaskDependedBy", fields: [dependsOnId], references: [id])
  type          DependencyType @default(FINISH_TO_START)
  @@unique([taskId, dependsOnId])
}

enum DependencyType { FINISH_TO_START START_TO_START FINISH_TO_FINISH START_TO_FINISH }

model Milestone {
  id        String   @id @default(cuid())
  name      String
  date      DateTime
  projectId String
  project   Project  @relation(fields: [projectId], references: [id])
  tasks     Task[]
}

model Comment {
  id        String   @id @default(cuid())
  content   String
  userId    String
  user      User     @relation(fields: [userId], references: [id])
  taskId    String
  task      Task     @relation(fields: [taskId], references: [id])
  parentId  String?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model TimeLog {
  id        String   @id @default(cuid())
  duration  Float    // hours
  description String?
  date      DateTime
  userId    String
  user      User     @relation(fields: [userId], references: [id])
  taskId    String
  task      Task     @relation(fields: [taskId], references: [id])
  createdAt DateTime @default(now())
}

model Label {
  id    String @id @default(cuid())
  name  String
  color String
  tasks Task[]
}

model Attachment {
  id        String   @id @default(cuid())
  name      String
  url       String
  size      Int      // bytes
  mimeType  String
  taskId    String
  task      Task     @relation(fields: [taskId], references: [id])
  uploadedBy String
  createdAt DateTime @default(now())
}
```

## Route Structure

| Route | Component | Auth | Description |
|-------|-----------|------|-------------|
| `/login` | LoginPage | — | Login |
| `/` | DashboardPage | Required | Cross-project dashboard |
| `/projects` | ProjectListPage | Required | All projects |
| `/projects/new` | ProjectFormPage | Required | Create project |
| `/projects/:id` | ProjectDetailPage | Member | Project overview |
| `/projects/:id/board` | ProjectBoardPage | Member | Kanban board |
| `/projects/:id/gantt` | ProjectGanttPage | Member | Gantt chart |
| `/projects/:id/tasks` | ProjectTasksPage | Member | Task list |
| `/projects/:id/settings` | ProjectSettingsPage | Owner, Admin | Project settings |
| `/projects/:id/team` | TeamPage | Member | Team management |
| `/tasks/:id` | TaskDetailPage | Member | Task detail |
| `/team` | TeamPage | Required | All teams |
| `/reports` | ReportsPage | Required | Reports and analytics |
| `/settings/profile` | ProfilePage | Required | User profile |
| `/settings/notifications` | NotificationsPage | Required | Notification prefs |

## Key Implementation Considerations

- Kanban: use dnd-kit with `SortableContext` per column, `DndContext` for the board
- Gantt chart: custom SVG/CSS rendering with date calculations (zoom levels: day, week, month)
- Normalize Redux state by entity ID for efficient updates (tasks, projects, members)
- Use optimistic updates for drag-and-drop operations (move task → update immediately → sync)
- Implement permission checks per project role (owner, admin, member, guest)
- Task dependencies: implement cycle detection when adding dependencies
- Time tracking: start/stop timer with manual entry option
- Real-time notifications via Server-Sent Events (SSE) or polling
- Search: implement command palette (Cmd+K) for quick access

## Performance Considerations

- Virtualize task list for projects with 500+ tasks (`@tanstack/react-virtual`)
- Gantt chart: use virtualization for rows (only render visible tasks)
- Kanban: limit column height with scroll per column, not page scroll
- Lazy load task detail (comments, attachments loaded on demand)
- Debounce real-time updates (batch notifications every 2 seconds)
- Use `React.memo` for KanbanCard, TaskRow, GanttRow
- Compute Gantt chart layout in a web worker for large projects
- Bundle split by route — Gantt chart loads separately from kanban

## Deployment Strategy

1. **Vite build** → Vercel or Netlify for frontend
2. **Node/Express API** for backend (or Next.js API routes)
3. **Neon.tech** for Postgres database
4. **AWS S3** for file attachments (presigned URLs)
5. **Environment variables**: database URL, storage keys, auth secret
6. **CI/CD**: GitHub Actions → lint → test → build → deploy
7. **Sentry** for error tracking

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Data model, user flows, wireframes | 2 |
| Foundation | Vite setup, auth, layouts, routing | 1.5 |
| Projects | CRUD, listing, settings | 1.5 |
| Tasks | CRUD, list view, detail, forms | 2 |
| Kanban Board | dnd-kit, columns, drag-and-drop | 3 |
| Gantt Chart | SVG rendering, dependencies, zoom | 3 |
| Team | Invite, roles, member management | 1.5 |
| Time Tracking | Timer, manual entry, reporting | 1.5 |
| Dashboard | Stats, charts, burndown | 1.5 |
| Notifications | In-app, email, real-time | 1.5 |
| Polish | Responsive, permissions, error states | 1.5 |
| Deploy | CI/CD, storage, environment config | 1 |
| **Total** | | **~11-16 days** |

## Learning Resources

- [dnd-kit Documentation](https://docs.dndkit.com/)
- [Redux Toolkit - Normalized State](https://redux-toolkit.js.org/usage/usage-with-typescript#normalized-state)
- [TanStack Query](https://tanstack.com/query/latest/docs/react/overview)
- [Gantt Chart SVG Tutorial](https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorial)
- [React Router v6](https://reactrouter.com/en/main)
- [Prisma with Postgres](https://www.prisma.io/docs/orm/overview/databases/postgresql)
- [date-fns Documentation](https://date-fns.org/)
- [React Hook Form + Zod](https://react-hook-form.com/get-started#IntegratingwithUIlibraries)
