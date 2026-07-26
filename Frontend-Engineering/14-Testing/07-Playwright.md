# Playwright

## What is Playwright?

Playwright is a browser automation framework from Microsoft for cross-browser E2E testing. It supports Chromium, Firefox, and WebKit with a single API.

## Setup

```bash
npm init playwright@latest
# Or in existing project:
npm install -D @playwright/test
npx playwright install
```

```ts
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './e2e',
  timeout: 30000,
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  reporter: [['html', { outputFolder: 'playwright-report' }]],
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    { name: 'firefox', use: { ...devices['Desktop Firefox'] } },
    { name: 'Mobile Safari', use: { ...devices['iPhone 13'] } },
  ],
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
    timeout: 120000,
  },
});
```

## Browser Contexts

Isolated browser sessions (like incognito windows).

```ts
test('isolated contexts', async ({ browser }) => {
  const context1 = await browser.newContext();
  const page1 = await context1.newPage();

  const context2 = await browser.newContext({
    storageState: 'auth.json',
    locale: 'fr-FR',
    viewport: { width: 390, height: 844 },
    geolocation: { latitude: 48.8566, longitude: 2.3522 },
    permissions: ['geolocation'],
  });
  const page2 = await context2.newPage();

  // context1 and context2 share nothing
});
```

## Locators

Playwright locators auto-wait for elements to be actionable.

```ts
// Role-based (preferred)
page.getByRole('button', { name: /submit/i });
page.getByRole('textbox', { name: 'Email' });
page.getByRole('heading', { level: 1 });
page.getByRole('link', { name: 'Dashboard' });

// Text, label, placeholder
page.getByText('Welcome back!');
page.getByLabel('Password');
page.getByPlaceholder('Search...');

// Test ID
page.getByTestId('checkout-button');

// Chaining & filtering
page.locator('.product-list')
  .getByRole('listitem')
  .filter({ hasText: 'Laptop' })
  .getByRole('button');

// CSS (last resort)
page.locator('[data-custom="value"]');
```

### Assertions

```ts
import { expect } from '@playwright/test';

const button = page.getByRole('button', { name: 'Submit' });

// Visibility & state
await expect(button).toBeVisible();
await expect(button).toBeHidden();
await expect(button).toBeEnabled();
await expect(button).toBeDisabled();
await expect(button).toBeChecked();
await expect(button).toBeFocused();

// Text & attributes
await expect(button).toHaveText('Submit');
await expect(button).toContainText('Sub');
await expect(button).toHaveAttribute('type', 'submit');
await expect(button).toHaveCSS('background-color', 'rgb(0, 0, 255)');

// Value & count
await expect(page.getByLabel('Name')).toHaveValue('Alice');
await expect(page.getByRole('listitem')).toHaveCount(3);

// URL & title
await expect(page).toHaveURL('/dashboard');
await expect(page).toHaveTitle('My App');
```

## Actions

```ts
await page.getByText('Submit').click();
await page.getByRole('button').click({ force: true });
await page.getByRole('button').dblclick();

// Type / Fill
await page.getByLabel('Email').fill('user@test.com');

// Keyboard
await page.keyboard.press('Enter');
await page.keyboard.press('Control+A');

// Select, Check, Upload
await page.getByLabel('Country').selectOption('US');
await page.getByLabel('I agree').check();
await page.getByLabel('Avatar').setInputFiles('photo.jpg');

// Drag & scroll
await page.locator('#source').dragTo(page.locator('#target'));
await page.getByText('Footer').scrollIntoViewIfNeeded();
```

## Network Mocking

```ts
test('mock API response', async ({ page }) => {
  await page.route('**/api/user', async (route) => {
    await route.fulfill({
      status: 200,
      contentType: 'application/json',
      body: JSON.stringify({ id: 1, name: 'Mock User' }),
    });
  });
  await page.goto('/profile');
  await expect(page.getByText('Mock User')).toBeVisible();
});

test('abort analytics requests', async ({ page }) => {
  await page.route('**/analytics/**', route => route.abort());
  await page.goto('/');
});

test('modify API response', async ({ page }) => {
  await page.route('**/api/products', async (route) => {
    const response = await route.fetch();
    const body = await response.json();
    body.forEach(p => p.price *= 0.5);
    await route.fulfill({ response, body: JSON.stringify(body) });
  });
  await page.goto('/products');
  await expect(page.getByText('$')).toBeVisible();
});

test('wait for API call', async ({ page }) => {
  const responsePromise = page.waitForResponse('**/api/submit');
  await page.getByText('Submit').click();
  const response = await responsePromise;
  expect(response.status()).toBe(200);
});
```

