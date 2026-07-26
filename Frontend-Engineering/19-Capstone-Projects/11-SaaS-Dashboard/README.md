# SaaS Dashboard

## Project Overview

Build a multi-tenant SaaS dashboard with onboarding flows, team management, subscription billing, analytics, feature flags, RBAC (Role-Based Access Control), and white-labeling. This is the most architecturally complex project, combining patterns from all previous projects into a production-grade B2B SaaS application.

## Learning Objectives

- Multi-tenant architecture (row-level security, tenant isolation)
- SaaS billing lifecycle (free trial → subscription → upgrade/downgrade → cancel)
- Stripe subscription management with webhooks
- Feature flags for gradual rollout and per-plan features
- RBAC with granular permissions
- White-labeling (custom branding per tenant)
- Team management with invitations
- Onboarding flows (wizard, progress tracking)
- Analytics and usage tracking
- Webhook handling and idempotency

## Tech Stack

| Technology | Purpose | Why |
|-----------|---------|-----|
| Next.js 14 | Framework | SSR, API routes, App Router |
| TypeScript | Language | Type safety across tenant data |
| Redux Toolkit | Client state | Complex state, normalized entities |
| Prisma + Postgres | Database | Row-level security, migrations |
| Stripe | Billing | Subscriptions, invoices, webhooks |
| Auth.js | Authentication | OAuth, credentials, session |
| Tailwind CSS | Styling | Rapid UI, white-label theming |
| Recharts | Charts | Analytics dashboards |
| TanStack Query | Server state | Caching, optimistic updates |
| React Hook Form + Zod | Forms | Complex validation |
| next-themes | Theming | White-label CSS variables |

## Feature List

### MVP Features
- Multi-tenant account creation (workspace signup)
- User authentication (email/password + OAuth)
- Role-based access (Owner, Admin, Member, Viewer)
- Tenant onboarding wizard (profile, invite team, set up billing)
- Dashboard with key metrics (MRR, active users, usage)
- User management (invite, roles, suspend, remove)
- Subscription management (view plan, upgrade, downgrade, cancel)
- Billing portal (invoices, payment method, billing history)
- Feature flags per plan (free → pro → enterprise tiers)
- Settings (tenant profile, branding, security)

### Advanced Features
- White-labeling (custom domain, logo, colors, favicon)
- Usage-based billing (API calls, storage, seats)
- API key management for integrations
- Audit log (who did what, when)
- Team roles with granular permissions
- SCIM provisioning for enterprise SSO
- Multi-region data residency
- Custom reporting builder
- Webhook endpoints for integrations
- SSO (SAML/OIDC) for enterprise plans

## Full Architecture Diagram

