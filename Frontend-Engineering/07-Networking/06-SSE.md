# Server-Sent Events (SSE)

## What are Server-Sent Events?

SSE is a technology that allows a server to push data to a client over a single HTTP connection. Unlike WebSocket, SSE is unidirectional — data flows from server to client only.

## SSE vs WebSocket

| Feature | SSE | WebSocket |
|---------|-----|-----------|
| Direction | Server → Client (unidirectional) | Full-duplex |
| Protocol | HTTP (standard) | WebSocket (upgrade) |
| Message format | Text only (UTF-8) | Text and Binary |
| Auto-reconnection | Built-in (EventSource API) | Must implement manually |
| Event types | Named events | Custom message types |
| Request headers | Standard HTTP | Not supported after handshake |
| Browser support | All modern browsers | All modern browsers |
| Max connections | 6 per domain (HTTP/1.1) | Unlimited |
| Complexity | Very simple | Moderate |
| Firewall friendly | Yes (uses HTTP) | May be blocked |

## When to Use SSE vs WebSocket

```
SSE is better for:
  - Live news feeds / stock tickers
  - Real-time notifications
  - Status updates / progress bars
  - Twitter feeds / social media streams
  - Log streaming
  - Server metrics dashboard

WebSocket is better for:
  - Chat applications
  - Multiplayer games
  - Collaborative editing
  - Any bidirectional real-time communication
```

## The Event Stream Format

```
data: {"message": "Hello, world!"}

```

Each event consists of:

| Field | Description | Required |
|-------|-------------|----------|
| `event` | Event type (default: `message`) | No |
| `data` | Payload (can be multiple lines) | Yes |
| `id` | Last event ID (for reconnection) | No |
| `retry` | Reconnection time in ms | No |

### Event Format Examples

```text
# Simple text message
data: Hello world

---

# JSON event
data: {"user": "Alice", "action": "login"}

---

# Named event with ID
event: user-login
id: 42
data: {"user": "Alice", "timestamp": "2025-01-15T10:00:00Z"}

---

# Multiline data event
event: notification
data: {"type": "alert"}
data: {"title": "Server Alert", "severity": "warning"}

---

# Retry interval (sent once, applies to all future reconnections)
retry: 3000

---

# Server sends a comment (ignored by client)
: This is a comment
```

## EventSource API

```javascript
// Basic usage
const eventSource = new EventSource('https://api.example.com/events');

// Listen for named events
eventSource.addEventListener('user-login', (event) => {
  const data = JSON.parse(event.data);
  console.log('User logged in:', data.user);
});

eventSource.addEventListener('notification', (event) => {
  const data = JSON.parse(event.data);
  showNotification(data.title, data.severity);
});

// Listen for all unnamed messages
eventSource.onmessage = (event) => {
  console.log('Received:', event.data);
};

// Connection opened
eventSource.onopen = () => {
  console.log('SSE connection established');
};

// Error handling
eventSource.onerror = (error) => {
  console.error('SSE error:', error);
  // EventSource will automatically attempt reconnection
};
```

## SSE Client Implementation

```javascript
class SSEClient {
  constructor(url, options = {}) {
    this.url = url;
    this.options = {
      withCredentials: false,
      headers: {},
      lastEventId: null,
      ...options
    };
    this.eventSource = null;
    this.listeners = new Map();
    this.reconnectTimeout = null;
  }

  connect() {
    if (this.eventSource) {
      this.close();
    }

    const url = this.options.lastEventId
      ? `${this.url}?lastEventId=${this.options.lastEventId}`
      : this.url;

    this.eventSource = new EventSource(url, {
      withCredentials: this.options.withCredentials
    });

    this.eventSource.onopen = () => {
      console.log('SSE connected');
      this.emit('connected');
    };

    this.eventSource.onmessage = (event) => {
      this.handleEvent('message', event);
    };

    this.eventSource.onerror = () => {
      // EventSource automatically reconnects
      console.warn('SSE connection error, reconnecting...');
      this.emit('reconnecting');
    };
  }

  addEventListener(event, callback) {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, []);
      if (this.eventSource) {
        this.eventSource.addEventListener(event, (e) => this.handleEvent(event, e));
      }
    }
    this.listeners.get(event).push(callback);
    return () => this.removeEventListener(event, callback);
  }

  removeEventListener(event, callback) {
    const callbacks = this.listeners.get(event);
    if (callbacks) {
      const index = callbacks.indexOf(callback);
      if (index !== -1) callbacks.splice(index, 1);
    }
  }

  handleEvent(event, nativeEvent) {
    try {
      const data = JSON.parse(nativeEvent.data);
      // Track last event ID for reconnection
      if (nativeEvent.lastEventId) {
        this.options.lastEventId = nativeEvent.lastEventId;
      }
      this.emit(event, data, nativeEvent);
    } catch {
      this.emit(event, nativeEvent.data, nativeEvent);
    }
  }

  emit(event, ...args) {
    const callbacks = this.listeners.get(event);
    if (callbacks) {
      callbacks.forEach(cb => cb(...args));
    }
  }

  close() {
    if (this.eventSource) {
      this.eventSource.close();
      this.eventSource = null;
    }
  }
}

// Usage
const sse = new SSEClient('https://api.example.com/stream', {
  withCredentials: true
});

const unsubscribe = sse.addEventListener('price-update', (data) => {
  updatePriceChart(data.symbol, data.price);
});

sse.addEventListener('connected', () => {
  console.log('Live price feed active');
});

sse.connect();

// Cleanup
// unsubscribe();
// sse.close();
```

## Server-side SSE (Node.js)

