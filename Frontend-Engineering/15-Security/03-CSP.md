# Content Security Policy (CSP)

Content Security Policy (CSP) is a security standard that helps prevent Cross-Site Scripting (XSS), clickjacking, and other code injection attacks by controlling which resources the browser is allowed to load for a given page.

## How CSP Works

CSP works via an HTTP header that defines a whitelist of trusted content sources. The browser enforces these restrictions:

```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://apis.example.com
```

```
[Browser requests page]
        |
        v
[Server responds with CSP header]
        |
        v
[Browser parses CSP directives]
        |
        v
[For each resource request (script, style, img, etc)]
        |
        ├── Source matches CSP? ──> Allowed
        └── Source violates CSP? ──> Blocked + report
```

## CSP Directives

### Fetch Directives
Control resource loading sources:

| Directive | Controls | Example |
|-----------|----------|---------|
| `default-src` | Fallback for all fetch directives | `default-src 'self'` |
| `script-src` | JavaScript sources | `script-src 'self' https://cdn.example.com` |
| `style-src` | CSS sources | `style-src 'self' 'unsafe-inline'` |
| `img-src` | Image sources | `img-src 'self' data: https://images.example.com` |
| `font-src` | Font sources | `font-src 'self' https://fonts.gstatic.com` |
| `connect-src` | Fetch, XHR, WebSocket sources | `connect-src 'self' https://api.example.com wss://ws.example.com` |
| `media-src` | Audio/video sources | `media-src 'self' https://videos.example.com` |
| `frame-src` | Iframe sources | `frame-src 'self' https://player.vimeo.com` |
| `object-src` | Plugin (Flash, Java) sources | `object-src 'none'` |
| `manifest-src` | Web app manifest sources | `manifest-src 'self'` |

### Document Directives

| Directive | Purpose |
|-----------|---------|
| `base-uri` | Restricts URLs in `<base>` element |
| `form-action` | Restricts form submission targets |
| `frame-ancestors` | Controls which sites can embed the page (clickjacking protection) |
| `navigate-to` | Restricts navigation targets |
| `report-uri` | Deprecated in favor of `report-to` |

### Source Expressions

| Source | Description |
|--------|-------------|
| `'self'` | Same origin |
| `'none'` | No sources allowed |
| `'unsafe-inline'` | Allows inline scripts/styles (use nonce/hash instead) |
| `'unsafe-eval'` | Allows `eval()`, `setTimeout(string)` |
| `'strict-dynamic'` | Trust propagated from a nonced script |
| `'nonce-<base64>'` | Specific nonce for inline scripts |
| `'<hash-algorithm>-<base64>'` | Hash of allowed inline script content |
| `http://example.com` | Specific origin (avoid http in production) |
| `https://*.example.com` | Wildcard subdomain |
| `data:` | Data URIs |
| `blob:` | Blob URIs |
| `https:` | Any HTTPS origin (too permissive) |

### CSP Header Examples

**Strict CSP (Recommended):**
```http
Content-Security-Policy:
  default-src 'self';
  script-src 'strict-dynamic' 'nonce-RANDOM' 'unsafe-inline' https: 'unsafe-eval';
  object-src 'none';
  base-uri 'none';
```

**Typical SPA CSP:**
```http
Content-Security-Policy:
  default-src 'self';
  script-src 'self' https://app.example.com;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https://images.example.com;
  font-src 'self' https://fonts.gstatic.com;
  connect-src 'self' https://api.example.com;
  frame-ancestors 'none';
  base-uri 'self';
  form-action 'self';
```

**Strict but Allow External Services:**
```http
Content-Security-Policy:
  default-src 'self';
  script-src 'self' https://www.googletagmanager.com https://www.google-analytics.com;
  style-src 'self' 'unsafe-inline' https://fonts.googleapis.com;
  img-src 'self' data: https://www.google-analytics.com https://www.googletagmanager.com;
  connect-src 'self' https://api.example.com;
  font-src 'self' https://fonts.gstatic.com;
  frame-src 'self' https://www.youtube.com;
```

## Report-Only Mode

Instead of enforcing, CSP can run in report-only mode to monitor violations:

```http
Content-Security-Policy-Report-Only:
  default-src 'self';
  script-src 'self';
  report-uri /csp-violation-report;
```

Reports are sent as POST requests to the report-uri:

```javascript
// CSP violation report endpoint (Express.js)
app.post('/csp-violation-report', (req, res) => {
  const report = req.body['csp-report'] || req.body;
  console.error('CSP Violation:', {
    'document-uri': report['document-uri'],
    'violated-directive': report['violated-directive'],
    'blocked-uri': report['blocked-uri'],
    'original-policy': report['original-policy'],
  });
  // Send to monitoring system
  sendToSentry({
    message: 'CSP Violation',
    extra: report
  });
  res.status(204).end();
});
```

```bash
# Sample CSP violation report payload
# POST /csp-violation-report
{
  "csp-report": {
    "document-uri": "https://example.com/",
    "referrer": "https://evil.com/",
    "blocked-uri": "https://evil.com/steal.js",
    "violated-directive": "script-src 'self'",
    "original-policy": "default-src 'self'; script-src 'self'; report-uri /csp-violation-report"
  }
}
```

Modern approach uses `report-to` header:

```http
Content-Security-Policy: ...; report-to csp-endpoint
Report-To: { "group": "csp-endpoint", "max_age": 10886400, "endpoints": [{ "url": "https://example.com/csp-reports" }] }
```

## Nonce and Hash for Inline Scripts

### Nonce-based CSP

Generate a unique nonce per request:

```javascript
// Server generates random nonce per request
const crypto = require('crypto');

app.use((req, res, next) => {
  res.locals.nonce = crypto.randomBytes(16).toString('base64');
  res.setHeader('Content-Security-Policy',
    `script-src 'strict-dynamic' 'nonce-${res.locals.nonce}'`
  );
  next();
});
```

```html
<!-- Server includes nonce in HTML -->
<script nonce="<%= nonce %>">
  window.__INITIAL_STATE__ = <%= JSON.stringify(state) %>;
</script>

<script nonce="<%= nonce %>" src="/app.js"></script>
```

### Hash-based CSP

Calculate SHA hash of inline script content:

```bash
# Calculate hash for a script
echo -n "alert('hello');" | openssl dgst -sha256 -binary | base64
# Output: Z3VpZGVkLXNjcmlwdC0xMjM0...

# Or use a script
node -e "
  const crypto = require('crypto');
  const script = 'alert(\"hello\");';
  const hash = crypto.createHash('sha256').update(script).digest('base64');
  console.log(`'sha256-${hash}'`);
"
```

```http
Content-Security-Policy:
  script-src 'sha256-Z3VpZGVkLXNjcmlwdC0xMjM0...';
```

```html
<!-- The exact script must match the hash -->
<script>alert('hello');</script>
```

## Strict CSP

A "strict CSP" relies on nonces/hashes instead of domain whitelists to defend against XSS. It's more secure than URL whitelisting.

```http
Content-Security-Policy:
  script-src 'strict-dynamic' 'nonce-RANDOM_VALUE' 'unsafe-inline' https: 'unsafe-eval';
  object-src 'none';
  base-uri 'none';
```

**Properties:**
- `'strict-dynamic'`: Trust propagates from an allowed script to any scripts it loads
- `'unsafe-inline'`: Fallback for browsers that don't support nonce
- `https:`: Fallback for Safari
- `'unsafe-eval'`: Required if using frameworks like Angular or React DevTools

```
[Nonced script loads]
        |
        v
[Creates new script element dynamically]
        |
        v
[strict-dynamic allows it because parent was trusted]
        |
        v
[Dynamically created scripts propagate trust]
```

**Why NOT domain whitelisting:**
```http
# BAD: Domain whitelist is easily bypassed
Content-Security-Policy: script-src https://cdn.example.com https://cdn.google.com

# Bypass: Attacker uploads malicious JS to any subdomain of allowed origins
# https://cdn.example.com/user-uploads/evil.js would be ALLOWED
```

## CSP Evaluator