```
┌────────────────────────────────────────────────────────────────────────────┐
│                        SAAS DASHBOARD ARCHITECTURE                         │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  ┌──────────────────────────────────────────────────────────────────┐      │
│  │                     FRONTEND (Next.js 14)                        │      │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │      │
│  │  │ Public      │  │ App (Auth)  │  │ Admin       │              │      │
│  │  │ Landing     │  │ Dashboard   │  │ SuperAdmin  │              │      │
│  │  └─────────────┘  └─────────────┘  └─────────────┘              │      │
│  │         │                │                  │                     │      │
│  │         ▼                ▼                  ▼                     │      │
│  │  ┌─────────────────────────────────────────────────────┐         │      │
│  │  │              Shared Components Layer                │         │      │
│  │  │  Tables, Charts, Forms, Modals, Layouts, Nav        │         │      │
│  │  └─────────────────────────────────────────────────────┘         │      │
│  │         │                │                  │                     │      │
│  │         ▼                ▼                  ▼                     │      │
│  │  ┌─────────────────────────────────────────────────────┐         │      │
│  │  │           State Layer (Redux + TanStack Query)       │         │      │
│  │  └─────────────────────────────────────────────────────┘         │      │
│  └──────────────────────────────────────────────────────────────────┘      │
│                            │                                                │
│                            ▼                                                │
│  ┌──────────────────────────────────────────────────────────────────┐      │
│  │                       API LAYER (Next.js API)                    │      │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────────┐    │      │
│  │  │ Tenant   │ │ Auth     │ │ Billing  │ │ Feature Flags    │    │      │
│  │  │ API      │ │ API      │ │ API      │ │ API              │    │      │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────────────┘    │      │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────────┐    │      │
│  │  │ Team     │ │ Settings │ │ Analytics│ │ Webhooks         │    │      │
│  │  │ API      │ │ API      │ │ API      │ │ API              │    │      │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────────────┘    │      │
│  └──────────────────────────────────────────────────────────────────┘      │
│                            │                                                │
│         ┌──────────────────┼─────────────────────┐                         │
│         ▼                  ▼                     ▼                         │
│  ┌────────────┐   ┌──────────────┐   ┌──────────────────┐                 │
│  │  Postgres  │   │  Stripe      │   │  Redis/Upstash   │                 │
│  │  (Prisma)  │   │  (Billing)   │   │  (Cache/Queue)   │                 │
│  └────────────┘   └──────────────┘   └──────────────────┘                 │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

## Feature List Breakdown

### Plans & Pricing
| Feature | Free | Pro ($29/mo) | Enterprise (custom) |
|---------|------|--------------|---------------------|
| Team members | 3 | 10 | Unlimited |
| Projects | 1 | Unlimited | Unlimited |
| Storage | 100MB | 10GB | Custom |
| API calls/mo | 1K | 50K | Unlimited |
| White-label | — | — | Yes |
| SSO | — | — | Yes |
| Support | Email | Priority | Dedicated |

## Architecture Diagram

```
src/
├── app/
│   ├── layout.tsx                      # Root layout
│   ├── page.tsx                        # Landing/redirect
│   ├── (public)/
│   │   ├── layout.tsx                  # Public layout (no sidebar)
│   │   ├── page.tsx                    # Landing page
│   │   ├── pricing/
│   │   │   └── page.tsx
│   │   ├── blog/
│   │   │   └── page.tsx
│   │   └── docs/
│   │       └── page.tsx
│   ├── (auth)/
│   │   ├── login/
│   │   │   └── page.tsx
│   │   ├── register/
│   │   │   └── page.tsx
│   │   └── forgot-password/
│   │       └── page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx                  # Auth check + sidebar
│   │   ├── page.tsx                    # Main dashboard
│   │   ├── onboarding/
│   │   │   ├── page.tsx                # Onboarding wizard
│   │   │   ├── profile/
│   │   │   │   └── page.tsx
│   │   │   ├── team/
│   │   │   │   └── page.tsx
│   │   │   └── billing/
│   │   │       └── page.tsx
│   │   ├── team/
│   │   │   ├── page.tsx                # Team management
│   │   │   ├── members/
│   │   │   │   └── page.tsx
│   │   │   └── roles/
│   │   │       └── page.tsx
│   │   ├── billing/
│   │   │   ├── page.tsx                # Subscription
│   │   │   ├── invoices/
│   │   │   │   └── page.tsx
│   │   │   └── payment-methods/
│   │   │       └── page.tsx
│   │   ├── settings/
│   │   │   ├── page.tsx                # General settings
│   │   │   ├── branding/
│   │   │   │   └── page.tsx            # White-label (enterprise)
│   │   │   ├── security/
│   │   │   │   └── page.tsx
│   │   │   └── api-keys/
│   │   │       └── page.tsx
│   │   ├── analytics/
│   │   │   └── page.tsx
│   │   ├── feature-flags/
│   │   │   └── page.tsx
│   │   └── audit-log/
│   │       └── page.tsx
│   ├── (admin)/
│   │   ├── layout.tsx                  # Super admin layout
│   │   ├── page.tsx                    # Super admin dashboard
│   │   ├── tenants/
│   │   │   └── page.tsx                # All tenants
│   │   ├── plans/
│   │   │   └── page.tsx                # Plan management
│   │   └── feature-flags/
│   │       └── page.tsx                # Global feature flags
│   └── api/
│       ├── auth/
│       ├── tenants/
│       ├── team/
│       ├── billing/
│       ├── stripe/                     # Webhooks
│       ├── analytics/
│       ├── feature-flags/
│       └── settings/
├── components/
│   ├── layout/
│   │   ├── AppSidebar.tsx              # Tenant-aware sidebar
│   │   ├── TopBar.tsx
│   │   ├── TenantSwitcher.tsx          # Multi-tenant (optional)
│   │   └── OnboardingBanner.tsx
│   ├── onboarding/
│   │   ├── OnboardingWizard.tsx
│   │   ├── StepProfile.tsx
│   │   ├── StepTeam.tsx
│   │   ├── StepBilling.tsx
│   │   └── StepComplete.tsx
│   ├── team/
│   │   ├── MemberTable.tsx
│   │   ├── InviteForm.tsx
│   │   ├── RoleSelect.tsx
│   │   └── PermissionsMatrix.tsx
│   ├── billing/
│   │   ├── PlanCard.tsx
│   │   ├── SubscriptionStatus.tsx
│   │   ├── InvoiceTable.tsx
│   │   ├── PaymentMethodForm.tsx
│   │   └── BillingHistory.tsx
│   ├── settings/
│   │   ├── BrandingForm.tsx            # Logo, colors, domain
│   │   ├── SecurityForm.tsx
│   │   └── ApiKeyManager.tsx
│   ├── analytics/
│   │   ├── MetricCard.tsx
│   │   ├── MRRChart.tsx
│   │   ├── ActiveUsersChart.tsx
│   │   ├── UsageChart.tsx
│   │   └── TopEventsTable.tsx
│   ├── feature-flags/
│   │   ├── FlagToggle.tsx
│   │   ├── FlagTable.tsx
│   │   └── FlagForm.tsx
│   ├── admin/
│   │   ├── TenantTable.tsx
│   │   ├── TenantDetail.tsx
│   │   ├── PlanEditor.tsx
│   │   └── GlobalSettings.tsx
│   └── ui/
│       ├── DataTable.tsx
│       ├── Modal.tsx
│       ├── Badge.tsx
│       ├── Tabs.tsx
│       ├── Card.tsx
│       └── Skeleton.tsx
├── store/
│   ├── index.ts
│   ├── slices/
│   │   ├── tenantSlice.ts
│   │   ├── billingSlice.ts
│   │   ├── featuresSlice.ts
│   │   └── uiSlice.ts
│   └── api/
│       ├── tenantApi.ts
│       ├── teamApi.ts
│       ├── billingApi.ts
│       └── analyticsApi.ts
├── hooks/
│   ├── useTenant.ts
│   ├── usePermissions.ts
│   ├── useFeatureFlag.ts
│   └── useBilling.ts
├── lib/
│   ├── stripe.ts
│   ├── prisma.ts
│   ├── auth.ts
│   ├── permissions.ts                  # Permission definitions
│   ├── feature-flags.ts                # Flag evaluation logic
│   ├── tenant-context.ts               # Tenant identification
│   ├── white-label.ts                  # Theme generation
│   └── utils.ts
├── middleware.ts                        # Tenant resolution + auth
├── types/
│   ├── tenant.ts
│   ├── billing.ts
│   ├── team.ts
│   └── feature-flag.ts
└── styles/
    └── globals.css                     # CSS variables for white-label
