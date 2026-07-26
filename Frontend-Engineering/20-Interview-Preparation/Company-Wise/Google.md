# Google Frontend Interview Guide

## Interview Process

Google's frontend interview process typically consists of:

1. **Recruiter Screen** (30 min) - Background, experience, expectations
2. **Phone Screen** (45-60 min) - Coding (data structures, algorithms) OR frontend-focused
3. **On-site** (4-5 rounds, 45 min each):
   - Coding (2 rounds) - Algorithms, data structures
   - Frontend Design (1 round) - Web application architecture
   - Googliness / Behavioral (1 round)
   - Technical Deep Dive (1 round) - Past projects, system design

## Focus Areas

### 1. Algorithms & Data Structures
Google emphasizes problem-solving skills over framework knowledge.

**Key topics:**
- Arrays, strings, hash maps
- Trees, graphs, recursion
- Dynamic programming
- Sorting, searching
- Time/space complexity analysis

**Example questions:**
```
// Phone screen - find most frequent word in a paragraph
function mostFrequentWord(paragraph: string, banned: string[]): string {
  const words = paragraph.toLowerCase().match(/\w+/g) || [];
  const bannedSet = new Set(banned);
  const freq = new Map<string, number>();
  
  let maxFreq = 0;
  let result = '';
  
  for (const word of words) {
    if (bannedSet.has(word)) continue;
    const count = (freq.get(word) || 0) + 1;
    freq.set(word, count);
    if (count > maxFreq) {
      maxFreq = count;
      result = word;
    }
  }
  
  return result;
}
```

### 2. Frontend System Design

**Common problems:**
- Design Google Docs
- Design Google Search (autocomplete)
- Design Google Maps (rendering tiles)
- Design a real-time collaborative editor

**Evaluation criteria:**
- Requirements gathering
- Component architecture
- Data flow and state management
- Performance considerations
- Accessibility and internationalization

### 3. Googliness (Behavioral)

**What they look for:**
- Comfort with ambiguity
- Collaborative attitude
- Bias for action
- Intellectual humility
- Growth mindset

**Sample questions:**
- Tell me about a time you disagreed with your manager
- How would you help a struggling teammate?
- Describe a project that failed and what you learned
- How do you handle conflicting priorities?

## Coding Round Patterns

**Pattern 1: DOM Manipulation**
```javascript
// Implement a function that debounces search input
// and displays suggestions from an API
function createAutocomplete(inputElement, suggestionContainer, fetchFn) {
  let debounceTimer;
  
  inputElement.addEventListener('input', (e) => {
    clearTimeout(debounceTimer);
    const query = e.target.value;
    
    if (query.length < 2) {
      suggestionContainer.innerHTML = '';
      return;
    }
    
    debounceTimer = setTimeout(async () => {
      const suggestions = await fetchFn(query);
      renderSuggestions(suggestions);
    }, 300);
  });
}

// Implement a virtual scrolling list
class VirtualScroller {
  constructor(container, items, itemHeight) {
    this.container = container;
    this.items = items;
    this.itemHeight = itemHeight;
    this.visibleItems = Math.ceil(container.clientHeight / itemHeight) + 2;
    this.init();
  }
  
  init() {
    this.container.style.overflow = 'auto';
    this.container.style.position = 'relative';
    this.container.addEventListener('scroll', () => this.render());
    this.render();
  }
  
  render() {
    const scrollTop = this.container.scrollTop;
    const startIndex = Math.floor(scrollTop / this.itemHeight);
    
    this.container.innerHTML = '';
    const fragment = document.createDocumentFragment();
    
    for (let i = startIndex; i < startIndex + this.visibleItems && i < this.items.length; i++) {
      const item = document.createElement('div');
      item.style.position = 'absolute';
      item.style.top = `${i * this.itemHeight}px`;
      item.style.height = `${this.itemHeight}px`;
      item.textContent = this.items[i];
      fragment.appendChild(item);
    }
    
    this.container.appendChild(fragment);
  }
}
```

## Preparation Strategy

### 3 months before:
- **Months 1-2:** LeetCode (medium) - focus on arrays, strings, trees, DP
- **Month 3:** Frontend system design, past projects
- **Weekly:** 2-3 coding problems + 1 system design

### 1 month before:
- Mock interviews (Pramp, interviewing.io)
- Review past projects with STAR format
- Practice whiteboarding (or Google Docs coding)

### Week before:
- Review your prepared stories
- Light practice (1 problem/day)
- Research Google's products

## Tips from Interviewees

**Coding:**
- Start with brute force, then optimize
- Explain your thought process continuously
- Test with example inputs before calling it complete
- Handle edge cases: empty input, duplicates, large values
- Write clean, readable code (Google values code quality)

**Design:**
- Ask clarifying questions upfront
- Start broad, then go deep on specific components
- Discuss trade-offs: "We could use WebSockets for real-time, but SSE is simpler"
- Consider scale: "For 10 users this works, for 10M we need..."

**General:**
- Google loves data-driven decisions
- Show intellectual honesty: admit what you don't know
- Demonstrate collaboration: "I would work with the backend team to..."
- Stay calm and methodical
- It's okay to take a moment to think before answering

**Common mistakes to avoid:**
- Not asking clarifying questions
- Jumping to solution without exploring alternatives
- Forgetting to handle edge cases
- Not communicating while coding
- Being defensive when receiving hints
