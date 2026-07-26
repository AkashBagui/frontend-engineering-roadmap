# Hospital Management System

## Project Overview

Build a comprehensive hospital management system with patient registration, appointment scheduling, doctor management, billing, pharmacy inventory, and lab reports. This project involves multi-role access (admin, doctor, patient, receptionist), complex scheduling algorithms, and HIPAA-compliant data handling patterns.

## Learning Objectives

- Multi-role system design (admin, doctor, patient, receptionist, lab tech)
- Appointment scheduling with conflict detection
- Patient record management with medical history
- Billing and invoice generation
- Pharmacy inventory management
- Lab report workflow (order → sample → results)
- Real-time notifications (appointment reminders, lab results)
- Searchable data tables with advanced filtering
- Responsive design for hospital staff (tablets, desktops)

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| Next.js 14 | Framework | SSR for SEO, API routes, App Router |
| TypeScript | Language | Type safety for medical data |
| Zustand | State management | Lightweight, scalable for multi-role state |
| TanStack Query | Server state | Caching, real-time refetch, mutations |
| Prisma + Postgres | Database | Type-safe ORM, migrations, relations |
| Tailwind CSS | Styling | Rapid UI development, responsive |
| Auth.js | Authentication | Role-based access, session management |
| React Hook Form + Zod | Forms | Complex medical form validation |
| Recharts | Charts | Dashboard analytics |
| date-fns | Dates | Timezone-aware date handling |

## Feature List

### MVP Features
- Patient registration and management
- Patient medical history and records
- Doctor directory with specialties
- Appointment scheduling with calendar
- Doctor availability management
- Appointment booking (patient self-service + receptionist)
- Billing and invoice generation
- Prescription management
- Pharmacy inventory tracking
- Lab test catalog and order management
- Lab results entry and viewing
- Dashboard with key metrics (patients, appointments, revenue)
- Role-based dashboards (admin, doctor, receptionist)

### Advanced Features
- Online appointment booking with payment
- Video consultation integration
- E-prescription with QR code
- Bed management (ward, ICU, occupancy)
- Insurance claim processing
- SMS/email notifications (reminders, results)
- Doctor rating and feedback
- Medicine interaction checker
- Radiology/PACS image viewer
- Multi-branch / multi-hospital support
- Audit log for compliance

## Architecture Diagram