```

## Component Tree

```
<DashboardLayout>
  <AppSidebar>
    <TenantInfo />                      {/* Logo, name */}
    <NavSection label="Main">
      <NavItem icon={Home} />           {/* Dashboard */}
      <NavItem icon={Analytics} />      {/* Analytics */}
    </NavSection>
    <NavSection label="Team">
      <NavItem icon={Users} />          {/* Team */}
      <NavItem icon={Settings} />       {/* Settings */}
    </NavSection>
    <NavSection label="Billing">
      <NavItem icon={CreditCard} />     {/* Subscription */}
      <NavItem icon={FileText} />       {/* Invoices */}
    </NavSection>
    <BottomActions>
      <TenantSwitcher />
      <UserMenu />
    </BottomActions>
  </AppSidebar>
  <MainContent>
    <TopBar>
      <Breadcrumbs />
      <OnboardingBanner />              {/* Show if incomplete */}
      <HelpButton />
      <UserAvatar />
    </TopBar>
    <PageContent>{children}</PageContent>
  </MainContent>
</DashboardLayout>

OnboardingWizard:
  <Stepper steps={4} currentStep={1} />
  <StepProfile>
    <ProfileForm />
  </StepProfile>
  <StepTeam>
    <InviteForm />
    <MemberList />
  </StepTeam>
  <StepBilling>
    <PlanComparison>
      <PlanCard plan="free" />
      <PlanCard plan="pro" selected />
      <PlanCard plan="enterprise" />
    </PlanComparison>
    <PaymentForm />                     {/* Stripe Elements */}
  </StepBilling>
  <StepComplete>
    <SuccessMessage />
    <LaunchButton />
  </StepComplete>

DashboardPage:
  <MetricRow>
    <MetricCard title="MRR" value="$12,340" change="+12%" />
    <MetricCard title="Active Users" value="1,234" change="+5%" />
    <MetricCard title="API Calls" value="234K" change="+18%" />
    <MetricCard title="Seats Used" value="8/10" />
  </MetricRow>
  <ChartRow>
    <MRRChart />                        {/* Monthly Recurring Revenue */}
    <ActiveUsersChart />
  </ChartRow>
  <Row>
    <RecentActivity />
    <TopEventsTable />
  </Row>
