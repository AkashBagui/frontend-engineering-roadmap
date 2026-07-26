# Networking Projects

## Project: Multi-API Dashboard

Build a dashboard that consumes multiple public APIs with proper loading states, error handling, caching, abort controllers, and request deduplication.

## APIs to Use

```javascript
const APIs = {
  github: {
    base: 'https://api.github.com',
    endpoints: {
      user: '/users/{username}',
      repos: '/users/{username}/repos',
      readme: '/repos/{owner}/{repo}/readme'
    }
  },
  weather: {
    base: 'https://api.open-meteo.com/v1',
    endpoints: {
      forecast: '/forecast?latitude={lat}&longitude={lon}&current_weather=true'
    }
  },
  jsonplaceholder: {
    base: 'https://jsonplaceholder.typicode.com',
    endpoints: {
      posts: '/posts',
      users: '/users',
      comments: '/posts/{id}/comments'
    }
  },
  countries: {
    base: 'https://restcountries.com/v3.1',
    endpoints: {
      all: '/all',
      name: '/name/{name}'
    }
  }
};
```

## Core Implementation

### 1. HTTP Client with Abort Controllers

```javascript
class HttpClient {
  constructor(baseUrl, options = {}) {
    this.baseUrl = baseUrl.replace(/\/$/, '');
    this.options = options;
    this.abortControllers = new Map();
  }

  async request(method, path, options = {}) {
    const url = `${this.baseUrl}${path}`;
    const requestId = `${method}:${url}`;

    // Cancel previous request for same endpoint
    this.cancelRequest(requestId);

    const controller = new AbortController();
    this.abortControllers.set(requestId, controller);

    try {
      const response = await fetch(url, {
        method,
        signal: controller.signal,
        headers: {
          'Content-Type': 'application/json',
          ...this.options.headers,
          ...options.headers
        },
        ...options.fetchOptions
      });

      this.abortControllers.delete(requestId);

      if (!response.ok) {
        throw new HttpError(response.status, await response.text());
      }

      return response.json();
    } catch (error) {
      this.abortControllers.delete(requestId);
      if (error.name === 'AbortError') {
        throw new RequestAbortedError();
      }
      throw error;
    }
  }

  cancelRequest(requestId) {
    const controller = this.abortControllers.get(requestId);
    if (controller) {
      controller.abort();
      this.abortControllers.delete(requestId);
    }
  }

  cancelAll() {
    this.abortControllers.forEach(controller => controller.abort());
    this.abortControllers.clear();
  }
}
```

### 2. Request Deduplication

```javascript
class RequestDeduplicator {
  constructor() {
    this.inFlight = new Map();
  }

  async deduplicate(key, fetcher) {
    // If request already in flight, wait for it
    if (this.inFlight.has(key)) {
      return this.inFlight.get(key);
    }

    const promise = fetcher().finally(() => {
      this.inFlight.delete(key);
    });

    this.inFlight.set(key, promise);
    return promise;
  }
}

// Usage
const dedup = new RequestDeduplicator();

async function fetchUserRepos(username) {
  return dedup.deduplicate(`repos:${username}`, () =>
    fetch(`https://api.github.com/users/${username}/repos`).then(r => r.json())
  );
}

// Multiple calls to same endpoint share one request
const [a, b, c] = await Promise.all([
  fetchUserRepos('octocat'),
  fetchUserRepos('octocat'),
  fetchUserRepos('octocat')
]); // Only 1 HTTP request made
```

### 3. Caching Layer (IndexedDB)

```javascript
class IndexedDBCache {
  constructor(dbName = 'api-cache', version = 1) {
    this.dbName = dbName;
    this.version = version;
    this.db = null;
  }

  async open() {
    return new Promise((resolve, reject) => {
      const request = indexedDB.open(this.dbName, this.version);

      request.onupgradeneeded = (event) => {
        const db = event.target.result;
        if (!db.objectStoreNames.contains('cache')) {
          const store = db.createObjectStore('cache', { keyPath: 'key' });
          store.createIndex('expires', 'expires', { unique: false });
        }
      };

      request.onsuccess = () => {
        this.db = request.target.result;
        resolve();
      };

      request.onerror = () => reject(request.error);
    });
  }

  async get(key) {
    if (!this.db) await this.open();

    return new Promise((resolve, reject) => {
      const transaction = this.db.transaction('cache', 'readonly');
      const store = transaction.objectStore('cache');
      const request = store.get(key);

      request.onsuccess = () => {
        const entry = request.result;
        if (!entry || Date.now() > entry.expires) {
          if (entry) this.delete(key);
          resolve(null);
        } else {
          resolve(entry.data);
        }
      };

      request.onerror = () => reject(request.error);
    });
  }

  async set(key, data, ttl = 300000) {
    if (!this.db) await this.open();

    return new Promise((resolve, reject) => {
      const transaction = this.db.transaction('cache', 'readwrite');
      const store = transaction.objectStore('cache');
      const request = store.put({
        key,
        data,
        expires: Date.now() + ttl,
        timestamp: Date.now()
      });

      request.onsuccess = () => resolve();
      request.onerror = () => reject(request.error);
    });
  }

