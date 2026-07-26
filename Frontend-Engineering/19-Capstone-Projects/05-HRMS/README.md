# HRMS (Human Resource Management System)

## Project Overview

Build a comprehensive HR management system with employee directory, attendance tracking, leave management, payroll views, org chart, and reports. This project focuses on data-heavy interfaces, complex forms, role-based access, AG Grid for enterprise-grade tables, and RTK Query for API state management.

## Learning Objectives

- Enterprise-grade data tables with AG Grid (sorting, filtering, grouping, export)
- RTK Query for API state management (cache invalidation, optimistic updates)
- Complex form workflows (multi-step onboarding, leave applications)
- Organizational chart rendering with custom layout
- Role-based access control (employee, manager, HR, admin)
- File upload and management (documents, profile pictures)
- Report generation and data export
- Dashboard analytics with charts
- Date/time handling across timezones

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| React + Vite | Framework | Fast builds, no SSR needed |
| TypeScript | Language | Type safety |
| Redux Toolkit + RTK Query | State + API | Built-in caching, normalized state, code generation |
| AG Grid | Data table | Enterprise features: grouping, pivoting, export |
| Recharts | Charts | Dashboard visualizations |
| Tailwind CSS | Styling | Utility-first, custom component classes |
| Auth.js | Authentication | Session + role management |
| Prisma + Postgres | Database | Type-safe schema, migrations |
| React Router v6 | Routing | Layout routes, nested routing |
| React Hook Form + Zod | Forms | Complex form validation |

## Feature List

### MVP Features
- Employee directory with search, filter, sort
- Employee profile pages (personal, contact, job info, documents)
- Department and team management
- Attendance tracking (clock in/out, daily logs)
- Leave management (apply, approve/reject, balance tracking)
- Leave calendar view
- Organizational chart
- Payroll summary view
- HR dashboard with key metrics
- Role-based access (Employee, Manager, HR Admin, Super Admin)

### Advanced Features
- Performance review workflows
- Asset management (laptops, phones assigned to employees)
- Employee onboarding checklist
- Timesheet and project hours tracking
- Expense management
- Announcements and company news feed
- Employee self-service portal
- Shift scheduling and swap requests
- Training and certification tracking
- Integration with payroll systems

## Architecture Diagram