```
src/
├── app/
│   ├── layout.tsx
│   ├── page.tsx                    # Role-based redirect
│   ├── (auth)/
│   │   ├── login/
│   │   │   └── page.tsx
│   │   └── register/
│   │       └── page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx              # Sidebar + header
│   │   ├── page.tsx                # Dashboard home
│   │   ├── patients/
│   │   │   ├── page.tsx            # Patient list
│   │   │   └── [id]/
│   │   │       ├── page.tsx        # Patient overview
│   │   │       ├── records/
│   │   │       │   └── page.tsx    # Medical records
│   │   │       ├── appointments/
│   │   │       │   └── page.tsx    # Patient appointments
│   │   │       └── billing/
│   │   │           └── page.tsx    # Patient billing
│   │   ├── appointments/
│   │   │   ├── page.tsx            # Calendar view
│   │   │   ├── new/
│   │   │   │   └── page.tsx        # New appointment
│   │   │   └── [id]/
│   │   │       └── page.tsx        # Appointment detail
│   │   ├── doctors/
│   │   │   ├── page.tsx            # Doctor directory
│   │   │   └── [id]/
│   │   │       ├── page.tsx        # Doctor profile
│   │   │       └── schedule/
│   │   │           └── page.tsx    # Schedule management
│   │   ├── pharmacy/
│   │   │   ├── page.tsx            # Inventory list
│   │   │   └── medicines/
│   │   │       ├── page.tsx
│   │   │       └── [id]/
│   │   │           └── page.tsx
│   │   ├── lab/
│   │   │   ├── page.tsx            # Lab dashboard
│   │   │   ├── tests/
│   │   │   │   └── page.tsx        # Test catalog
│   │   │   └── results/
│   │   │       └── page.tsx        # Results entry
│   │   ├── billing/
│   │   │   ├── page.tsx            # Billing list
│   │   │   └── [id]/
│   │   │       └── page.tsx        # Invoice detail
│   │   └── reports/
│   │       └── page.tsx            # Analytics reports
│   └── api/
│       ├── patients/
│       ├── appointments/
│       ├── doctors/
│       ├── billing/
│       ├── pharmacy/
│       ├── lab/
│       └── dashboard/
├── components/
│   ├── layout/
│   │   ├── Sidebar.tsx             # Role-based navigation
│   │   ├── Header.tsx
│   │   ├── MobileNav.tsx
│   │   └── RoleGuard.tsx           # Permission wrapper
│   ├── patients/
│   │   ├── PatientTable.tsx
│   │   ├── PatientCard.tsx
│   │   ├── PatientForm.tsx
│   │   ├── MedicalHistory.tsx
│   │   └── VitalsForm.tsx
│   ├── appointments/
│   │   ├── CalendarView.tsx
│   │   ├── AppointmentCard.tsx
│   │   ├── AppointmentForm.tsx
│   │   ├── TimeSlotPicker.tsx
│   │   └── DoctorSchedule.tsx
│   ├── billing/
│   │   ├── InvoiceTable.tsx
│   │   ├── InvoiceCard.tsx
│   │   ├── InvoiceForm.tsx
│   │   └── PaymentForm.tsx
│   ├── pharmacy/
│   │   ├── InventoryTable.tsx
│   │   ├── MedicineCard.tsx
│   │   ├── DispenseForm.tsx
│   │   └── LowStockAlert.tsx
│   ├── lab/
│   │   ├── TestOrderForm.tsx
│   │   ├── ResultsEntry.tsx
│   │   ├── LabReport.tsx
│   │   └── TestCatalog.tsx
│   ├── dashboard/
│   │   ├── StatCard.tsx
│   │   ├── AppointmentsChart.tsx
│   │   ├── RevenueChart.tsx
│   │   ├── PatientDemographics.tsx
│   │   └── RecentActivity.tsx
│   └── ui/
│       ├── Calendar.tsx
│       ├── DataTable.tsx
│       ├── Badge.tsx
│       ├── Modal.tsx
│       └── Combobox.tsx
├── stores/
│   ├── useAuthStore.ts
│   ├── useUIStore.ts
│   └── useAppointmentStore.ts
├── hooks/
│   ├── usePatients.ts
│   ├── useAppointments.ts
│   ├── useBilling.ts
│   └── useDashboard.ts
├── lib/
│   ├── prisma.ts
│   ├── auth.ts
│   ├── utils.ts
│   ├── scheduling.ts               # Conflict detection
│   └── billing.ts                  # Calculations
└── types/
    ├── patient.ts
    ├── appointment.ts
    ├── billing.ts
    └── medicine.ts
```

## Component Tree

```
<DashboardLayout>
  <Sidebar>
    <RoleNav>                       {/* Shows items based on role */}
      <NavItem />*                  {/* Dashboard, Patients, Appointments, Doctors, Pharmacy, Lab, Billing, Reports */}
    </RoleNav>
  </Sidebar>
  <MainContent>
    <Header />
    <PageContent>
      {children}
    </PageContent>
  </MainContent>
</DashboardLayout>

DashboardPage:
  <StatCards>
    <StatCard title="Today's Appointments" />
    <StatCard title="New Patients" />
    <StatCard title="Pending Lab Results" />
    <StatCard title="Today's Revenue" />
  </StatCards>
  <Row>
    <AppointmentsChart />          {/* Line/bar chart */}
    <RevenueChart />               {/* Daily revenue */}
  </Row>
  <Row>
    <UpcomingAppointments />
    <RecentPatients />
  </Row>

AppointmentsPage:
  <CalendarView>                   {/* Month/week/day toggle */}
    <CalendarDay />*               {/* With appointment dots */}
    <TimeSlot />*                  {/* Available slots */}
  </CalendarView>
  <AppointmentList>                {/* Sidebar list */}
    <AppointmentCard />*           {/* Patient, doctor, time, status */}
    <AppointmentForm />            {/* Modal */}
  </AppointmentList>

DoctorSchedulePage:
  <WeekView>
    <DayColumn />*                 {/* Hour slots */}
    <AppointmentBlock />*          {/* Overlapping appointments */}
  </WeekView>
  <AvailabilityForm />             {/* Set available hours */}
```

## Route Structure

