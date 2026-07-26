# Chat Application

**Difficulty:** Medium | **Est. Time:** 45–60 min

---

## Problem Statement

Build a real-time chat application where users can send and receive messages. The UI should display a message list with typing indicators, online status indicators, and support pagination of historical messages.

---

## Requirements

### Functional
- [ ] Display a list of chat messages (author, text, timestamp)
- [ ] Send a new message via input field (Enter key or button)
- [ ] Messages appear immediately in the UI (optimistic update)
- [ ] Typing indicator: "User is typing..." shown while user types
- [ ] Online/offline status indicator for each user
- [ ] Scroll to bottom on new message (unless user scrolled up)
- [ ] Load older messages on scroll-to-top (pagination)
- [ ] Message timestamps (relative: "2m ago" or absolute)

### Non-Functional
- [ ] Smooth automatic scroll behavior
- [ ] Handle rapid message sending without duplicate IDs
- [ ] Avoid layout shift when images/links load
- [ ] Accessible (ARIA live regions for new messages)

---

## Component Architecture

```
App
├── ChatSidebar
│   ├── UserSearch
│   └── ConversationList
│       └── ConversationItem (×N)
│           ├── Avatar
│           ├── UserName
│           ├── LastMessage
│           └── OnlineIndicator (green dot)
├── ChatWindow
│   ├── ChatHeader
│   │   ├── UserName
│   │   ├── OnlineIndicator
│   │   └── UserAvatar
│   ├── MessageList (scrollable, virtualized)
│   │   ├── DateSeparator
│   │   └── Message (×N)
│   │       ├── Avatar
│   │       ├── MessageBubble
│   │       │   ├── Text
│   │       │   ├── Attachments
│   │       │   └── Timestamp
│   │       └── StatusIcon (sent ✓, delivered ✓✓, read blue ✓✓)
│   ├── TypingIndicator
│   └── MessageInput
│       ├── TextArea (auto-resize)
│       ├── EmojiPicker (optional)
│       └── SendButton
```

---

## WebSocket vs Polling

| Approach | Pros | Cons |
|----------|------|------|
| **WebSocket** | Real-time, bidirectional, low latency | Requires server support, connection management |
| **Server-Sent Events** | Simpler one-way, auto-reconnect | No client-to-server streaming |
| **Polling** (setInterval) | Easy to implement, no server changes | Latency, wasted bandwidth |
| **Long Polling** | Works everywhere | Complex, still has latency |

**For interview:** Implement polling first (simpler), then discuss WebSocket as improvement.

---

## Optimistic Updates

```js
function sendMessage(text) {
  const tempId = `temp_${Date.now()}`;
  const optimisticMessage = {
    id: tempId,
    text,
    author: currentUser,
    createdAt: new Date().toISOString(),
    status: 'sending'
  };

  // Immediately add to UI
  setMessages(prev => [...prev, optimisticMessage]);

  // Send to server
  api.sendMessage(text)
    .then(response => {
      // Replace temp message with confirmed message
      setMessages(prev => prev.map(m =>
        m.id === tempId ? { ...response, status: 'sent' } : m
      ));
    })
    .catch(() => {
      // Mark as failed, show retry option
      setMessages(prev => prev.map(m =>
        m.id === tempId ? { ...m, status: 'failed' } : m
      ));
    });
}
```

---

## State Management

```js
const [messages, setMessages] = useState([]);
const [users, setUsers] = useState([]);        // online users
const [typingUsers, setTypingUsers] = useState({});
const [hasMore, setHasMore] = useState(true);
const [isLoading, setIsLoading] = useState(false);
```

---

## Implementation Steps

