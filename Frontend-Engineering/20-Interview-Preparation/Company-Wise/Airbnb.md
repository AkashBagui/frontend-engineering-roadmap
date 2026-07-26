# Airbnb Frontend Interview Guide

## Interview Process

Airbnb's frontend interview process:

1. **Recruiter Screen** (30 min) - Background, expectations
2. **Technical Phone Screen** (60 min) - Frontend coding + React
3. **On-site** (4-5 rounds):
   - **Frontend Coding** (45 min) - React component building
   - **Design Review** (45 min) - System design for a feature
   - **Portfolio Review** (45 min) - Deep dive into past projects
   - **Cross-Functional** (45 min) - Working with PMs, designers, backend
   - **Core Values** (45 min) - Behavioral, cultural fit

## Frontend Deep Dives

### What Airbnb Looks For:

**1. Design Thinking**
Airbnb is design-driven. Your technical decisions should consider:
- **User experience first** - How does this affect the user?
- **Visual polish** - Attention to detail, animations, transitions
- **Accessibility** - Inclusive design for all travelers and hosts

**2. Component Architecture**
```typescript
// Airbnb values clean, composable component design

// Good example: Composable date picker
interface DatePickerState {
  startDate: Date | null;
  endDate: Date | null;
  focusedInput: 'start' | 'end' | null;
}

function DateRangePicker({ onChange, minDate, maxDate, ...props }) {
  const [state, dispatch] = useReducer(datePickerReducer, initialState);
  
  return (
    <DatePickerProvider value={{ state, dispatch }}>
      <DateInput label="Check-in" date={state.startDate} />
      <DateInput label="Check-out" date={state.endDate} />
      <Calendar
        minDate={minDate}
        maxDate={maxDate}
        selectedRange={{ start: state.startDate, end: state.endDate }}
      />
    </DatePickerProvider>
  );
}
```

**3. Attention to Detail**
- Smooth animations and transitions
- Skeleton loading states
- Optimistic UI updates
- Error states with recovery
- Empty states with helpful messages

## Design Challenges

### Typical Problems:
- Design a property search with filters
- Design a booking flow (search -> select -> book)
- Design a host dashboard for managing listings
- Design a review and rating system
- Design a wishlist/collections feature

### Sample: Design Search Results Page

**Approach:**
```
1. Requirements:
   - Display listings as cards
   - Map view toggle
   - Filters (price, dates, guests, amenities)
   - Sort (recommended, price, rating)
   - Pagination/Infinite scroll

2. Architecture:
   SearchPage
   ├── SearchBar (location, dates, guests)
   ├── FilterPanel (collapsible on mobile)
   │   ├── PriceRangeSlider
   │   ├── DateFilter
   │   ├── AmenityCheckboxes
   │   └── GuestSelector
   ├── ViewToggle (list/map)
   ├── ResultCount + SortBar
   ├── VirtualizedList
   │   └── ListingCard (×N)
   │       ├── ImageCarousel
   │       ├── PriceBadge
   │       ├── SuperhostBadge
   │       ├── RatingStars
   │       └── WishlistButton
   ├── MapView (hidden unless map selected)
   └── InfiniteScrollTrigger

3. Data Flow:
   URL params (shareable state) 
     → Fetch results from API
     → Cache with SWR
     → Optimistic updates for filters
     → Re-fetch on filter change

4. Performance:
   - Virtualize listing list
   - Lazy load images with blur placeholder
   - Debounce price range updates
   - Lazy load map component
   - Use CSS transforms for animations
```

## Portfolio Review

Airbnb asks you to present a past project in depth (~15 min presentation).

**Presenting your project:**
1. **Context** - What was the problem? Who was the user?
2. **Your role** - What did you contribute specifically?
3. **Technical decisions** - Why did you choose certain approaches?
4. **Challenges** - What was hard? How did you solve it?
5. **Impact** - What were the results (metrics)?
6. **Learnings** - What would you do differently?

**What they evaluate:**
- Technical depth
- Design sensibility
- Communication clarity
- Impact orientation
- Learning ability

## Cultural Fit

Airbnb's core values:
- **Champion the Mission** - Belonging is the core
- **Be a Host** - Hospitality in everything
- **Embrace the Adventure** - Take risks, be curious
- **Every Frame Matters** - Attention to detail

**Sample behavioral questions:**
- Tell me about a time you went above and beyond for a user
- How do you handle ambiguity in product requirements?
- Describe a project where you collaborated with design and product
- Tell me about a time you gave difficult feedback
- How do you balance technical excellence with shipping speed?

## Preparation Strategy

### Technical Preparation:
- **React mastery** - Airbnb uses React extensively; know hooks deeply
- **CSS expertise** - Animations, responsive design, layouts
- **TypeScript** - Airbnb has strong TypeScript practices
- **Performance** - Bundle optimization, rendering performance
- **Testing** - Unit, integration, E2E testing approach

### Design Preparation:
- Study Airbnb's design system (look at their actual site)
- Practice thinking like a designer: "What would make this better for users?"
- Understand accessibility standards
- Be ready to discuss UI animations and transitions

### Portfolio Preparation:
- Select 2-3 projects with significant impact
- Prepare slide deck (or at least talking points)
- Practice the presentation (time yourself)
- Highlight technical decisions and trade-offs
- Include metrics and user impact

## Tips from Interviewees

- **Design sensibility matters** - Airbnb is unique among tech companies for its design focus
- **Be specific** - "We used React Query with stale-while-revalidate for caching" (not just "we cached data")
- **Show empathy** - Discuss how your decisions affected users
- **Know Airbnb's product** - Use the app, understand the flow
- **Attention to detail** - From code quality to how you present
- **Be passionate** - Airbnb values genuine enthusiasm for their mission

## Common Mistakes

- Focusing only on technical aspects, ignoring design
- Not considering mobile experience (Airbnb is mobile-first)
- Weak presentation skills (portfolio review is crucial)
- Not understanding Airbnb's mission and values
- Over-engineering simple solutions
- Not mentioning accessibility
