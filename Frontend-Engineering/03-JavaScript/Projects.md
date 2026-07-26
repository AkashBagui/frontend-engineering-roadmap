# JavaScript Projects

---

## 1. Calculator

**Requirements:** Basic arithmetic (+, -, *, /, %), decimal support, clear/reset, keyboard support, display with scroll, error handling (division by zero).

**Learning Objectives:**
- DOM manipulation and event handling
- String/number conversion
- State management (current value, operator, display)
- Keyboard events

**Step-by-Step Guide:**
1. Create HTML structure: display area + button grid (numbers, operators, equals, clear)
2. Style with CSS Grid for button layout
3. Select DOM elements in JS
4. Handle number button clicks — build current input string
5. Handle operator clicks — store first operand and operator, reset display
6. Implement equals — parse stored values, evaluate, handle edge cases (chaining operations)
7. Handle clear/reset — reset all state
8. Add decimal point — prevent multiple decimals
9. Handle keyboard events — map `keydown` events to button actions
10. Handle division by zero — show "Error" message
11. Add backspace support
12. Refactor into clean functions

---

## 2. Todo App

**Requirements:** Add/delete/edit tasks, mark complete, filter (All/Active/Completed), localStorage persistence, clear completed.

**Learning Objectives:**
- CRUD operations on arrays
- localStorage API
- Event delegation
- Array methods (map, filter, findIndex)
- Form handling

**Step-by-Step Guide:**
1. Set up HTML: input field, add button, task list, filter buttons
2. Style with clean, minimal design
3. Initialize todos array from localStorage or empty
4. Render function — map todos to DOM elements
5. Handle add — prevent empty, create todo object (id, text, completed), update array and render
6. Handle delete — use event delegation, remove by id
7. Handle toggle completed — toggle boolean in object
8. Handle edit — double-click to enter edit mode (replace with input)
9. Implement filter buttons — filter array before rendering
10. Add "Clear Completed" button — remove completed from array
11. Persist to localStorage on every change
12. Add remaining count display

---

## 3. Weather App

**Requirements:** Search by city name, display temp/humidity/wind/description, weather icons, 5-day forecast, error handling, loading state, responsive.

**Learning Objectives:**
- Fetch API and async/await
- Working with external APIs (OpenWeatherMap)
- Error handling (network errors, invalid city)
- Loading states and UX patterns

**Step-by-Step Guide:**
1. Get free API key from OpenWeatherMap
2. Build HTML: search input, current weather card, forecast cards
3. Style with gradient backgrounds based on weather
4. Create `fetchWeather(city)` function using async/await
5. Extract relevant data (temp, humidity, description, icon code)
6. Display current weather with icon from OpenWeatherMap URL
7. Create `fetchForecast(city)` for 5-day data (3-hour intervals → daily)
8. Display forecast in cards
9. Handle errors: invalid city → "City not found", network error → "Check connection"
10. Add loading spinner while fetching
11. Handle empty input — don't fetch
12. Add geolocation button for current location
13. Add recent searches dropdown

---

## 4. Notes App

**Requirements:** Create/edit/delete notes, Markdown support (optional), search/filter, color coding, pin notes, auto-save, localStorage.

**Learning Objectives:**
- ContentEditable or textarea handling
- Debounced auto-save
- Advanced localStorage patterns
- Search/filter implementation
- Drag and drop for reordering

**Step-by-Step Guide:**
1. HTML layout: sidebar (list) + main area (editor)
2. Style with card-based layout
3. Initialize notes from localStorage
4. Note object: { id, title, content, color, pinned, createdAt, updatedAt }
5. Display note list sorted by pinned then updatedAt
6. Click note → load into editor
7. Editor with title input and content textarea
8. Implement "New Note" button — create note with placeholder
9. Auto-save with debounce (500ms delay)
10. Delete note with confirmation
11. Color picker — change note background color
12. Search bar — filter notes by title/content
13. Pin/unpin toggle
14. Export notes as JSON
15. Prevent XSS — escape HTML in content

---

## 5. Quiz App

**Requirements:** Multiple choice questions, timer, scoring, progress bar, review answers, different categories, shuffle questions.

**Learning Objectives:**
- State management for complex UI
- Timer implementation with setInterval
- Conditional rendering based on state
- Array shuffling (Fisher-Yates)
- Score calculation and feedback

**Step-by-Step Guide:**
1. Define questions array: { question, options[], correct, category }
2. HTML: start screen, quiz screen, results screen
3. Style with clean card layout
4. Start screen — category selection, difficulty, start button
5. Shuffle questions using Fisher-Yates algorithm
6. Display question with option buttons
7. Track state: currentQuestion, score, answers[], timeLeft
8. Handle answer selection — highlight correct/incorrect, disable after selection
9. Timer — countdown per question (30s)
10. Progress bar — percentage complete
11. Results screen — score, correct/incorrect breakdown, option to retry
12. Add animation for correct/incorrect answers
13. Review mode — show all questions with submitted answers
14. Calculate percentage and grade

---

## 6. Expense Tracker

**Requirements:** Add/delete expenses, categorize, monthly summary, charts (bar/pie), filter/sort, export CSV, budget limits.

**Learning Objectives:**
- Data analysis with array methods
- Chart rendering (Canvas API or library like Chart.js)
- Date manipulation
- CSV generation
- Advanced filtering and sorting

**Step-by-Step Guide:**
1. HTML: form (amount, category, date, description), summary cards, list, chart area
2. Style with dashboard layout
3. Initialize expenses from localStorage
4. Expense object: { id, amount, category, date, description }
5. Add expense form with validation (positive amount, required fields)
6. Display expenses in table with delete button per row
7. Summary cards: total expenses, this month, average per day
8. Category breakdown — group by category, calculate totals
9. Chart using Chart.js (or Canvas API) — pie chart by category, bar chart by month
10. Filter by date range and category
11. Sort by amount, date, category
12. Monthly budget limits — warning when exceeded
13. Export to CSV — generate blob, download link
14. Undo delete option (soft delete with timeout)