```

## Database Schema

```prisma
enum PlanTier { FREE PRO ENTERPRISE }
enum TenantStatus { ACTIVE TRIALING PAST_DUE CANCELLED SUSPENDED }
enum MemberRole { OWNER ADMIN MEMBER VIEWER }

model Tenant {
  id             String        @id @default(cuid())
  name           String
  slug           String        @unique // Used in subdomain/URL
  status         TenantStatus  @default(TRIALING)
  plan           PlanTier      @default(FREE)
  trialEndsAt    DateTime?
  stripeCustomerId String?     @unique
  stripeSubscriptionId String? @unique
  members        TenantMember[]
  settings       TenantSettings?
  createdAt      DateTime      @default(now())
  updatedAt      DateTime      @updatedAt
}

model TenantSettings {
  id          String  @id @default(cuid())
  tenantId    String  @unique
  tenant      Tenant  @relation(fields: [tenantId], references: [id])
  logoUrl     String?
  primaryColor String @default("#6366f1") // White-label
  faviconUrl  String?
  customDomain String? @unique
  timezone    String  @default("UTC")
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}

model User {
  id        String         @id @default(cuid())
  email     String         @unique
  name      String
  password  String?
  image     String?
  tenants   TenantMember[]
  createdAt DateTime       @default(now())
}

model TenantMember {
  id        String     @id @default(cuid())
  tenantId  String
  tenant    Tenant     @relation(fields: [tenantId], references: [id])
  userId    String
  user      User       @relation(fields: [userId], references: [id])
  role      MemberRole @default(MEMBER)
  joinedAt  DateTime   @default(now())
  @@unique([tenantId, userId])
}

model Subscription {
  id                 String   @id @default(cuid())
  tenantId           String   @unique
  tenant             Tenant   @relation(fields: [tenantId], references: [id])
  stripeSubscriptionId String @unique
  stripePriceId      String
  status             String   // active, past_due, canceled
  currentPeriodStart DateTime
  currentPeriodEnd   DateTime
  cancelAtPeriodEnd  Boolean  @default(false)
  trialEndsAt        DateTime?
  createdAt          DateTime @default(now())
  updatedAt          DateTime @updatedAt
}

model Invoice {
  id              String   @id @default(cuid())
  tenantId        String
  tenant          Tenant   @relation(fields: [tenantId], references: [id])
  stripeInvoiceId String   @unique
  amount          Decimal
  currency        String   @default("usd")
  status          String   // paid, open, uncollectible, void
  paidAt          DateTime?
  periodStart     DateTime
  periodEnd       DateTime
  invoiceUrl      String?
  createdAt       DateTime @default(now())
}

model FeatureFlag {
  id          String   @id @default(cuid())
  key         String   @unique // "advanced-analytics"
  name        String
  description String?
  enabled     Boolean  @default(false)
  planTier    PlanTier // Minimum plan to access
  tenantOverrides FeatureFlagOverride[]
  createdAt   DateTime @default(now())
}

model FeatureFlagOverride {
  id            String       @id @default(cuid())
  featureFlagId String
  featureFlag   FeatureFlag  @relation(fields: [featureFlagId], references: [id])
  tenantId      String
  tenant        Tenant       @relation(fields: [tenantId], references: [id])
  enabled       Boolean
  @@unique([featureFlagId, tenantId])
}

model ApiKey {
  id        String   @id @default(cuid())
  name      String
  key       String   @unique
  tenantId  String
  tenant    Tenant   @relation(fields: [tenantId], references: [id])
  lastUsed  DateTime?
  expiresAt DateTime?
  createdAt DateTime @default(now())
}