  async delete(key) {
    if (!this.db) await this.open();
    return new Promise((resolve, reject) => {
      const transaction = this.db.transaction('cache', 'readwrite');
      const store = transaction.objectStore('cache');
      store.delete(key);
      transaction.oncomplete = () => resolve();
      transaction.onerror = () => reject(transaction.error);
    });
  }

  async clearExpired() {
    if (!this.db) await this.open();
    const now = Date.now();

    return new Promise((resolve, reject) => {
      const transaction = this.db.transaction('cache', 'readwrite');
      const store = transaction.objectStore('cache');
      const index = store.index('expires');
      const range = IDBKeyRange.upperBound(now);

      index.openCursor(range).onsuccess = (event) => {
        const cursor = event.target.result;
        if (cursor) {
          store.delete(cursor.primaryKey);
          cursor.continue();
        }
      };

      transaction.oncomplete = () => resolve();
      transaction.onerror = () => reject(transaction.error);
    });
  }
}
```

### 4. Unified API Service

```javascript
class ApiService {
  constructor() {
    this.http = new HttpClient('https://api.github.com', {
      headers: { 'Accept': 'application/vnd.github.v3+json' }
    });
    this.cache = new IndexedDBCache();
    this.dedup = new RequestDeduplicator();
    this.retryDelays = [1000, 2000, 4000];
  }

  async fetchWithCache(key, fetcher, ttl = 300000) {
    // Check cache first
    const cached = await this.cache.get(key);
    if (cached) return cached;

    // Deduplicate in-flight requests
    const data = await this.dedup.deduplicate(key, () =>
      this.fetchWithRetry(fetcher)
    );

    // Store in cache
    await this.cache.set(key, data, ttl);
    return data;
  }

  async fetchWithRetry(fetcher, attempt = 0) {
    try {
      return await fetcher();
    } catch (error) {
      if (error instanceof HttpError && error.status < 500) {
        throw error; // Don't retry client errors
      }

      if (attempt >= this.retryDelays.length) {
        throw error; // Out of retries
      }

      // Wait with exponential backoff
      await new Promise(r => setTimeout(r, this.retryDelays[attempt]));
      return this.fetchWithRetry(fetcher, attempt + 1);
    }
  }

  getUser(username) {
    return this.fetchWithCache(
      `user:${username}`,
      () => this.http.request('GET', `/users/${username}`),
      60000 // 1 minute
    );
  }

  getUserRepos(username) {
    return this.fetchWithCache(
      `repos:${username}`,
      () => this.http.request('GET', `/users/${username}/repos`),
      300000 // 5 minutes
    );
  }

  getWeather(lat, lon) {
    const weatherHttp = new HttpClient('https://api.open-meteo.com/v1');
    return this.fetchWithCache(
      `weather:${lat}:${lon}`,
      () => weatherHttp.request('GET', `/forecast?latitude=${lat}&longitude=${lon}&current_weather=true`),
      600000 // 10 minutes (weather doesn't change fast)
    );
  }

  cancelUserRequests(username) {
    this.http.cancelRequest(`GET:https://api.github.com/users/${username}`);
    this.http.cancelRequest(`GET:https://api.github.com/users/${username}/repos`);
  }
}
```

### 5. React Dashboard Components

```javascript
// useApi hook with all features
import { useState, useEffect, useRef, useCallback } from 'react';

function useApi(fetcher, options = {}) {
  const {
    enabled = true,
    onSuccess,
    onError,
    staleTime = 0
  } = options;

  const [state, setState] = useState({
    data: null,
    loading: true,
    error: null,
    isStale: false
  });

  const fetcherRef = useRef(fetcher);
  const mountedRef = useRef(true);

  const execute = useCallback(async () => {
    setState(prev => ({ ...prev, loading: true, error: null }));

    try {
      const data = await fetcherRef.current();
      if (mountedRef.current) {
        setState({ data, loading: false, error: null, isStale: false });
        onSuccess?.(data);
      }
    } catch (error) {
      if (error.name === 'AbortError') return;
      if (mountedRef.current) {
        setState(prev => ({
          ...prev,
          loading: false,
          error: error.message || 'Something went wrong'
        }));
        onError?.(error);
      }
    }
  }, []);

  useEffect(() => {
    mountedRef.current = true;
    if (enabled) execute();
    return () => { mountedRef.current = false; };
  }, [enabled, execute]);

  return {
    ...state,
    refetch: execute
  };
}

