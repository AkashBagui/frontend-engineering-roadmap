# Frontend Career Roadmap

## The Career Ladder

```mermaid
graph TD
    L1[Junior Engineer<br>0-2 years] --> L2[Mid-Level Engineer<br>2-5 years]
    L2 --> L3[Senior Engineer<br>5-8 years]
    L3 --> L4[Staff Engineer<br>8-12 years]
    L3 --> L4A[Lead Engineer<br>8-12 years]
    L4 --> L5[Principal Engineer<br>12+ years]
    L4A --> L5

    subgraph IC Track [Individual Contributor]
        L1
        L2
        L3
        L4
        L5
    end

    subgraph Mgmt Track [Engineering Management]
        L4A[Engineering Manager]
        L5A[Director of Engineering]
        L4A --> L5A
    end
```

## Level Breakdown

### Junior Engineer (0–2 years)

```
┌─────────────────────────────────────────────────────────────┐
│  "I can build features with supervision"                    │
├─────────────────────────────────────────────────────────────┤
│  Core Skills:                                               │
│  ✓ HTML, CSS, JavaScript fundamentals                       │
│  ✓ One framework (React/Vue) — basic components             │
│  ✓ Git (add, commit, push, pull, branches)                  │
│  ✓ Basic responsive design                                  │
│  ✓ Consume REST APIs (fetch/axios)                          │
│  ✓ Debug with DevTools (Console, Network, Elements)         │
│                                                             │
│  Needs help with:                                           │
│  ✗ Architecture decisions                                   │
│  ✗ Performance optimization                                 │
│  ✗ Complex state management                                 │
│  ✗ Testing strategies                                       │
└─────────────────────────────────────────────────────────────┘
```

### Mid-Level Engineer (2–5 years)

```
┌─────────────────────────────────────────────────────────────┐
│  "I can own features end-to-end"                            │
├─────────────────────────────────────────────────────────────┤
│  Core Skills:                                               │
│  ✓ Deep framework knowledge (hooks, lifecycle, patterns)    │
│  ✓ State management (Redux, Zustand, TanStack Query)        │
│  ✓ TypeScript — generics, utility types                     │
│  ✓ Unit/integration testing                                 │
│  ✓ Build tooling (Vite/Webpack)                             │
│  ✓ CI/CD basics                                             │
│  ✓ Performance basics (lazy loading, bundle analysis)       │
│  ✓ Cross-browser compatibility                              │
│                                                             │
│  Starts contributing to:                                    │
│  • Code review                                              │
│  • Technical discussions                                    │
│  • Estimating effort                                        │
└─────────────────────────────────────────────────────────────┘
```

### Senior Engineer (5–8 years)

```
┌─────────────────────────────────────────────────────────────┐
│  "I lead technical direction for a domain"                  │
├─────────────────────────────────────────────────────────────┤
│  Core Skills:                                               │
│  ✓ Architecture design (component trees, data flow)         │
│  ✓ Advanced performance (Core Web Vitals, profiling)        │
│  ✓ Accessibility mastery (WCAG 2.1 AA/AAA)                  │
│  ✓ Testing strategy (what to test, how to mock)             │
│  ✓ Mentoring juniors/mid-level                              │
│  ✓ Cross-team collaboration                                 │
│  ✓ Technical decision-making (RFCs, ADRs)                   │
│  ✓ Security-conscious development                           │
│                                                             │
│  Outputs:                                                   │
│  • Design documents                                         │
│  • Code review culture                                      │
│  • Technical talks / knowledge sharing                      │
└─────────────────────────────────────────────────────────────┘
```

### Staff Engineer (8–12 years)

```
┌─────────────────────────────────────────────────────────────┐
│  "I set technical strategy across multiple teams"           │
├─────────────────────────────────────────────────────────────┤
│  Core Skills:                                               │
│  ✓ Cross-team architecture influence                        │
│  ✓ Deep specialization (perf, a11y, infra)                  │
│  ✓ Build vs buy decisions                                   │
│  ✓ Org-wide standards (design system, testing conventions)  │
│  ✓ Unblocking teams — technical and organizational          │
│  ✓ Hiring and interviewing                                  │
│                                                             │
│  Outputs:                                                   │
│  • Multi-quarter technical roadmaps                         │
│  • Engineering-wide initiatives                             │
│  • Platform/tool creation                                   │
└─────────────────────────────────────────────────────────────┘
```

