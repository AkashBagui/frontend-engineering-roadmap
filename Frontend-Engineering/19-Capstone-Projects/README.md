# Capstone Projects

## Overview

This section contains 11 carefully designed capstone projects that simulate real-world software engineering challenges. Each project progressively builds on the concepts learned in earlier modules, covering everything from basic React components to complex state management, real-time collaboration, AI integration, and multi-tenant SaaS architectures.

These projects are not tutorials — they are blueprints that require you to architect, design, and implement production-grade features. They bridge the gap between understanding isolated concepts and shipping complete applications.

## Purpose

| Purpose | Description |
|---------|-------------|
| Consolidation | Apply every concept from previous modules in a single coherent application |
| Portfolio building | Create production-quality projects that demonstrate real engineering skill |
| Decision making | Practice choosing between libraries, patterns, and architectures |
| Problem solving | Encounter and resolve open-ended engineering challenges |
| Code quality | Enforce clean architecture, testing, and performance best practices |
| Collaboration prep | Simulate team-scale codebases with proper separation of concerns |

## What These Projects Combine

- **React & Next.js** — Pages, layouts, server components, client components, SSR, ISR, streaming
- **State Management** — Redux Toolkit, Zustand, RTK Query, TanStack Query
- **UI & Styling** — Tailwind CSS, Framer Motion, component composition
- **Authentication** — Auth.js (NextAuth), RBAC, session management
- **Payments** — Stripe integration, webhooks, subscription lifecycle
- **Real-time** — WebSockets, Socket.io, CRDT, collaborative editing
- **AI/ML** — Vercel AI SDK, streaming responses, prompt engineering
- **Data Layer** — SQL schemas, Prisma ORM, API design, caching
- **Performance** — Code splitting, lazy loading, memoization, virtualization
- **Testing** — Unit, integration, E2E testing strategies
- **DevOps** — Deployment, CI/CD, monitoring, environment management

## Difficulty Progression

```
01-Personal-Portfolio     ───  ●○○○○  Beginner
02-Blog-Platform          ───  ●●○○○  Beginner-Intermediate
03-E-Commerce             ───  ●●●○○  Intermediate
04-CRM-Dashboard          ───  ●●●○○  Intermediate
05-HRMS                   ───  ●●●●○  Intermediate-Advanced
06-Hospital-Management    ───  ●●●●○  Intermediate-Advanced
07-Project-Management     ───  ●●●●○  Intermediate-Advanced
08-AI-Chat                ───  ●●●●○  Advanced
09-Google-Docs-Clone      ───  ●●●●●  Advanced
10-Figma-Clone            ───  ●●●●●  Advanced
11-SaaS-Dashboard         ───  ●●●●●  Advanced
```

## Suggested Order

| Order | Project | Rationale |
|-------|---------|-----------|
| 1 | Personal Portfolio | Single-page, low complexity, introduces Next.js + Tailwind + animations |
| 2 | Blog Platform | Adds data layer, ISR, SEO, search — still manageable scope |
| 3 | E-Commerce | State management at scale, payments, auth — major complexity jump |
| 4 | CRM Dashboard | Dashboard UI patterns, kanban, complex component trees |
| 5 | Project Management | Combines kanban + Gantt + team features with dnd |
| 6 | HRMS | Data-heavy grids, org charts, complex filtering |
| 7 | Hospital Management | Multi-role system (admin, doctor, patient) with scheduling |
| 8 | AI Chat | Streaming, AI SDK, prompt handling — unique paradigm |
| 9 | SaaS Dashboard | Multi-tenancy, billing, feature flags, RBAC — highest architectural complexity |
| 10 | Google Docs Clone | Real-time collaboration, CRDT, operational transform |
| 11 | Figma Clone | Canvas rendering, vector editing, custom rendering pipeline |

> **Alternative path**: Build 1→2→3→8→11→9→4→7→5→6→10, mixing domains to keep motivation high.

## How to Approach Large Projects

### Phase 1: Planning (1-2 days)
- Write a one-page spec describing what the app does
- Draw system architecture diagram
- Design database schema (entities, relationships, indexes)
- Plan component tree and route structure
- Choose libraries — justify each choice