// Dashboard Component
function Dashboard({ username, lat, lon }) {
  const api = useMemo(() => new ApiService(), []);

  const user = useApi(() => api.getUser(username));
  const repos = useApi(() => api.getUserRepos(username));
  const weather = useApi(() => api.getWeather(lat, lon));

  // Cleanup on unmount
  useEffect(() => {
    return () => {
      api.cancelAll();
      api.cache.clearExpired();
    };
  }, []);

  return (
    <div className="dashboard">
      <section className="card">
        <h2>GitHub Profile</h2>
        <AsyncData {...user}>
          {(data) => (
            <div>
              <img src={data.avatar_url} alt="" className="avatar" />
              <h3>{data.name}</h3>
              <p>{data.bio}</p>
              <div className="stats">
                <span>Repos: {data.public_repos}</span>
                <span>Followers: {data.followers}</span>
              </div>
            </div>
          )}
        </AsyncData>
      </section>

      <section className="card">
        <h2>Repositories</h2>
        <AsyncData {...repos}>
          {(data) => (
            <ul className="repo-list">
              {data.map(repo => (
                <li key={repo.id}>
                  <a href={repo.html_url}>{repo.name}</a>
                  <p>{repo.description}</p>
                  <span className="language">{repo.language}</span>
                  <span>{repo.stargazers_count} stars</span>
                </li>
              ))}
            </ul>
          )}
        </AsyncData>
      </section>

      <section className="card">
        <h2>Local Weather</h2>
        <AsyncData {...weather}>
          {(data) => (
            <div className="weather">
              <div className="temp">
                {data.current_weather.temperature}°C
              </div>
              <div className="wind">
                Wind: {data.current_weather.windspeed} km/h
              </div>
            </div>
          )}
        </AsyncData>
      </section>
    </div>
  );
}

// AsyncData Component for loading/error/empty states
function AsyncData({ data, loading, error, refetch, children }) {
  if (loading) {
    return (
      <div className="loading-state">
        <div className="spinner" />
        <p>Loading...</p>
      </div>
    );
  }

  if (error) {
    return (
      <div className="error-state" role="alert">
        <h3>Failed to load data</h3>
        <p>{error}</p>
        <button onClick={refetch} className="retry-btn">
          Retry
        </button>
      </div>
    );
  }

  if (!data) {
    return <div className="empty-state">No data available</div>;
  }

  return children(data);
}
```

### 6. Full Error Handling

```javascript
// Error types
class HttpError extends Error {
  constructor(status, body) {
    super(`HTTP ${status}: ${body}`);
    this.status = status;
    this.body = body;
  }
}

class RequestAbortedError extends Error {
  constructor() {
    super('Request was aborted');
    this.name = 'AbortError';
  }
}

class RateLimitError extends HttpError {
  constructor(retryAfter) {
    super(429, 'Rate limited');
    this.retryAfter = retryAfter;
  }
}

// Rate limit handler
async function handleRateLimit(response) {
  if (response.status === 429) {
    const retryAfter = response.headers.get('Retry-After') || 60;
    await new Promise(r => setTimeout(r, retryAfter * 1000));
    return true; // Signal caller to retry
  }
  return false;
}

// GitHub API specific handler
const githubApi = {
  async request(path) {
    const response = await fetch(`https://api.github.com${path}`, {
      headers: {
        'Accept': 'application/vnd.github.v3+json',
        'Authorization': `token ${localStorage.getItem('github_token')}`
      }
    });

    // Check remaining rate limit
    const remaining = response.headers.get('X-RateLimit-Remaining');
    if (remaining === '0') {
      const reset = new Date(
        response.headers.get('X-RateLimit-Reset') * 1000
      );
      throw new RateLimitError((reset - Date.now()) / 1000);
    }

    if (!response.ok) {
      throw new HttpError(response.status, await response.text());
    }

    return response.json();
  }
};
```

## Project Structure

```
dashboard/
  src/
    services/
      HttpClient.js      # HTTP client with abort
      ApiService.js      # Unified API service
      RequestDedup.js    # Request deduplication
    cache/
      IndexedDBCache.js  # Persistent cache
      MemoryCache.js     # In-memory cache
    hooks/
      useApi.js          # Data fetching hook
      useCache.js        # Caching hook
    components/
      Dashboard.jsx      # Main dashboard
      AsyncData.jsx      # Loading/error states
      UserCard.jsx       # User profile card
      RepoList.jsx       # Repository list
      WeatherWidget.jsx  # Weather display
    utils/
      errors.js          # Error classes
      retry.js           # Retry logic
      rateLimit.js       # Rate limit handling
```

## Testing Scenarios

```javascript
// Test cases to verify
const tests = {
  loadingState: 'Show spinner while fetching',
  errorState: 'Show error with retry button on failure',
  emptyState: 'Show empty state when no data',
  cacheHit: 'Show data instantly from cache without loading',
  cacheStale: 'Show stale data while revalidating',
  abortPrevious: 'Cancel in-flight request when parameters change',
  deduplication: 'Only make one request for identical concurrent calls',
  retryLogic: 'Retry on 5xx errors with backoff',
  rateLimit: 'Handle 429 with Retry-After header',
  networkOffline: 'Show offline state gracefully'
};
```

## Key Takeaways

- Always use AbortController to cancel stale requests
- Deduplicate identical concurrent requests to save bandwidth
- Cache API responses in IndexedDB for persistence across sessions
- Implement retry logic with exponential backoff for transient failures
- Handle loading, error, empty, and success states for every data fetch
- Use stale-while-revalidate pattern: show cached data, refresh in background
- Rate limit handling is essential for public APIs
- Clean up subscriptions and abort requests on component unmount
