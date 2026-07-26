# Cross-Site Request Forgery (CSRF)

Cross-Site Request Forgery (CSRF) is an attack that forces an authenticated user to execute unwanted actions on a web application they're currently authenticated to, by exploiting the trust the application has in the user's browser.

## How CSRF Works

```
[Victim logged into bank.com]
        |
        v
[Attacker sends email with malicious link]
        |
        v
[Victim clicks link while bank.com session is active]
        |
        v
[Browser automatically includes cookies for bank.com]
        |
        v
[bank.com executes the forged request]
```

### CSRF Attack Flow (Mermaid)

```mermaid
sequenceDiagram
    participant Victim
    participant Browser
    participant Bank as Bank.com
    participant Attacker
    participant Evil as Evil.com

    Note over Victim,Evil: Victim authenticates
    Victim->>Browser: Login to bank.com
    Browser->>Bank: POST /login (credentials)
    Bank-->>Browser: Set-Cookie: session=abc123; Secure; HttpOnly
    Browser->>Browser: Stores session cookie

    Note over Victim,Evil: Attacker crafts exploit
    Attacker->>Evil: Hosts malicious page

    Note over Victim,Evil: Victim visits malicious page
    Victim->>Browser: Opens evil.com
    Browser->>Evil: GET /
    Evil-->>Browser: <img src="https://bank.com/transfer?to=attacker&amount=1000">
    Browser->>Bank: GET /transfer?to=attacker&amount=1000
    Note over Browser,Bank: Automatically includes bank.com cookies!
    Bank->>Bank: Validates session
    Bank->>Bank: Executes transfer
    Bank-->>Browser: Transfer successful

    Note over Victim,Evil: CSRF successful without victim's knowledge
```

## CSRF Attack Vectors

### 1. Image Tag CSRF
```html
<!-- Automatically fires GET request -->
<img src="https://bank.com/api/transfer?to=attacker&amount=1000" width="0" height="0" />
```

### 2. Form Auto-Submit CSRF
```html
<form action="https://bank.com/api/transfer" method="POST" id="csrf-form">
  <input type="hidden" name="to" value="attacker" />
  <input type="hidden" name="amount" value="1000" />
</form>
<script>document.getElementById('csrf-form').submit();</script>
```

### 3. XHR/Fetch CSRF
```html
<script>
  fetch('https://bank.com/api/transfer', {
    method: 'POST',
    credentials: 'include',  // sends cookies
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ to: 'attacker', amount: 1000 })
  });
</script>
```

### 4. Link CSRF
```html
<!-- User clicks link thinking it's something else -->
<a href="https://bank.com/api/delete-account?confirm=true">Click here for cute cat pictures</a>
```

## Prevention Techniques

### 1. SameSite Cookies (Most Effective)
SameSite attribute restricts when cookies are sent with cross-origin requests:

```http
Set-Cookie: session=abc123; SameSite=Strict; Secure; HttpOnly
```

| SameSite Value | Behavior |
|---------------|----------|
| `Strict` | Cookie sent only for same-site requests (top-level navigation) |
| `Lax` | Cookie sent for same-site and top-level GET navigations (default in modern browsers) |
| `None` | Cookie sent for all requests (requires `Secure`) |

```javascript
// Express.js example
app.use(session({
  secret: 'your-secret',
  cookie: {
    sameSite: 'strict',  // 'lax' | 'none'
    secure: true,
    httpOnly: true
  }
}));
```

### 2. CSRF Tokens (Synchronizer Token Pattern)
Server generates a unique, unpredictable token embedded in forms:

```javascript
// Server-side (Express.js)
const csrf = require('csrf');
const tokens = new csrf();

app.get('/form', (req, res) => {
  const secret = tokens.secretSync();
  req.session.csrfSecret = secret;
  const token = tokens.create(secret);
  res.render('form', { csrfToken: token });
});

app.post('/transfer', (req, res) => {
  if (!tokens.verify(req.session.csrfSecret, req.body.csrfToken)) {
    return res.status(403).send('CSRF validation failed');
  }
  // Process transfer
});
```

```html
<!-- Hidden field in form -->
<form method="POST" action="/transfer">
  <input type="hidden" name="csrfToken" value="<%= csrfToken %>">
  <input type="text" name="amount">
  <button type="submit">Transfer</button>
</form>
```

```javascript
// Frontend: Include CSRF token in API calls
async function transfer(amount, to) {
  const response = await fetch('/api/transfer', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'X-CSRF-Token': getCsrfToken()  // Read from meta tag or cookie
    },
    body: JSON.stringify({ amount, to })
  });
  return response.json();
}
```

### 3. Double-Submit Cookie Pattern
Send a random value as both a cookie and a request header. Server validates they match.