| Route | Component | Roles | Description |
|-------|-----------|-------|-------------|
| `/login` | LoginPage | All | Login |
| `/` | DashboardPage | All | Role-based dashboard |
| `/patients` | PatientListPage | Admin, Doctor, Receptionist | Patient directory |
| `/patients/new` | PatientFormPage | Admin, Receptionist | Register patient |
| `/patients/[id]` | PatientDetailPage | All (own) | Patient profile |
| `/patients/[id]/records` | MedicalRecordsPage | Admin, Doctor | Medical history |
| `/patients/[id]/billing` | PatientBillingPage | Admin, Billing | Patient invoices |
| `/appointments` | AppointmentsPage | All | Calendar view |
| `/appointments/new` | NewAppointmentPage | Admin, Receptionist, Patient | Book appointment |
| `/appointments/[id]` | AppointmentDetailPage | All (relevant) | Appointment info |
| `/doctors` | DoctorsPage | All | Doctor directory |
| `/doctors/[id]` | DoctorProfilePage | All | Doctor profile |
| `/doctors/[id]/schedule` | DoctorSchedulePage | Admin, Doctor | Manage schedule |
| `/pharmacy` | PharmacyPage | Admin, Pharmacist | Inventory |
| `/pharmacy/medicines/new` | AddMedicinePage | Admin, Pharmacist | Add medicine |
| `/lab` | LabPage | Admin, Lab Tech | Lab dashboard |
| `/lab/tests/new` | NewTestOrderPage | Doctor | Order lab test |
| `/lab/results` | LabResultsPage | Lab Tech | Enter results |
| `/billing` | BillingPage | Admin, Billing | All invoices |
| `/billing/[id]` | InvoiceDetailPage | Admin, Billing | Invoice detail |
| `/reports` | ReportsPage | Admin | Analytics |

## Database Schema

```prisma
enum Role { SUPER_ADMIN ADMIN DOCTOR RECEPTIONIST LAB_TECH PHARMACIST PATIENT }

model User {
  id       String @id @default(cuid())
  email    String @unique
  password String
  role     Role
  profile  Profile?
  patient  Patient?
  doctor   Doctor?
  staff    Staff?
}

model Profile {
  id        String @id @default(cuid())
  userId    String @unique
  user      User   @relation(fields: [userId], references: [id])
  firstName String
  lastName  String
  phone     String?
  avatar    String?
  address   String?
}

model Patient {
  id             String     @id @default(cuid())
  userId         String     @unique
  user           User       @relation(fields: [userId], references: [id])
  dateOfBirth    DateTime
  bloodGroup     String?
  allergies      String?
  medicalHistory Json?      // Array of conditions
  appointments   Appointment[]
  prescriptions  Prescription[]
  labOrders      LabOrder[]
  invoices       Invoice[]
  createdAt      DateTime   @default(now())
}

model Doctor {
  id            String       @id @default(cuid())
  userId        String       @unique
  user          User         @relation(fields: [userId], references: [id])
  specialization String
  licenseNumber String       @unique
  qualifications String[]
  experience    Int
  consultationFee Decimal
  availableDays  String[]    // ["MON", "TUE", ...]
  appointments   Appointment[]
  schedules      DoctorSchedule[]
  createdAt      DateTime     @default(now())
}

model DoctorSchedule {
  id        String   @id @default(cuid())
  doctorId  String
  doctor    Doctor   @relation(fields: [doctorId], references: [id])
  dayOfWeek Int      // 0-6
  startTime String   // "09:00"
  endTime   String   // "17:00"
  slotDuration Int   // minutes
  isActive  Boolean  @default(true)
}

model Appointment {
  id          String           @id @default(cuid())
  patientId   String
  patient     Patient          @relation(fields: [patientId], references: [id])
  doctorId    String
  doctor      Doctor           @relation(fields: [doctorId], references: [id])
  date        DateTime
  startTime   String           // "14:30"
  endTime     String           // "15:00"
  status      AppointmentStatus @default(SCHEDULED)
  type        AppointmentType
  reason      String?
  notes       String?
  invoice     Invoice?
  createdAt   DateTime         @default(now())
}

enum AppointmentStatus { SCHEDULED CONFIRMED IN_PROGRESS COMPLETED CANCELLED NO_SHOW }
enum AppointmentType { IN_PERSON VIDEO CONSULTATION FOLLOW_UP EMERGENCY }

model Invoice {
  id            String   @id @default(cuid())
  patientId     String
  patient       Patient  @relation(fields: [patientId], references: [id])
  appointmentId String?  @unique
  appointment   Appointment? @relation(fields: [appointmentId], references: [id])
  items         InvoiceItem[]
  total         Decimal
  paidAmount    Decimal   @default(0)
  status        InvoiceStatus @default(PENDING)
  dueDate       DateTime
  createdAt     DateTime @default(now())
}

enum InvoiceStatus { PENDING PAID PARTIAL OVERDUE CANCELLED }

model InvoiceItem {
  id        String  @id @default(cuid())
  invoiceId String
  invoice   Invoice @relation(fields: [invoiceId], references: [id])
  name      String
  quantity  Int
  rate      Decimal
  amount    Decimal
}

model Medicine {
  id          String   @id @default(cuid())
  name        String
  genericName String
  category    String
  manufacturer String?
  unit        String   // tablet, ml, mg
  price       Decimal
  stock       Int
  reorderLevel Int     @default(10)
  expiryDate  DateTime?
  createdAt   DateTime @default(now())
}

model Prescription {
  id          String   @id @default(cuid())
  patientId   String
  patient     Patient  @relation(fields: [patientId], references: [id])
  doctorId    String
  doctor      Doctor   @relation(fields: [doctorId], references: [id])
  medicines   Json     // [{medicineId, dosage, duration, instructions}]
  notes       String?
  createdAt   DateTime @default(now())
}

model LabTest {
  id       String @id @default(cuid())
  name     String
  category String
  price    Decimal
  turnaround Int   // hours
  orders   LabOrder[]
}

model LabOrder {
  id         String    @id @default(cuid())
  patientId  String
  patient    Patient   @relation(fields: [patientId], references: [id])
  doctorId   String
  doctor     Doctor    @relation(fields: [doctorId], references: [id])
  testId     String
  test       LabTest   @relation(fields: [testId], references: [id])
  status     LabStatus @default(ORDERED)
  result     String?   // JSON or text
  resultFile String?   // PDF/Image URL
  notes      String?
  orderedAt  DateTime  @default(now())
  completedAt DateTime?
}

enum LabStatus { ORDERED SAMPLE_COLLECTED IN_PROGRESS COMPLETED CANCELLED }
```