Use [CSP Evaluator](https://csp-evaluator.withgoogle.com/) by Google to check policies:

```javascript
// Programmatic evaluation
const cspParser = require('content-security-policy-parser');

function evaluateCSP(policy) {
  const parsed = cspParser(policy);
  const issues = [];
  
  if (parsed['script-src']?.includes("'unsafe-inline'") && !parsed['script-src']?.includes("'nonce-")) {
    issues.push('DANGER: unsafe-inline without nonce allows any inline script');
  }
  
  if (parsed['script-src']?.includes("'unsafe-eval'")) {
    issues.push('WARNING: unsafe-eval enables eval-based attacks');
  }
  
  if (!parsed['object-src'] && !parsed['default-src']?.includes("'none'")) {
    issues.push('WARNING: missing object-src allows plugin-based attacks');
  }
  
  if (parsed['script-src']?.includes('https:')) {
    issues.push('WARNING: https: allows any HTTPS origin - too permissive');
  }
  
  return issues;
}

console.log(evaluateCSP("default-src 'self'; script-src https: 'unsafe-inline'"));
// ['WARNING: https: allows any HTTPS origin - too permissive', 'DANGER: unsafe-inline without nonce allows any inline script']
```

## Real-World CSP Failures

### GitHub (2016)
GitHub deployed CSP with `script-src 'self' *.github.com *.githubusercontent.com`:
- Attacker exploited a vulnerability in `github.com` to inject scripts
- CSP allowed scripts from `github.com` itself, so the attack succeeded
- **Lesson:** Domain whitelists are fragile; prefer nonce-based strict CSP

### XSS on Google Search (2019)
Google's CSP used `script-src 'self' https://*.google.com`:
- A vulnerability in Google Tag Manager allowed script injection
- CSP trusted all google.com subdomains, including the compromised one
- **Lesson:** Use `'strict-dynamic'` instead of domain whitelists

## Implementation Guide

### Express.js Middleware
```javascript
const helmet = require('helmet');

app.use(
  helmet.contentSecurityPolicy({
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", (req, res) => `'nonce-${res.locals.nonce}'`],
      styleSrc: ["'self'", "'unsafe-inline'"],
      imgSrc: ["'self'", "data:", "https://images.example.com"],
      connectSrc: ["'self'", "https://api.example.com"],
      fontSrc: ["'self'", "https://fonts.gstatic.com"],
      objectSrc: ["'none'"],
      frameAncestors: ["'none'"],
      baseUri: ["'self'"],
      formAction: ["'self'"],
    },
    reportOnly: false, // Set to true for testing
  })
);
```

### Nginx Configuration
```nginx
add_header Content-Security-Policy "
  default-src 'self';
  script-src 'strict-dynamic' 'nonce-$request_id' 'unsafe-inline' https:;
  style-src 'self' 'unsafe-inline';
  img-src 'self' data:;
  object-src 'none';
  base-uri 'none';
" always;
```

### React Integration
```javascript
// Generate nonce in server-rendered HTML
<meta httpEquiv="Content-Security-Policy"
  content={`script-src 'strict-dynamic' 'nonce-${nonce}'`} />

// Create script tags with nonce
<script nonce={nonce} src="/static/js/main.js"></script>

// In index.html template
<script nonce="<%= nonce %>">
  window.__INITIAL_STATE__ = <%= JSON.stringify(initialState) %>;
</script>
```

## CSP Checklist

- [ ] Start with `Content-Security-Policy-Report-Only` before enforcing
- [ ] Use `'strict-dynamic'` with `'nonce-'` instead of domain whitelists
- [ ] Set `object-src 'none'` and `base-uri 'none'`
- [ ] Use `frame-ancestors 'none'` to prevent clickjacking
- [ ] Validate all inline scripts/use nonces for SPA bootstrapping
- [ ] Monitor CSP reports via `report-uri` or `report-to`
- [ ] Remove `'unsafe-inline'` once all inline handlers are migrated
- [ ] Avoid using `https:` or data: URI schemes as broad whitelists
- [ ] Test policies with CSP Evaluator before production deployment
- [ ] Set up real-time alerts for CSP violation reports

## Resources
- [CSP Evaluator](https://csp-evaluator.withgoogle.com/)
- [MDN: Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [web.dev: CSP](https://web.dev/csp/)
- [CSP Reference](https://content-security-policy.com/)
- [Helmet.js CSP](https://helmetjs.github.io/)