```
src/
├── main.tsx
├── App.tsx                           # Router
├── layouts/
│   ├── AuthLayout.tsx
│   ├── DashboardLayout.tsx
│   └── AdminLayout.tsx
├── pages/
│   ├── auth/
│   │   ├── LoginPage.tsx
│   │   └── ForgotPasswordPage.tsx
│   ├── dashboard/
│   │   └── DashboardPage.tsx
│   ├── employees/
│   │   ├── EmployeeListPage.tsx
│   │   ├── EmployeeDetailPage.tsx
│   │   └── EmployeeOnboardingPage.tsx
│   ├── attendance/
│   │   ├── AttendancePage.tsx        # Clock in/out
│   │   └── AttendanceReportPage.tsx
│   ├── leave/
│   │   ├── LeavePage.tsx             # Apply leave
│   │   ├── LeaveBalancePage.tsx
│   │   └── LeaveApprovalPage.tsx     # Manager view
│   ├── payroll/
│   │   ├── PayrollPage.tsx           # Payroll summary
│   │   └── PayslipDetailPage.tsx
│   ├── reports/
│   │   └── ReportsPage.tsx
│   ├── org-chart/
│   │   └── OrgChartPage.tsx
│   └── admin/
│       ├── DepartmentsPage.tsx
│       ├── PositionsPage.tsx
│       ├── SettingsPage.tsx
│       └── AuditLogPage.tsx
├── components/
│   ├── layout/
│   │   ├── Sidebar.tsx
│   │   ├── Header.tsx
│   │   └── NavGroup.tsx
│   ├── employees/
│   │   ├── EmployeeTable.tsx         # AG Grid wrapper
│   │   ├── EmployeeCard.tsx
│   │   ├── EmployeeForm.tsx
│   │   ├── EmployeeDocuments.tsx
│   │   └── EmployeeTimeline.tsx
│   ├── attendance/
│   │   ├── ClockWidget.tsx
│   │   ├── AttendanceTable.tsx
│   │   └── AttendanceCalendar.tsx
│   ├── leave/
│   │   ├── LeaveForm.tsx
│   │   ├── LeaveBalanceCard.tsx
│   │   ├── LeaveCalendar.tsx
│   │   └── LeaveApprovalCard.tsx
│   ├── payroll/
│   │   ├── PayrollSummary.tsx
│   │   ├── PayslipCard.tsx
│   │   └── PayrollChart.tsx
│   ├── org-chart/
│   │   ├── OrgTree.tsx
│   │   ├── OrgNode.tsx
│   │   └── OrgControls.tsx
│   └── ui/
│       ├── DataTable.tsx
│       ├── StatCard.tsx
│       ├── Badge.tsx
│       ├── Tabs.tsx
│       ├── Stepper.tsx               # Onboarding wizard
│       └── FileUpload.tsx
├── store/
│   ├── index.ts
│   ├── api/
│   │   ├── employeesApi.ts          # RTK Query
│   │   ├── attendanceApi.ts
│   │   ├── leaveApi.ts
│   │   └── payrollApi.ts
│   └── slices/
│       ├── authSlice.ts
│       └── uiSlice.ts
├── hooks/
│   ├── useAuth.ts
│   ├── usePermissions.ts
│   └── useDebounce.ts
├── lib/
│   ├── utils.ts
│   ├── constants.ts
│   ├── permissions.ts
│   └── export.ts                    # CSV/PDF export
├── types/
│   ├── employee.ts
│   ├── attendance.ts
│   ├── leave.ts
│   ├── payroll.ts
│   └── user.ts
└── styles/
    └── globals.css
```

## Component Tree

```
<DashboardLayout>
  <Sidebar>
    <Logo />
    <NavGroup label="Main">
      <NavItem icon={Dashboard} />
      <NavItem icon={People} />       {/* Employees */}
      <NavItem icon={Clock} />        {/* Attendance */}
      <NavItem icon={Calendar} />     {/* Leave */}
    </NavGroup>
    <NavGroup label="Finance">
      <NavItem icon={Currency} />     {/* Payroll */}
    </NavGroup>
    <NavGroup label="Organization">
      <NavItem icon={Hierarchy} />    {/* Org Chart */}
      <NavItem icon={Chart} />        {/* Reports */}
    </NavGroup>
  </Sidebar>
  <Content>
    <Header>
      <Breadcrumbs />
      <Notifications />
      <UserMenu />
    </Header>
    <PageContent />
  </Content>
</DashboardLayout>

DashboardPage:
  <StatRow>
    <StatCard title="Total Employees" />
    <StatCard title="Present Today" />
    <StatCard title="On Leave" />
    <StatCard title="New Hires" />
  </StatRow>
  <ChartRow>
    <EmployeeGrowthChart />
    <DepartmentDistribution />
  </ChartRow>
  <RecentActivity />

EmployeeListPage:
  <EmployeeTable />                   {/* AG Grid — search, filter, sort, group by department */}
  <AddEmployeeButton />

EmployeeDetailPage:
  <ProfileHeader />
  <Tabs>
    <Tab label="Personal"><PersonalInfoForm /></Tab>
    <Tab label="Employment"><EmploymentForm /></Tab>
    <Tab label="Documents"><EmployeeDocuments /></Tab>
    <Tab label="Leave"><LeaveHistory /></Tab>
    <Tab label="Attendance"><AttendanceLog /></Tab>
  </Tabs>

LeavePage:
  <LeaveBalanceCard />               {/* Remaining days */}
  <LeaveForm />                       {/* Apply */}
  <LeaveCalendar />                   {/* Team calendar */}
  <LeaveHistory />                    {/* Past applications */}
```