```javascript
const express = require('express');
const app = express();

app.get('/events', (req, res) => {
  // Set SSE headers
  res.writeHead(200, {
    'Content-Type': 'text/event-stream',
    'Cache-Control': 'no-cache',
    'Connection': 'keep-alive',
    'X-Accel-Buffering': 'no' // Disable nginx buffering
  });

  // Send initial retry interval
  res.write('retry: 3000\n\n');

  // Handle last-event-id for reconnection
  const lastEventId = req.query.lastEventId;
  if (lastEventId) {
    // Send missed events since lastEventId (from your event store)
    sendMissedEvents(res, lastEventId);
  }

  // Send periodic events
  const intervalId = setInterval(() => {
    const data = {
      time: new Date().toISOString(),
      value: Math.random()
    };

    res.write(`event: update\n`);
    res.write(`id: ${Date.now()}\n`);
    res.write(`data: ${JSON.stringify(data)}\n\n`);
  }, 1000);

  // Handle client disconnect
  req.on('close', () => {
    clearInterval(intervalId);
    console.log('Client disconnected');
  });
});

app.listen(3000);
```

```python
# Python server (Flask)
from flask import Flask, Response, stream_with_context
import json
import time

app = Flask(__name__)

@app.route('/events')
def stream():
    def generate():
        while True:
            data = {
                'timestamp': time.time(),
                'value': 'live data'
            }
            yield f"event: update\ndata: {json.dumps(data)}\n\n"
            time.sleep(1)
    
    return Response(
        stream_with_context(generate()),
        mimetype='text/event-stream',
        headers={
            'Cache-Control': 'no-cache',
            'Connection': 'keep-alive',
            'X-Accel-Buffering': 'no'
        }
    )
```

## Real-world Examples

### Live Notification Feed

```javascript
class NotificationFeed {
  constructor() {
    this.sse = new SSEClient('/api/notifications/stream');
    this.setupListeners();
  }

  setupListeners() {
    this.sse.addEventListener('notification', (notification) => {
      this.addToFeed(notification);
      this.playSound();
      
      if (notification.severity === 'critical') {
        this.showAlert(notification);
      }
    });

    this.sse.addEventListener('connected', () => {
      this.showToast('Connected to notification service');
    });

    this.sse.addEventListener('reconnecting', () => {
      this.updateStatus('Reconnecting...');
    });

    this.sse.connect();
  }

  addToFeed(notification) {
    const container = document.getElementById('notification-feed');
    const item = document.createElement('div');
    item.className = `notification ${notification.severity}`;
    item.innerHTML = `
      <div class="notification-icon">${this.getIcon(notification.type)}</div>
      <div class="notification-content">
        <h4>${escapeHtml(notification.title)}</h4>
        <p>${escapeHtml(notification.body)}</p>
        <span class="notification-time">
          ${new Date(notification.timestamp).toLocaleTimeString()}
        </span>
      </div>
    `;
    container.prepend(item);
  }
}
```

### Live Search Suggestions

```javascript
class LiveSearch {
  constructor(inputElement) {
    this.input = inputElement;
    this.sse = null;
    this.setupInput();
  }

  setupInput() {
    let debounceTimer;
    this.input.addEventListener('input', (e) => {
      clearTimeout(debounceTimer);
      debounceTimer = setTimeout(() => {
        this.startStream(e.target.value);
      }, 300);
    });
  }

  startStream(query) {
    if (this.sse) {
      this.sse.close();
    }

    if (query.length < 2) {
      this.clearSuggestions();
      return;
    }

    this.sse = new EventSource(`/api/search/suggest?q=${encodeURIComponent(query)}`);

    this.sse.onmessage = (event) => {
      const { suggestions } = JSON.parse(event.data);
      this.renderSuggestions(suggestions);
    };
  }

  renderSuggestions(suggestions) {
    const container = document.getElementById('suggestions');
    container.innerHTML = suggestions
      .map(s => `<li>${escapeHtml(s)}</li>`)
      .join('');
  }
}
```

### Progress Tracking

```javascript
class ProgressTracker {
  constructor(taskId) {
    this.taskId = taskId;
    this.sse = new EventSource(`/api/tasks/${taskId}/progress`);
    
    this.sse.addEventListener('progress', (event) => {
      const { percent, message } = JSON.parse(event.data);
      this.updateProgressBar(percent);
      this.updateStatus(message);
      
      if (percent >= 100) {
        this.sse.close();
        this.onComplete();
      }
    });

    this.sse.addEventListener('error', (event) => {
      const { message } = JSON.parse(event.data);
      this.showError(message);
      this.sse.close();
    });
  }

  updateProgressBar(percent) {
    const bar = document.getElementById('progress-bar');
    bar.style.width = `${percent}%`;
    bar.textContent = `${Math.round(percent)}%`;
  }
}
```

## Connection Limits

```
HTTP/1.1: Browsers limit 6 concurrent SSE connections per domain
HTTP/2:   Effectively unlimited (multiplexed)
```

### Workaround for HTTP/1.1

```javascript
// Use a single SSE connection that multiplexes event types
const sse = new EventSource('/api/events');

// Single connection handles multiple event types
sse.addEventListener('notification', handleNotification);
sse.addEventListener('price-update', handlePrice);
sse.addEventListener('chat-message', handleChat);
sse.addEventListener('status', handleStatus);
```

## Key Takeaways

- SSE is a unidirectional (server → client) real-time protocol over HTTP
- Built-in auto-reconnection with `Last-Event-ID` for missed events
- Much simpler than WebSocket when you only need server push
- Supports named events for organizing different data types
- 6 connection limit per domain under HTTP/1.1 (use HTTP/2 or multiplex)
- Cannot send data from client to server (use HTTP requests or WebSocket)
- Great for notifications, live feeds, progress updates, and metrics