```javascript
// Server sets a CSRF cookie
app.get('/api/csrf-token', (req, res) => {
  const csrfToken = crypto.randomBytes(32).toString('hex');
  res.cookie('csrf-token', csrfToken, {
    secure: true,
    sameSite: 'strict',
    httpOnly: false,  // JS needs to read it
    path: '/'
  });
  res.json({ csrfToken });
});

// Frontend reads cookie and sends as header
async function csrfSafeRequest(url, options = {}) {
  const csrfToken = getCookie('csrf-token');
  return fetch(url, {
    ...options,
    headers: {
      ...options.headers,
      'X-CSRF-Token': csrfToken
    },
    credentials: 'include'
  });
}

// Server validates cookie matches header
app.post('/api/transfer', (req, res) => {
  const cookieToken = req.cookies['csrf-token'];
  const headerToken = req.headers['x-csrf-token'];
  
  if (!cookieToken || !headerToken || cookieToken !== headerToken) {
    return res.status(403).json({ error: 'CSRF validation failed' });
  }
  // Process request
});
```

**Double-Submit Cookie Pattern Diagram:**
```mermaid
sequenceDiagram
    participant Client
    participant Server

    Client->>Server: GET /api/csrf-token
    Server-->>Client: Set-Cookie: csrf-token=random; (SameSite=Strict)
    Note over Client: JS reads cookie value

    Client->>Server: POST /api/transfer
    Note over Client: Cookie: csrf-token=random (auto-sent)
    Note over Client: Header: X-CSRF-Token: random (explicitly set)
    Server->>Server: Validate cookieToken === headerToken
    Server-->>Client: 200 OK (or 403 Forbidden)
```

### 4. Custom Request Headers
Modern single-page apps can leverage custom headers for CSRF protection. Browsers enforce CORS preflight for custom headers, preventing cross-origin requests.

```javascript
// Always send a custom header
fetch('/api/transfer', {
  method: 'POST',
  headers: {
    'X-Requested-By': 'XMLHttpRequest',  // Custom header triggers CORS preflight
    'Content-Type': 'application/json'
  },
  credentials: 'include',
  body: JSON.stringify({ amount: 100, to: 'alice' })
});
```

### 5. Origin / Referer Header Validation
Server checks the `Origin` or `Referer` header:

```javascript
const allowedOrigins = ['https://bank.com', 'https://app.bank.com'];

function validateOrigin(req, res, next) {
  const origin = req.headers.origin || req.headers.referer;
  
  if (!origin) return res.status(403).json({ error: 'Origin required' });
  
  const isAllowed = allowedOrigins.some(allowed => origin.startsWith(allowed));
  
  if (!isAllowed) return res.status(403).json({ error: 'Invalid origin' });
  
  next();
}
```

### 6. reCAPTCHA
Require user interaction for sensitive actions:

```javascript
// Frontend
grecaptcha.ready(function() {
  grecaptcha.execute('6Lc...', { action: 'transfer' }).then(function(token) {
    fetch('/api/transfer', {
      method: 'POST',
      headers: { 'X-Recaptcha-Token': token },
      body: JSON.stringify({ amount: 1000, to: 'bob' })
    });
  });
});

// Server validates
app.post('/api/transfer', async (req, res) => {
  const recaptchaToken = req.headers['x-recaptcha-token'];
  const verification = await verifyRecaptcha(recaptchaToken);
  if (!verification.success) return res.status(403).json({ error: 'Bot detected' });
  // Process transfer
});
```

## CSRF vs XSS Comparison

| Aspect | CSRF | XSS |
|--------|------|-----|
| Attack vector | Forged requests via authenticated session | Script injection |
| Target | State-changing operations | Any page operation |
| Exploits | App trusts user's browser | App trusts user input |
| User action | Click link or visit page | View page with injected script |
| Session required | Yes (relies on active session) | Not necessarily |
| Prevention | CSRF tokens, SameSite, Origin check | Output encoding, CSP, sanitization |

## Real-World CSRF Examples

### Example 1: ING Direct CSRF (2018)
ING Direct's money transfer API lacked CSRF protection:
- Attacker crafted a form that auto-submitted to ING
- Victims with active sessions unknowingly transferred funds
- Fixed by implementing SameSite cookies and CSRF tokens

### Example 2: YouTube CSRF (2008)
YouTube had a CSRF vulnerability affecting all actions:
- Attacker lured logged-in users to a malicious page
- Page sent requests to add videos to favorites, add friends, send messages
- Worm-like propagation affected thousands of users
- Exploited the lack of CSRF tokens in YouTube's API

## CSRF Prevention Checklist

- [ ] Set `SameSite=Strict` or `SameSite=Lax` on all session cookies
- [ ] Implement CSRF tokens for all state-changing requests (POST, PUT, DELETE)
- [ ] Validate `Origin` and/or `Referer` headers on server side
- [ ] Never use GET requests for state-changing operations
- [ ] Use custom headers for API requests (X-Requested-By, X-CSRF-Token)
- [ ] Require re-authentication for sensitive actions (password change, transfer)
- [ ] Include CSRF tokens in response headers for SPA clients
- [ ] Use cookie prefixes (`__Host-` or `__Secure-`) for added protection
- [ ] Consider implementing the Double-Submit Cookie pattern for stateless APIs

## Resources
- [OWASP CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
- [SameSite Cookies Explained](https://web.dev/samesite-cookies-explained/)
- [MDN: SameSite cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)