## Key Implementation Considerations

- Appointment scheduling: implement conflict detection algorithm (check doctor + time overlap)
- Use TanStack Query with optimistic updates for appointment booking
- Implement role-based middleware in Next.js for route protection
- Medical records: store as JSON for flexibility, index key fields for search
- Billing: calculate totals with taxes and discounts, generate invoice numbers
- Pharmacy: implement low-stock alerts and expiry date tracking
- Lab orders: implement status workflow (ordered → sample → in_progress → completed)
- Use date-fns for timezone-aware scheduling (hospital may serve multiple timezones)
- Implement proper audit logging for HIPAA compliance (who accessed what record)

## Performance Considerations

- Lazy load calendar components (month view renders all days)
- Virtualize patient list and invoice table for large datasets
- Cache doctor schedules and medicine catalog (low churn data)
- Use TanStack Query `staleTime` strategically — patient list 30s, medicine catalog 5min
- Debounce search inputs for patient and medicine lookup
- Prefetch today's appointments on dashboard
- Use `next/image` for profile photos and medical images
- Bundle split by role — admin loads heavier components than receptionist

## Deployment Strategy

1. **Vercel** for Next.js app (SSR + API routes)
2. **Neon.tech** or **Supabase** for Postgres database
3. **Cloudinary** for medical images and documents
4. **Twilio** or **SendGrid** for SMS/email notifications
5. **Environment variables**: database URL, auth secret, notification keys
6. **CI/CD**: GitHub Actions → lint → test → build → deploy
7. **Sentry** for error tracking (HIPAA-compliant logging)

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Data model, role matrix, user flows | 2 |
| Foundation | Next.js setup, auth, roles, layouts | 2 |
| Patients | Registration, records, medical history | 2.5 |
| Appointments | Calendar, scheduling, conflict detection | 3 |
| Doctors | Profiles, schedule management | 1.5 |
| Billing | Invoices, payments, receipt generation | 2 |
| Pharmacy | Inventory, dispensing, low-stock alerts | 2 |
| Lab | Test catalog, orders, results entry | 2 |
| Dashboard | Role-based dashboards, charts | 2 |
| Notifications | Email/SMS reminders, alerts | 1 |
| Polish | Responsive, permissions, error states | 1.5 |
| Deploy | CI/CD, environment config, audit logging | 1 |
| **Total** | | **~12-18 days** |

## Learning Resources

- [Next.js App Router](https://nextjs.org/docs)
- [Zustand for State Management](https://docs.pmnd.rs/zustand/getting-started/introduction)
- [TanStack Query](https://tanstack.com/query/latest/docs/react/overview)
- [Prisma with Postgres](https://www.prisma.io/docs/orm/overview/databases/postgresql)
- [Auth.js Role Management](https://authjs.dev/reference/nextjs)
- [date-fns Documentation](https://date-fns.org/)
- [React Hook Form + Zod](https://react-hook-form.com/get-started#IntegratingwithUIlibraries)
- [HIPAA Compliance Basics](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)