## Data Model

```prisma
model Employee {
  id             String     @id @default(cuid())
  employeeId     String     @unique // EMP-001
  firstName      String
  lastName       String
  email          String     @unique
  phone          String?
  dateOfBirth    DateTime?
  gender         String?
  maritalStatus  String?
  nationality    String?

  // Employment
  jobTitle       String
  departmentId   String
  department     Department @relation(fields: [departmentId], references: [id])
  positionId     String?
  position       Position?  @relation(fields: [positionId], references: [id])
  managerId      String?
  manager        Employee?  @relation("ManagerSubordinates", fields: [managerId], references: [id])
  subordinates   Employee[] @relation("ManagerSubordinates")
  hireDate       DateTime
  employmentType EmploymentType @default(FULL_TIME)
  status         EmployeeStatus @default(ACTIVE)

  // Payroll
  salary         Decimal?
  bankAccount    String?
  taxId          String?

  // Auth
  userId         String?    @unique
  user           User?

  // Relations
  attendance     Attendance[]
  leaveRequests  LeaveRequest[]
  documents      Document[]
  createdAt      DateTime   @default(now())
  updatedAt      DateTime   @updatedAt
}

enum EmploymentType { FULL_TIME PART_TIME CONTRACT INTERN }
enum EmployeeStatus { ACTIVE TERMINATED ON_LEAVE RESIGNED }

model Department {
  id        String     @id @default(cuid())
  name      String     @unique
  code      String     @unique
  headId    String?
  head      Employee?  @relation("DepartmentHead", fields: [headId], references: [id])
  employees Employee[]
  createdAt DateTime   @default(now())
}

model Attendance {
  id         String   @id @default(cuid())
  employeeId String
  employee   Employee @relation(fields: [employeeId], references: [id])
  date       DateTime
  clockIn    DateTime?
  clockOut   DateTime?
  status     AttendanceStatus @default(PRESENT)
  note       String?
}

enum AttendanceStatus { PRESENT ABSENT LATE HALF_DAY WFH }

model LeaveRequest {
  id          String       @id @default(cuid())
  employeeId  String
  employee    Employee     @relation(fields: [employeeId], references: [id])
  leaveType   LeaveType
  startDate   DateTime
  endDate     DateTime
  duration    Int          // Days
  reason      String?
  status      LeaveStatus  @default(PENDING)
  approvedBy  String?
  approver    Employee?    @relation("LeaveApprover", fields: [approvedBy], references: [id])
  createdAt   DateTime     @default(now())
  updatedAt   DateTime     @updatedAt
}

enum LeaveType { ANNUAL SICK PERSONAL MATERNITY PATERNITY UNPAID }
enum LeaveStatus { PENDING APPROVED REJECTED CANCELLED }

model LeaveBalance {
  id         String     @id @default(cuid())
  employeeId String
  employee   Employee   @relation(fields: [employeeId], references: [id])
  leaveType  LeaveType
  total      Int
  used       Int        @default(0)
  year       Int
  @@unique([employeeId, leaveType, year])
}

model Document {
  id         String   @id @default(cuid())
  name       String
  type       String   // passport, degree, contract
  url        String
  employeeId String
  employee   Employee @relation(fields: [employeeId], references: [id])
  uploadedAt DateTime @default(now())
}
```

## Route Structure