model AuditLog {
  id        String   @id @default(cuid())
  tenantId  String
  userId    String
  action    String   // "member.invited", "subscription.upgraded"
  details   Json?
  ip        String?
  createdAt DateTime @default(now())
}
```

## Route Structure

| Route | Component | Auth | Role | Description |
|-------|-----------|------|------|-------------|
| `/` | LandingPage | — | — | Marketing page |
| `/pricing` | PricingPage | — | — | Pricing plans |
| `/login` | LoginPage | — | — | Login |
| `/register` | RegisterPage | — | — | Sign up |
| `/onboarding` | OnboardingWizard | Required | Owner | Initial setup |
| `/dashboard` | DashboardPage | Required | All | Main dashboard |
| `/team` | TeamPage | Required | Admin+ | Team management |
| `/team/members` | MemberListPage | Required | Admin+ | Member list |
| `/team/roles` | RolesPage | Required | Owner | Role configuration |
| `/billing` | BillingPage | Required | Admin+ | Subscription |
| `/billing/invoices` | InvoicesPage | Required | Admin+ | Invoice history |
| `/settings` | SettingsPage | Required | Admin+ | General settings |
| `/settings/branding` | BrandingPage | Required | Owner | White-label |
| `/settings/security` | SecurityPage | Required | Admin+ | Security settings |
| `/settings/api-keys` | ApiKeysPage | Required | Admin+ | API key management |
| `/analytics` | AnalyticsPage | Required | All | Usage analytics |
| `/feature-flags` | FeatureFlagsPage | Required | Owner | Feature management |
| `/audit-log` | AuditLogPage | Required | Owner | Activity audit |
| `/admin` | AdminDashboard | Required | Super Admin | Multi-tenant admin |
| `/admin/tenants` | TenantListPage | Required | Super Admin | All tenants |
| `/admin/plans` | PlansPage | Required | Super Admin | Plan management |

## Key Implementation Considerations

- **Multi-tenancy**: use `slug` subdomain or path-based tenant identification, resolve in middleware
- **Row-level security**: all queries filter by `tenantId` — use Prisma middleware or raw SQL
- **Stripe integration**: manage subscription lifecycle via webhooks (customer.subscription.updated, invoice.paid)
- **Feature flags**: evaluate at API level (middleware) and UI level (useFeatureFlag hook)
- **RBAC**: define permissions as a matrix (resource + action), check via middleware and UI conditions
- **White-labeling**: generate CSS variables from tenant settings, inject into layout
- **Onboarding**: track completion stages, redirect incomplete tenants
- **Audit logging**: middleware-based approach — log all mutating API calls

## Performance Considerations

- Cache tenant settings and feature flags in Redis (Upstash)
- Use TanStack Query with `staleTime: 60000` for dashboard metrics
- Lazy load analytics charts (heavy Recharts bundle)
- API key hashing — store hash, not plaintext
- Paginate audit log and invoice history
- Use Next.js ISR for public pages (landing, pricing)
- Tenant context: resolve once in middleware, pass via headers/cookies
- Bundle split by route — admin panel loads separately

## Deployment Strategy

1. **Vercel** — Next.js app, edge middleware for tenant resolution
2. **Neon.tech** — Postgres with row-level security
3. **Stripe** — billing, webhooks, customer portal
4. **Upstash Redis** — caching, rate limiting, session store
5. **AWS Route53** — custom domain per tenant (white-label, enterprise)
6. **SendGrid** — transactional emails (invites, invoices, alerts)
7. **CI/CD**: GitHub Actions → lint → test → build → deploy
8. **Monitoring**: Sentry + Vercel Analytics + Datadog (enterprise)

## Estimated Timeline

| Phase | Tasks | Days |
|-------|-------|------|
| Planning | Multi-tenant architecture, data model, plans | 2 |
| Foundation | Next.js, auth, middleware, tenant resolution | 2 |
| Onboarding | Wizard flow, profile, team invite | 2 |
| Team Management | CRUD, roles, permissions, invite flow | 2 |
| Billing | Stripe subscriptions, pricing page, webhooks | 3 |
| Dashboard | Metrics, charts, real-time data | 2 |
| Feature Flags | Flag management, evaluation, UI controls | 1.5 |
| Settings | General, branding/white-label, security | 1.5 |
| Analytics | Usage tracking, charts, reporting | 2 |
| Admin Panel | Tenant management, plan editor, global flags | 2 |
| Audit Log | Logging, viewing, filtering, export | 1 |
| Polish | Responsive, error states, loading, permissions | 2 |
| Deploy | CI/CD, Stripe webhooks, custom domains | 1.5 |
| **Total** | | **~16-24 days** |

## Learning Resources

- [Multi-Tenant Architecture with Prisma](https://www.prisma.io/docs/orm/prisma-client/queries/crud#multi-tenancy)
- [Stripe Subscriptions Guide](https://stripe.com/docs/billing/subscriptions/overview)
- [Stripe Webhooks Best Practices](https://stripe.com/docs/webhooks)
- [Auth.js Documentation](https://authjs.dev/reference/nextjs)
- [Redux Toolkit](https://redux-toolkit.js.org/)
- [TanStack Query](https://tanstack.com/query/latest/docs/react/overview)
- [Feature Flags Pattern](https://martinfowler.com/articles/feature-toggles.html)
- [RBAC Pattern](https://en.wikipedia.org/wiki/Role-based_access_control)
- [next-themes for White-labeling](https://github.com/pacocoursey/next-themes)
- [Vercel Edge Middleware](https://vercel.com/docs/functions/edge-middleware)