### Principal Engineer (12+ years)

```
┌─────────────────────────────────────────────────────────────┐
│  "I shape the engineering vision of the company"            │
├─────────────────────────────────────────────────────────────┤
│  Core Skills:                                               │
│  ✓ Industry influence (talks, OSS, writing)                 │
│  ✓ Company-wide technical strategy                          │
│  ✓ Extreme cross-domain knowledge (backend, infra, product) │
│  ✓ Anticipates problems 2-3 years ahead                     │
│  ✓ Drives cultural change                                   │
│                                                             │
│  Outputs:                                                   │
│  • Company technical vision                                 │
│  • Architectural decisions that last years                  │
│  • Growing other Staff+ engineers                           │
└─────────────────────────────────────────────────────────────┘
```

## Salary Ranges (USD, Approximate)

| Level | India (₹) | US (Global) | Remote (US-based) |
|-------|-----------|-------------|-------------------|
| **Junior** | ₹3L–₹8L | $60k–$90k | $50k–$80k |
| **Mid** | ₹8L–₹20L | $90k–$140k | $80k–$130k |
| **Senior** | ₹20L–₹40L | $140k–$220k | $130k–$200k |
| **Staff** | ₹40L–₹70L | $200k–$350k | $180k–$300k |
| **Principal** | ₹70L–₹1Cr+ | $300k–$600k+ | $250k–$500k |

*Note: Total compensation includes base salary + stock + bonus. FAANG+ companies typically pay at the higher end of each range.*

## Timeline Expectations

```mermaid
gantt
    title Frontend Career Progression Timeline
    dateFormat  YYYY
    axisFormat  %Y
    
    section Junior (0-2)
    Learn fundamentals                 :a1, 2024, 2y
    
    section Mid-Level (2-5)
    Build features independently       :a2, after a1, 3y
    
    section Senior (5-8)
    Lead technical direction           :a3, after a2, 3y
    
    section Staff (8-12)
    Cross-team technical strategy      :a4, after a3, 4y
    
    section Principal (12+)
    Company-wide technical vision      :a5, after a4, 4y
```

## Skills by Level

```mermaid
graph LR
    subgraph "Junior"
        J1[HTML/CSS/JS]
        J2[Single Framework]
        J3[Git Basics]
    end
    subgraph "Mid"
        M1[TypeScript]
        M2[State Mgmt]
        M3[Testing]
        M4[Build Tools]
    end
    subgraph "Senior"
        S1[Architecture]
        S2[Perf/Accesibility]
        S3[Mentoring]
        S4[Cross-team]
    end
    subgraph "Staff"
        SF1[Strategy]
        SF2[Org Impact]
        SF3[Specialization]
        SF4[Hiring]
    end
    subgraph "Principal"
        P1[Industry Influence]
        P2[Long-term Vision]
        P3[Cultural Change]
    end
    
    J1 --> M1
    J2 --> M2
    J3 --> M3
    M3 --> S1
    M4 --> S2
    S1 --> SF1
    S3 --> SF4
    SF1 --> P1
    SF2 --> P2
    SF3 --> P3
```

## How to Level Up

### Junior → Mid
- Build 3–5 complete features end-to-end
- Learn TypeScript properly (not just `: any`)
- Write tests for everything you build
- Understand the build tooling (Vite config, environment variables)
- Give your first code review

### Mid → Senior
- Lead the architecture for a medium-sized feature
- Mentor a junior developer
- Write a technical design document (RFC/ADR)
- Deep-dive into performance (use Lighthouse, Profiler)
- Speak at a team demo or internal tech talk

### Senior → Staff
- Span multiple teams — solve problems that don't belong to one team
- Build tooling/platforms that amplify other engineers
- Develop a deep specialization (accessibility, performance, infra)
- Drive the hiring process (design interview questions, run loops)

### Staff → Principal
- Write tech strategy documents that influence the company direction
- Represent the company at industry conferences
- Build open-source projects or internal platforms used org-wide
- Develop other Staff+ engineers through sponsorship

## Key Takeaway

> Career growth is not about years — it's about **impact scope**. Junior impacts their own ticket. Senior impacts their team. Staff impacts the org. Principal impacts the industry. Always ask: "Who benefits from my work?"