### Phase 2: Foundation (2-3 days)
- Initialize project with proper tooling (ESLint, Prettier, TypeScript strict mode)
- Set up project structure (feature folders, shared components, utils)
- Configure Tailwind with design tokens
- Set up database schema and migrations
- Create API layer (tRPC, REST, or GraphQL)
- Implement authentication

### Phase 3: Core Features (5-10 days)
- Build one feature at a time, end-to-end
- Start with the critical path (e.g., for e-commerce: browse → cart → checkout)
- Add loading, empty, error states for every component
- Write tests alongside features

### Phase 4: Polish (2-3 days)
- Add animations and transitions
- Responsive design pass
- Performance audit (Lighthouse, bundle analysis)
- Error boundaries and error tracking
- Accessibility review

### Phase 5: Ship (1-2 days)
- Environment configuration (dev, staging, production)
- CI/CD pipeline
- Database migrations in production
- Monitoring and logging
- Documentation (README, API docs, deployment guide)

## Portfolio-Building Tips

### Quality over Quantity
- **3 polished projects** > 10 half-finished ones
- Complete the critical path fully before starting a new project
- Every feature should have loading, empty, error, and edge-case states

### Show Your Process
- Include architecture diagrams in READMEs
- Document key decisions (why Zustand over Redux, why ISR over SSR)
- Show before/after performance metrics
- Link to live demo + GitHub repo from each README

### Stand Out Features
- Dark/light theme with persistence
- Responsive design (mobile-first)
- Accessibility (keyboard navigation, screen reader support)
- Performance optimizations (lighthouse score 90+)
- Tests (unit + integration + E2E)
- CI/CD badge in README

### Presentation
- Deploy every project (Vercel for Next.js, Render for Node APIs)
- Use custom domains
- Write a clean, detailed README with screenshots
- Record a 2-minute demo video

### Code Quality Signals (what recruiters look for)
- TypeScript strict mode — no `any`
- Feature-based folder structure
- Custom hooks encapsulating logic
- Proper error handling (not just `console.error`)
- Tests that cover real user flows
- Clean git history with conventional commits

## Project Duration Estimates

| Project | Estimated Hours | Estimated Days (full-time) |
|---------|----------------|---------------------------|
| Personal Portfolio | 25-35 | 4-6 |
| Blog Platform | 40-60 | 6-10 |
| E-Commerce | 80-120 | 12-20 |
| CRM Dashboard | 60-90 | 10-15 |
| HRMS | 70-100 | 11-16 |
| Hospital Management | 80-110 | 12-18 |
| Project Management | 70-100 | 11-16 |
| AI Chat | 50-70 | 8-12 |
| Google Docs Clone | 90-130 | 14-22 |
| Figma Clone | 100-150 | 16-25 |
| SaaS Dashboard | 100-140 | 16-24 |

## Tech Stack Summary

| Project | Framework | State | Database | Auth | Special |
|---------|-----------|-------|----------|------|---------|
| Portfolio | Next.js 14 | Context | — | — | Framer Motion |
| Blog | Next.js 14 | Context | Postgres/MDX | — | ISR, MDX |
| E-Commerce | Next.js 14 | Redux Toolkit | Postgres | Auth.js | Stripe |
| CRM | React + Vite | Zustand | Postgres | Auth.js | Kanban |
| HRMS | React + Vite | RTK Query | Postgres | Auth.js | AG Grid |
| Hospital | Next.js 14 | Zustand | Postgres | Auth.js | Scheduling |
| PM | React + Vite | Redux Toolkit | Postgres | Auth.js | dnd-kit |
| AI Chat | Next.js 14 | Context | Postgres | Auth.js | AI SDK |
| Docs Clone | React + Vite | Zustand | Postgres | Auth.js | Yjs, Socket.io |
| Figma Clone | React + Vite | Zustand | Postgres | — | Canvas API |
| SaaS | Next.js 14 | Redux Toolkit | Postgres | Auth.js | Multi-tenant |
