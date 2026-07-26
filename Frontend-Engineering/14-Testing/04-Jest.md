# Jest

## What is Jest?

Jest is a batteries-included JavaScript testing framework from Meta. It provides a test runner, assertion library, mocking utilities, code coverage, and snapshot testing out of the box.

## Setup

```bash
npm install --save-dev jest @types/jest ts-jest
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

```ts
// jest.config.ts
import type { Config } from 'jest';

const config: Config = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  moduleNameMapper: {
    '\\.(css|less|scss)$': 'identity-obj-proxy',
    '^@/(.*)$': '<rootDir>/src/$1',
  },
  setupFilesAfterSetup: ['@testing-library/jest-dom'],
  coverageThreshold: {
    global: { statements: 80, branches: 75, functions: 80, lines: 80 },
  },
};
export default config;
```

## Globals

Jest injects globals — no imports needed.

| Global | Purpose |
|--------|---------|
| `describe(name, fn)` | Group related tests |
| `test` / `it` | Define a test case |
| `expect(value)` | Create an assertion |
| `beforeAll` / `afterAll` | Run once before/after all tests |
| `beforeEach` / `afterEach` | Run before/after each test |

```ts
describe('UserService', () => {
  beforeAll(() => { /* connect to DB */ });
  afterAll(() => { /* disconnect */ });

  describe('getUser', () => {
    test('returns user by id', () => { /* ... */ });
    test('throws when user not found', () => { /* ... */ });
  });
});
```

## Matchers

```ts
// Equality
expect(a).toBe(b);                    // Object.is
expect(obj).toEqual({ a: 1 });        // Deep equality
expect(response).toMatchObject({ status: 200 }); // Partial

// Truthiness
expect(null).toBeNull();
expect(undefined).toBeUndefined();
expect(1).toBeDefined();
expect(true).toBeTruthy();
expect(0).toBeFalsy();

// Numbers
expect(10).toBeGreaterThan(5);
expect(3.14).toBeCloseTo(3.14, 2);

// Strings & arrays
expect('hello').toMatch(/hello/);
expect([1, 2, 3]).toContain(2);
expect([1, 2, 3]).toHaveLength(3);

// Errors
expect(() => { throw new Error('fail'); }).toThrow('fail');
expect(() => {}).not.toThrow();
```

### Custom Matchers

```ts
expect.extend({
  toBeWithinRange(received, floor, ceiling) {
    const pass = received >= floor && received <= ceiling;
    return { pass, message: () => `expected ${received} to be within ${floor}-${ceiling}` };
  },
});
expect(100).toBeWithinRange(90, 110);
```

## Mocks

### jest.fn()

```ts
const mock = jest.fn();
mock();
expect(mock).toHaveBeenCalled();
expect(mock).toHaveBeenCalledTimes(1);

const add = jest.fn((a, b) => a + b);
expect(add(2, 3)).toBe(5);

// Return values
const r = jest.fn()
  .mockReturnValueOnce(10)
  .mockReturnValue(30);
r(); // 10
r(); // 30

// Promises
const fetch = jest.fn().mockResolvedValue({ data: 'ok' });
await fetch(); // { data: 'ok' }
```

### jest.spyOn()

```ts
const logger = { info: (msg: string) => console.log(msg) };
const spy = jest.spyOn(logger, 'info').mockImplementation(() => {});
logger.info('test');
expect(spy).toHaveBeenCalledWith('test');
spy.mockRestore();
```

### jest.mock()

```ts
// __mocks__/axios.ts
export default { get: jest.fn().mockResolvedValue({ data: { user: 'Alice' } }) };

// test file
jest.mock('axios');
import axios from 'axios';
test('fetches user', async () => {
  const user = await fetchUser(1);
  expect(axios.get).toHaveBeenCalledWith('/api/users/1');
  expect(user.name).toBe('Alice');
});
```

### jest.mock() with factory

```ts
jest.mock('next/navigation', () => ({
  useRouter: () => ({ push: jest.fn(), replace: jest.fn() }),
  usePathname: () => '/dashboard',
}));
```

## Timers

```ts
jest.useFakeTimers();

test('setTimeout', () => {
  const cb = jest.fn();
  setTimeout(cb, 1000);
  jest.advanceTimersByTime(1000);
  expect(cb).toHaveBeenCalledTimes(1);
});

test('setInterval', () => {
  const cb = jest.fn();
  setInterval(cb, 500);
  jest.advanceTimersByTime(2000);
  expect(cb).toHaveBeenCalledTimes(4);
});

jest.runAllTimers();
jest.useRealTimers();
```

## Snapshots

```ts
test('renders user card', () => {
  const tree = renderer
    .create(<UserCard user={{ name: 'Alice', role: 'Admin' }} />)
    .toJSON();
  expect(tree).toMatchSnapshot();
});
```

```bash
npx jest --updateSnapshot  # Update when UI intentionally changes
```

### Inline Snapshots

```ts
test('formatGreeting', () => {
  expect(formatGreeting('Alice')).toMatchInlineSnapshot(`"Hello, Alice!"`);
});
```

## Coverage Reports

```bash
npx jest --coverage
npx jest --coverage --coverageReporters=html  # HTML report
```

```bash
--------------------------|---------|----------|---------|---------|-------------------
File                      | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
--------------------------|---------|----------|---------|---------|-------------------
All files                 |   88.24 |    76.92 |   85.71 |   88.24 |
 src/auth/AuthService     |   95.00 |    85.71 |  100.00 |   95.00 | 42
 src/utils/validators     |   82.61 |    66.67 |   75.00 |   82.61 | 18,23
 src/components/LoginForm |   91.67 |    80.00 |   83.33 |   91.67 |
--------------------------|---------|----------|---------|---------|-------------------
```

## Real-World Scenario: Testing an API Client

```ts
// api/client.ts
export class ApiClient {
  constructor(private baseUrl: string) {}

  async getUser(id: string) {
    const res = await fetch(`${this.baseUrl}/users/${id}`);
    if (!res.ok) throw new Error(`HTTP ${res.status}`);
    return res.json();
  }
}
```

```ts
// api/client.test.ts
const mockFetch = jest.fn();
globalThis.fetch = mockFetch;
beforeEach(() => mockFetch.mockReset());

describe('ApiClient', () => {
  const client = new ApiClient('https://api.example.com');

  test('getUser returns data', async () => {
    mockFetch.mockResolvedValue({
      ok: true,
      json: () => Promise.resolve({ id: '1', name: 'Alice' }),
    });
    const user = await client.getUser('1');
    expect(user.name).toBe('Alice');
    expect(mockFetch).toHaveBeenCalledWith('https://api.example.com/users/1');
  });

  test('getUser throws on 404', async () => {
    mockFetch.mockResolvedValue({ ok: false, status: 404 });
    await expect(client.getUser('999')).rejects.toThrow('HTTP 404');
  });
});
```

## Summary

| Feature | API |
|---------|-----|
| Test runner | `jest`, `jest --watch` |
| Test structure | `describe`, `it`/`test` |
| Assertions | `expect(value).toXxx()` |
| Matchers | `toBe`, `toEqual`, `toContain`, `toThrow` |
| Function mocks | `jest.fn()`, `jest.spyOn()` |
| Module mocks | `jest.mock('module')` |
| Fake timers | `jest.useFakeTimers()` |
| Snapshots | `toMatchSnapshot()` |
| Coverage | `jest --coverage` |