1. Build the static ChatWindow layout (sidebar + message area)
2. Implement MessageList rendering (avatar, bubble, timestamp)
3. Implement MessageInput (auto-resize textarea, Enter to send)
4. Add new message to state and scroll to bottom
5. Add polling or WebSocket to receive messages from other users
6. Implement optimistic send: temp ID → replace on confirm
7. Add typing indicator: emit on keystroke, debounce, clear on send
8. Add online status: heartbeat or WebSocket connection state
9. Implement scroll-to-load-more (intersection observer on top sentinel)
10. Handle scroll behavior: auto-scroll to bottom on new message, unless user is reading history
11. Add relative timestamps (use date-fns `formatDistanceToNow`)
12. Handle edge cases: empty state, long messages, failed sends, reconnect

---

## Code Snippets

### Auto-scroll with User Scroll Detection

```js
const listRef = useRef(null);
const [isNearBottom, setIsNearBottom] = useState(true);

useEffect(() => {
  if (isNearBottom && messages.length > 0) {
    listRef.current?.lastElementChild?.scrollIntoView({ behavior: 'smooth' });
  }
}, [messages, isNearBottom]);

const handleScroll = useCallback(() => {
  const el = listRef.current;
  if (!el) return;
  const threshold = 100; // px from bottom
  setIsNearBottom(el.scrollHeight - el.scrollTop - el.clientHeight < threshold);
}, []);
```

### Typing Indicator (Debounced)

```js
const typingTimeoutRef = useRef(null);

function handleInputChange(e) {
  setInputText(e.target.value);
  socket.emit('typing:start', { userId: currentUser.id, conversationId });

  clearTimeout(typingTimeoutRef.current);
  typingTimeoutRef.current = setTimeout(() => {
    socket.emit('typing:stop', { userId: currentUser.id, conversationId });
  }, 1500);
}
```

### Intersection Observer for Pagination

```js
const observerRef = useRef(null);
const topSentinelRef = useCallback(node => {
  if (observerRef.current) observerRef.current.disconnect();
  observerRef.current = new IntersectionObserver(entries => {
    if (entries[0].isIntersecting && hasMore && !isLoading) {
      loadMoreMessages();
    }
  });
  if (node) observerRef.current.observe(node);
}, [hasMore, isLoading]);
```

---

## Edge Cases

| Case | Handling |
|------|----------|
| User sends empty message | Disable Send button when input is empty or whitespace-only |
| Very long message | Max length (e.g., 2000 chars); show character count; allow multiline |
| Rapid sends (spam) | Debounce or throttle; use temp IDs to avoid duplicates |
| Network disconnected | Show "Reconnecting..." banner; queue failed messages for retry |
| Same message sent twice | Deduplicate by temp ID replacement |
| User scrolls up while new message arrives | Don't auto-scroll; show "New messages ↓" button instead |
| Very old date | Show absolute date instead of relative ("Jan 5, 2024") |

---

## Bonus Features

- [ ] **Message reactions** (emoji reactions on hover)
- [ ] **Image/file attachments** with upload progress
- [ ] **Threads/replies** on messages
- [ ] **Message search** within conversation
- [ ] **Emoji picker** (use emoji-mart or simple grid)
- [ ] **Dark mode**
- [ ] **Read receipts** (mark messages as read when visible)

---

## Common Interview Questions

1. **How do you handle message ordering with optimistic updates?** — Use a monotonic timestamp or server-assigned ID. Sort by createdAt. Temp IDs sort by client timestamp; real IDs replace them.

2. **How do you implement typing indicators efficiently?** — Emit on keystroke (debounced to 300ms), stop typing after 1.5s of inactivity. Server broadcasts only to other participants in the conversation.

3. **How to handle reconnection?** — WebSocket: listen for `close`/`error`, attempt reconnect with exponential backoff. On reconnect, fetch missed messages using `lastSeenMessageId`.

4. **How do you prevent XSS in messages?** — Never use `dangerouslySetInnerHTML`. Render text as plain text. If you need rich text, use a sanitizer (DOMPurify) on trusted content only.

---

## Resources

- [WebSocket MDN](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)
- [Intersection Observer API](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
- [date-fns formatDistanceToNow](https://date-fns.org/docs/formatDistanceToNow)
