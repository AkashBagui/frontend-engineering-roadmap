# Calendar

**Difficulty:** Medium | **Est. Time:** 45–60 min

---

## Problem Statement

Build a calendar application that supports month, week, and day views. Users can navigate between months/weeks/days, create events, and view events on the calendar grid.

---

## Requirements

### Functional
- [ ] **Month view**: grid of days, shows events per day
- [ ] **Week view**: horizontal days with time slots (hourly rows)
- [ ] **Day view**: single day with detailed time slots
- [ ] Navigate between months/weeks (previous / next / today)
- [ ] Create an event with title, date, start time, end time
- [ ] Click on a day/time slot to create event
- [ ] Click on existing event to view/edit/delete
- [ ] Events persist in LocalStorage
- [ ] View switching (Month ↔ Week ↔ Day)

### Non-Functional
- [ ] Performant with 100+ events per month
- [ ] Accessible (keyboard navigation, ARIA labels)
- [ ] Responsive (stack days on mobile for week view)

---

## Component Architecture

```
App
├── CalendarHeader
│   ├── Navigation (Prev | Today | Next)
│   ├── Title ("July 2026")
│   └── ViewSwitcher (Month | Week | Day)
├── CalendarGrid
│   ├── MonthView
│   │   ├── DayHeaders (Sun Mon Tue …)
│   │   └── WeekRow (×4-6)
│   │       └── DayCell (×7)
│   │           └── EventChip (×N)
│   ├── WeekView
│   │   ├── TimeColumn (00:00 – 23:00)
│   │   └── DayColumn (×7)
│   │       └── TimeSlot (×24)
│   │           └── EventBlock
│   └── DayView
│       ├── TimeColumn
│       └── TimeSlot (×24)
│           └── EventBlock
├── EventModal
│   ├── TitleInput
│   ├── DatePicker
│   ├── TimeInputs (start, end)
│   └── Save / Delete buttons
└── CalendarContext (provides view, events, navigation)
```

---

## Date Handling

Use **date-fns** (lightweight, tree-shakeable) for all date operations:

```js
import { format, addMonths, subMonths, startOfMonth, endOfMonth, startOfWeek, endOfWeek, eachDayOfInterval, isSameMonth, isSameDay, isToday } from 'date-fns';
```

Avoid moment.js (heavy) and Day.js (good alternative if date-fns unavailable).

---

## State Management

```js
const [currentDate, setCurrentDate] = useState(new Date());  // visible month/week/day
const [view, setView] = useState('month');  // 'month' | 'week' | 'day'
const [events, setEvents] = useState([]);   // array of event objects
const [selectedEvent, setSelectedEvent] = useState(null);  // modal state
const [isCreating, setIsCreating] = useState(false);

// Event shape
{
  id: 'evt_1',
  title: 'Team standup',
  date: '2026-07-15',
  startTime: '09:00',
  endTime: '09:30',
  color: '#4f46e5'
}
```

---

## Implementation Steps

1. Set up date utilities with date-fns
2. Build MonthView: generate day grid from `startOfWeek(startOfMonth(currentDate))` to `endOfWeek(endOfMonth(currentDate))`
3. Build WeekView: generate 7 day columns, each with 24 time slots
4. Build DayView: single day column with 24 time slots
5. Add navigation (prev/next changes currentDate by ±1 month/week/day)
6. Add view switching (preserve currentDate, change rendering logic)
7. Implement event creation: click empty slot → EventModal → add to state
8. Implement event display: position EventChip in month cell, EventBlock in time slot
9. Implement event click → view/edit/delete
10. Persist events to LocalStorage
11. Handle empty states, overflow (show "+N more" for month view)
12. Add keyboard navigation (arrow keys to move between days)

---

## Code Snippets

### Month View Day Grid Generation

```js
function getMonthDays(date) {
  const monthStart = startOfMonth(date);
  const monthEnd = endOfMonth(date);
  const calStart = startOfWeek(monthStart, { weekStartsOn: 0 });
  const calEnd = endOfWeek(monthEnd, { weekStartsOn: 0 });
  return eachDayOfInterval({ start: calStart, end: calEnd });
}
```

### Event Positioning in Week View

```js
function getEventStyle(event) {
  const [startH, startM] = event.startTime.split(':').map(Number);
  const [endH, endM] = event.endTime.split(':').map(Number);
  const top = (startH * 60 + startM) * (SLOT_HEIGHT / 60);
  const height = ((endH * 60 + endM) - (startH * 60 + startM)) * (SLOT_HEIGHT / 60);
  return { position: 'absolute', top: `${top}px`, height: `${height}px` };
}
```

### "+N More" Overflow

```js
const MAX_VISIBLE = 3;
const visibleEvents = dayEvents.slice(0, MAX_VISIBLE);
const overflowCount = dayEvents.length - MAX_VISIBLE;

{visibleEvents.map(evt => <EventChip key={evt.id} event={evt} />)}
{overflowCount > 0 && <MoreButton>+{overflowCount} more</MoreButton>}
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| Event spanning midnight | Use full Date objects; split event display across days if needed |
| Overlapping events in week/day view | Calculate overlap width; stack events side by side |
| Month with 6 weeks vs 5 | Calculate dynamically; render 6 rows when needed |
| Empty month/week/day | Show "No events" message |
| DST transitions | date-fns handles DST correctly; test edge cases |
| Very long event title | Truncate with ellipsis; show tooltip on hover |

---

## Bonus Features

- [ ] **Drag & drop events** to reschedule (dnd-kit or native DnD)
- [ ] **Recurring events** (daily, weekly, monthly)
- [ ] **Color-coded calendars** (work, personal, etc.)
- [ ] **Mini-calendar** sidebar (small month view for quick navigation)
- [ ] **Week numbers** display
- [ ] **Export to iCalendar / Google Calendar**

---

## Common Interview Questions

1. **How do you calculate the number of days to show in month view?** — Get the start of the week containing the 1st, and the end of the week containing the last day. Walk the interval.

2. **How do you handle overlapping events in week view?** — Group events by overlap, divide column width evenly among overlapping events, set left offset based on position in group.

3. **How to persist events efficiently?** — Store as JSON array in LocalStorage. For large datasets, use IndexedDB. Debounce writes.

4. **How would you add drag-to-create (click drag on empty slot)?** — Listen for mousedown on slot → mousemove to expand → mouseup to finalize → show modal with pre-filled times.

---

## Resources

- [date-fns docs](https://date-fns.org/)
- [Building a calendar grid (CSS Grid)](https://css-tricks.com/snippets/css/complete-guide-grid/)