## Screenshot Testing

```ts
test('visual regression test', async ({ page }) => {
  await page.goto('/');

  await expect(page).toHaveScreenshot('homepage.png');
  await expect(page.locator('.hero')).toHaveScreenshot('hero.png');

  // Mask dynamic content
  await expect(page).toHaveScreenshot({
    mask: [page.locator('[data-testid="clock"]')],
    maskColor: '#FF00FF',
  });

  // Full page
  await page.screenshot({ path: 'screenshots/fullpage.png', fullPage: true });
});

// Update snapshots: npx playwright test --update-snapshots
```

## Trace Viewer

```ts
// Manual trace capture
test('debug with trace', async ({ page, context }) => {
  await context.tracing.start({ screenshots: true, snapshots: true });
  await page.goto('/checkout');
  await page.getByText('Buy Now').click();
  await context.tracing.stop({ path: 'trace-checkout.zip' });
});

// View: npx playwright show-trace trace-checkout.zip
```

## Example Test Suite: E-Commerce

```ts
describe('Checkout Flow', () => {
  test('complete purchase', async ({ page }) => {
    await page.goto('/products/1');
    await page.getByText('Add to Cart').click();
    await page.getByText('Checkout').click();

    await page.getByLabel('Full Name').fill('Alice Smith');
    await page.getByLabel('Address').fill('123 Main St');
    await page.getByLabel('City').fill('Portland');
    await page.getByLabel('ZIP').fill('97201');
    await page.getByText('Continue to Payment').click();

    const stripe = page.frameLocator('iframe[name="stripe"]');
    await stripe.getByLabel('Card number').fill('4242424242424242');
    await stripe.getByLabel('Expiry').fill('12/28');
    await stripe.getByLabel('CVC').fill('123');
    await page.getByText('Pay Now').click();

    await expect(page.getByText('Order Confirmed!')).toBeVisible();
    await expect(page.getByTestId('order-number')).toBeVisible();
  });

  test('shows validation errors', async ({ page }) => {
    await page.goto('/checkout');
    await page.getByText('Place Order').click();
    await expect(page.getByRole('alert')).toHaveCount(4);
  });

  test('handles server error', async ({ page }) => {
    await page.route('**/api/orders', route =>
      route.fulfill({ status: 500, body: 'Server error' })
    );
    await page.goto('/products/1');
    await page.getByText('Add to Cart').click();
    await page.getByText('Checkout').click();
    await page.getByLabel('Full Name').fill('Alice');
    await page.getByText('Place Order').click();
    await expect(page.getByText('Something went wrong')).toBeVisible();
  });
});
```

## Authentication Fixtures

```ts
// global-setup.ts
import { chromium } from '@playwright/test';
setup('authenticate', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill('test@example.com');
  await page.getByLabel('Password').fill('password123');
  await page.getByText('Sign In').click();
  await page.waitForURL('/dashboard');
  await page.context().storageState({ path: 'auth.json' });
});

// playwright.config.ts
globalSetup: './global-setup.ts',

// Usage in tests
test.use({ storageState: 'auth.json' });
```

## CI Integration

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20 }
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
```

## Summary

| Feature | API |
|---------|-----|
| Navigation | `page.goto(url)` |
| Locators | `page.getByRole()`, `getByText()`, `getByTestId()` |
| Actions | `.click()`, `.fill()`, `.selectOption()` |
| Assertions | `expect(locator).toBeVisible()`, `.toHaveText()` |
| Network | `page.route()`, `page.waitForResponse()` |
| Screenshots | `expect(page).toHaveScreenshot()` |
| Trace | `context.tracing.start/stop()` |
| Auth | `storageState` for session reuse |
