# Startup Frontend Interview Guide

## How Startup Interviews Differ

Startup interviews are fundamentally different from big tech interviews:

| Aspect | Big Tech | Startup |
|--------|----------|---------|
| Process | Structured, 4-6 rounds | Flexible, 2-4 rounds |
| Coding | LeetCode algorithms | Practical building |
| System Design | Large-scale distributed | Product-focused |
| Decision | Multiple interviewers | Founder/CTO often decides |
| Timeline | 4-8 weeks | 1-2 weeks |
| Focus | Theory, fundamentals | Practical, shipping |

## Generalist Expectations

### What Startups Look For:

**1. T-shaped skills - Broad knowledge, deep in one area**
```typescript
// You might be expected to:
// - Build the React frontend (your core)
// - Set up CI/CD pipelines
// - Configure cloud infrastructure
// - Write API endpoints (Node.js)
// - Manage databases (basic queries)
// - Implement authentication
// - Set up monitoring and analytics
```

**2. Ownership and autonomy**
- Startups need self-starters
- You'll own features end-to-end
- Less hand-holding, more responsibility
- Decisions have immediate impact

**3. Speed and pragmatism**
- "Good enough" shipped today > "perfect" next month
- MVP mindset: build, measure, learn
- Make pragmatic trade-offs
- Iterate based on user feedback

```javascript
// Startup approach vs Big Tech approach
// Startup: Ship quickly, iterate
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(setUser);
  }, [userId]);
  
  if (!user) return <Spinner />;
  
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      {/* Ship this, add error handling later */}
    </div>
  );
}
```

## Fast-Paced Environment

### How to Prepare:

**1. Full-stack awareness**
```javascript
// Know how the frontend connects to everything
// Full request flow:
// Browser -> DNS -> CDN -> Load Balancer -> Server -> API -> DB -> Response -> Render

// Be comfortable with:
// - RESTful API design
// - GraphQL basics
// - Authentication (JWT, OAuth, sessions)
// - Database basics (SQL queries)
// - Deployment (Docker, CI/CD)
// - Cloud services (AWS, Vercel, Netlify)
```

**2. Tooling and productivity**
```javascript
// Startups value developers who can:
// - Set up project scaffolding quickly
// - Write efficient build configurations
// - Debug across the stack
// - Automate repetitive tasks
// - Write practical tests (not 100% coverage)

// Example: Quick project setup script
// setup.sh
// npx create-next-app my-app --typescript --tailwind
// npm install @tanstack/react-query zustand react-hook-form
// npm install -D vitest @testing-library/react
```

**3. Communication and async work**
- Startups often have remote/hybrid teams
- Written communication matters
- Ability to work independently
- Comfort with ambiguity

## Smaller Scope, Broader Responsibility

### What This Means:

**Scope:**
- You might be one of 2-3 frontend engineers
- You'll influence the architecture
- Your code directly impacts the product
- You'll interact with customers/users

**Responsibility:**
```
Your responsibilities as a startup frontend engineer:
├── Core: Building UI features
├── Architecture: Component design, state management
├── Performance: Core Web Vitals, bundle size
├── Testing: Critical paths, not everything
├── DevOps: CI/CD, deployments
├── Monitoring: Error tracking, analytics
├── Design: Work with (or as) designer
├── Product: Understand user needs
└── Technical debt: Manage, not eliminate
```

## How to Prepare

### 1. Build a Portfolio Project

Create a full-stack application that demonstrates your abilities:

```typescript
// Ideal portfolio project:
// - Next.js + TypeScript
// - Database (Supabase, Prisma)
// - Authentication (Auth.js)
// - API routes
// - Deployment (Vercel)
// - Basic testing
// - Good UX/UI

// Example: Task management app
// Features: Auth, CRUD, real-time updates, drag-and-drop
```

### 2. Study Common Startup Tech Stacks

```
Vercel Stack:
├── Next.js (React framework)
├── TypeScript
├── Tailwind CSS
├── Prisma (ORM)
├── Postgres (Database)
├── Auth.js (Authentication)
└── Vercel (Deployment)

Alternatives:
├── Remix / SvelteKit / Nuxt
├── Supabase / Firebase
├── tRPC / GraphQL
├── Planetscale / SQLite
└── Railway / Fly.io / Render
```

### 3. Practice Building Fast

```
Time-boxed building practice:
├── Twitter clone: 4 hours
├── Trello board: 3 hours
├── Chat app: 3 hours
├── E-commerce page: 2 hours
└── Auth flow: 1 hour
```

### 4. Prepare for Non-Traditional Questions

**Sample startup interview questions:**
- "How would you build a Twitter clone frontend in 1 week?"
- "What's your approach to deciding when to refactor vs ship?"
- "You're the only frontend engineer. How do you prioritize?"
- "How would you debug a production issue affecting 50% of users?"
- "What technologies would you choose for our product and why?"

## Common Startup Interview Patterns

### Pattern 1: Take-Home Project
```
Timeline: 2-4 hours
You'll be asked to build:
├── A small app with specific features
├── Using their tech stack (or your choice)
├── With production-quality code
└── Deployed or with clear instructions

Tips:
- Complete all required features first
- Add polish if time permits
- Document your decisions
- Include a README
- Handle loading/error states
- Make it responsive
```

### Pattern 2: Pair Programming
```
Format: 45-60 minutes
You'll work on:
├── Building a component together
├── Refactoring existing code
├── Fixing a bug
└── Adding a feature

Tips:
- Think out loud
- Ask clarifying questions
- Accept suggestions gracefully
- Show your debugging process
- It's about collaboration, not perfection
```

### Pattern 3: Technical Discussion
```
Topics covered:
├── Architecture decisions
├── Past projects
├── Technology choices
└── Problem-solving approach

Tips:
- Be opinionated but flexible
- Show you've thought about trade-offs
- Discuss alternatives you considered
- Demonstrate learning from past projects
```

## Red Flags and Green Flags

### Green Flags (Good startup to join):
- Clear product vision
- Revenue/traction (not just ideas)
- Experienced technical leadership
- Reasonable work expectations
- Good engineering practices
- Transparency about challenges

### Red Flags (Be cautious):
- "We work 24/7" culture
- No technical leadership
- Constantly changing requirements
- No testing or quality practices
- Unclear funding situation
- Founder who doesn't respect engineering

## Final Advice

- **Be pragmatic** - Perfection is the enemy of shipped
- **Show adaptability** - Willingness to learn and switch contexts
- **Demonstrate ownership** - You'll own features from idea to production
- **Communicate clearly** - Written and verbal communication is crucial
- **Ask smart questions** - Show you've researched the company
- **Be honest about your limits** - "I haven't done that, but here's how I'd approach it"
- **Emphasize speed with quality** - Fast doesn't mean sloppy
