# Atlassian Frontend Interview Guide

## Interview Process

Atlassian's frontend interview process:

1. **Recruiter Screen** (30 min) - Background, salary expectations
2. **Hiring Manager Call** (45 min) - Experience, project walkthrough
3. **Technical Screen** (60 min) - Coding + frontend concepts
4. **On-site** (4 rounds, 45 min each):
   - **Frontend Coding** - Build a UI component
   - **System Design** - Architecture a feature
   - **Team Collaboration** - Cross-functional scenario
   - **Values & Culture** - Behavioral round

## Frontend Rounds

### 1. Coding Round (Build a UI)

Atlassian problems often relate to their products (Jira, Confluence, Trello).

**Typical problems:**
- Build a Kanban board (like Trello)
- Build a table with drag-and-drop rows
- Build a comment thread (like Jira)
- Build a rich text editor toolbar
- Build a calendar/timeline view

**Example: Build a Kanban Board**
```typescript
function KanbanBoard() {
  const [columns, setColumns] = useState({
    'todo': { title: 'To Do', items: [] },
    'in-progress': { title: 'In Progress', items: [] },
    'done': { title: 'Done', items: [] },
  });
  
  const [newItem, setNewItem] = useState({ title: '', column: 'todo' });
  
  const addItem = () => {
    if (!newItem.title.trim()) return;
    setColumns(prev => ({
      ...prev,
      [newItem.column]: {
        ...prev[newItem.column],
        items: [...prev[newItem.column].items, {
          id: Date.now().toString(),
          title: newItem.title,
        }]
      }
    }));
    setNewItem({ title: '', column: 'todo' });
  };
  
  const moveItem = (itemId, fromColumn, toColumn) => {
    setColumns(prev => {
      const item = prev[fromColumn].items.find(i => i.id === itemId);
      if (!item) return prev;
      
      return {
        ...prev,
        [fromColumn]: {
          ...prev[fromColumn],
          items: prev[fromColumn].items.filter(i => i.id !== itemId),
        },
        [toColumn]: {
          ...prev[toColumn],
          items: [...prev[toColumn].items, item],
        },
      };
    });
  };
  
  return (
    <div className="kanban-board">
      <div className="add-item">
        <input
          value={newItem.title}
          onChange={e => setNewItem({ ...newItem, title: e.target.value })}
          placeholder="New task..."
        />
        <select
          value={newItem.column}
          onChange={e => setNewItem({ ...newItem, column: e.target.value })}
        >
          {Object.entries(columns).map(([key, col]) => (
            <option key={key} value={key}>{col.title}</option>
          ))}
        </select>
        <button onClick={addItem}>Add</button>
      </div>
      
      <div className="columns">
        {Object.entries(columns).map(([key, column]) => (
          <div key={key} className="column"
            onDragOver={(e) => e.preventDefault()}
            onDrop={(e) => {
              const itemId = e.dataTransfer.getData('text/plain');
              moveItem(itemId, e.dataTransfer.getData('fromColumn'), key);
            }}
          >
            <h3>{column.title} ({column.items.length})</h3>
            {column.items.map(item => (
              <div
                key={item.id}
                className="card"
                draggable
                onDragStart={(e) => {
                  e.dataTransfer.setData('text/plain', item.id);
                  e.dataTransfer.setData('fromColumn', key);
                }}
              >
                {item.title}
              </div>
            ))}
          </div>
        ))}
      </div>
    </div>
  );
}
```

### 2. System Design

**Typical problems:**
- Design Jira issue detail page
- Design Confluence document editor
- Design Trello board with real-time collaboration
- Design a project timeline / Gantt chart

**Key considerations:**
- Collaborative editing (multiple users editing same document)
- Real-time updates (WebSocket, polling)
- Performance with large datasets (1000+ Jira issues)
- Plugin/extension architecture (Atlassian Marketplace)
- Accessibility (Atlassian has strong a11y standards)

## Design Thinking

Atlassian emphasizes design thinking in their engineering culture.

**The Double Diamond framework:**
1. **Discover** - Understand the problem
2. **Define** - Define the scope
3. **Develop** - Explore solutions
4. **Deliver** - Implement and test

**How to demonstrate design thinking in interviews:**
- Start by asking "Who is the user? What is their goal?"
- Propose multiple solutions before picking one
- Discuss trade-offs and why you chose your approach
- Consider the full user journey, not just happy path

## Collaboration Values

Atlassian's values:
- **Open company, no bullshit** - Transparency and honesty
- **Build with heart and balance** - Quality work, balanced life
- **Don't #@!% the customer** - Customer-first mentality
- **Play as a team** - Collaboration over individual heroics
- **Be the change you seek** - Proactive improvement

**Sample behavioral questions:**
- Tell me about a time you collaborated with designers
- How do you handle disagreements about technical approach?
- Describe a project where you went above and beyond for a customer
- How do you balance feature velocity with code quality?
- Tell me about a time you mentored a teammate

## Preparation Strategy

### Technical:
- React with hooks (Atlassian uses React extensively)
- Drag and drop (react-beautiful-dnd was created by Atlassian)
- Rich text editing (ProseMirror/Slate)
- Real-time collaboration (WebSocket, CRDT)
- Performance optimization (virtualization for large lists)
- CSS-in-JS (styled-components, Emotion)

### System Design:
- Practice designing collaborative features
- Understand optimistic UI patterns
- Know caching strategies for large datasets
- Consider offline support

### Behavioral:
- Prepare stories demonstrating collaboration
- Show customer empathy in your examples
- Demonstrate ownership and initiative
- Show balance between quality and speed

## Tips from Interviewees

- **Know Atlassian products** - Use Jira, Confluence, or Trello
- **Drag and drop is important** - They literally created react-beautiful-dnd
- **Accessibility matters** - Atlassian invests heavily in a11y
- **Collaboration is core** - Almost every feature involves multiple users
- **Show product sense** - Think about users, not just code
- **Be honest** - "No bullshit" is a core value

## Common Mistakes

- Not knowing about react-beautiful-dnd or drag-and-drop patterns
- Weak understanding of accessibility
- Not considering multi-user scenarios
- Focusing only on code, ignoring users and product
- Making assumptions without asking clarifying questions
- Not showing collaboration and teamwork mentality