| Route | Component | Auth | Role | Description |
|-------|-----------|------|------|-------------|
| `/login` | LoginPage | — | — | Login |
| `/` | DashboardPage | Required | All | HR dashboard |
| `/employees` | EmployeeListPage | Required | All | Employee directory |
| `/employees/new` | EmployeeOnboardingPage | Required | HR, Admin | Onboarding |
| `/employees/:id` | EmployeeDetailPage | Required | All | Employee profile |
| `/attendance` | AttendancePage | Required | All | Clock in/out, my log |
| `/attendance/report` | AttendanceReportPage | Required | HR, Admin | Department report |
| `/leave` | LeavePage | Required | All | Apply, view balance |
| `/leave/approvals` | LeaveApprovalPage | Required | Manager, HR | Approve/reject |
| `/payroll` | PayrollPage | Required | All | My payslips |
| `/payroll/admin` | PayrollAdminPage | Required | HR, Admin | All payroll |
| `/reports` | ReportsPage | Required | HR, Admin | Analytics |
| `/org-chart` | OrgChartPage | Required | All | Organization chart |
| `/admin/departments` | DepartmentsPage | Required | Admin | Manage departments |
| `/admin/positions` | PositionsPage | Required | Admin | Manage positions |
| `/admin/audit-log` | AuditLogPage | Required | Admin | Audit trail |

## Key Implementation Considerations

- Use AG Grid with `rowModelType: 'serverSide'` for employee table (1000+ rows)
- Implement RTK Query with `tagTypes` for cache invalidation (approve leave → refetch balances)
- Use optimistic updates for leave approval (update status immediately, rollback on error)
- Implement attendance clock-in with geolocation verification
- Leave balance calculation: query with raw SQL for accurate year-to-date totals
- Org chart: implement tree layout with custom SVG or canvas rendering
- File upload: use presigned URLs or multipart upload with progress tracking
- Export: generate CSV on client-side, PDF server-side with Puppeteer
- Implement proper timezone handling for attendance and leave dates

## Performance Considerations

- AG Grid virtual scrolling for employee list (handles 10k+ rows)
- Lazy load org chart (heavy rendering for large orgs)
- Cache department and position lists (low churn data)
- Debounce attendance clock-out to prevent duplicate entries
- Use `React.memo` for EmployeeRow, LeaveCard, AttendanceRow
- Implement pagination for leave history and attendance logs
- Bundle analysis — AG Grid enterprise features add size, import only needed modules
- Use `Suspense` with RTK Query for data-dependent dashboard widgets

## Deployment Strategy

1. **Vite build** → Vercel or Netlify for frontend
2. **Node/Express API** or **Next.js API routes** for backend
3. **Neon.tech** or **Supabase** for Postgres database
4. **AWS S3 or Cloudinary** for document storage
5. **Environment variables**: database URL, storage keys, auth secret
6. **CI/CD**: GitHub Actions → lint → test → build → deploy
7. **Sentry** for error tracking

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Data model, workflows, permission matrix | 2 |
| Foundation | Vite, Redux Toolkit, RTK Query, auth, layouts | 2 |
| Employees | Directory, profile, CRUD, AG Grid configuration | 3 |
| Attendance | Clock in/out, daily log, reports | 2 |
| Leave | Apply, approve, balance, calendar | 2.5 |
| Payroll | Payroll summary, payslip view, calculations | 2 |
| Org Chart | Tree rendering, interactivity, search | 1.5 |
| Dashboard | Metrics, charts, recent activity | 1.5 |
| Admin | Department/position management, audit log | 1.5 |
| Polish | Responsive, permissions, error states, export | 1.5 |
| Deploy | CI/CD, document storage, environment config | 1 |
| **Total** | | **~11-16 days** |

## Learning Resources

- [AG Grid React Documentation](https://www.ag-grid.com/react-data-grid/)
- [RTK Query Guide](https://redux-toolkit.js.org/rtk-query/overview)
- [Prisma with Postgres](https://www.prisma.io/docs/orm/overview/databases/postgresql)
- [React Hook Form + Zod](https://react-hook-form.com/get-started#IntegratingwithUIlibraries)
- [Recharts Documentation](https://recharts.org/en-US/guide)
- [React Router v6](https://reactrouter.com/en/main/start/tutorial)
- [Auth.js with React](https://authjs.dev/reference/react)
