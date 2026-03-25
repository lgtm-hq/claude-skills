---
name: test-ui
description: Playwright E2E testing best practices. Use when writing browser tests, visual regression, or accessibility tests. Enforces user-facing locators, auto-waiting, and Page Object Model.
---

# Playwright E2E Testing

Write browser E2E tests following Playwright best practices.

## Commands

- `bunx playwright test` - Run all tests
- `bunx playwright test --grep @smoke` - Run tagged tests
- `bunx playwright test --ui` - Interactive UI mode
- `UPDATE_SNAPSHOTS=1 bunx playwright test` - Update visual baselines

## Locators (Priority Order)

Use user-facing locators. Avoid CSS/XPath selectors.

```typescript
// BEST: Semantic locators
page.getByRole('button', { name: 'Submit' })
page.getByLabel('Email')
page.getByPlaceholder('Search...')
page.getByText('Welcome')

// GOOD: Test IDs for non-semantic elements
page.getByTestId('theme-trigger')

// AVOID: CSS selectors (fragile, implementation-coupled)
page.locator('#submit-btn')
page.locator('.form-input')
page.locator('div > button.primary')
```

## Auto-Waiting

Never use `waitForTimeout()`. Playwright auto-waits for elements.

```typescript
// WRONG: Manual timeouts
await page.waitForTimeout(1000);
await button.click();

// CORRECT: Auto-waiting assertions
await expect(button).toBeVisible();
await button.click();

// CORRECT: Poll for async state
await expect.poll(async () => {
  return await page.evaluate(() => localStorage.getItem('theme'));
}).toBe('dark');

// CORRECT: Wait for specific conditions
await page.waitForLoadState('networkidle');
await page.waitForURL(/\/dashboard/);
```

## Web-First Assertions

Use Playwright's auto-retrying assertions.

```typescript
// CORRECT: Web-first (auto-retries)
await expect(page.getByRole('heading')).toHaveText('Dashboard');
await expect(page.locator('html')).toHaveAttribute('data-theme', 'dark');
await expect(button).toBeEnabled();
await expect(dialog).toBeVisible();

// AVOID: Manual checks (no retry)
const text = await heading.textContent();
expect(text).toBe('Dashboard');
```

## Page Object Model

Encapsulate page interactions in classes.

```typescript
// pages/HomePage.ts
export class HomePage {
  readonly page: Page;

  constructor(page: Page) {
    this.page = page;
  }

  async goto(): Promise<void> {
    await this.page.goto('/');
    await this.page.waitForLoadState('domcontentloaded');
  }

  getNavLink(name: string): Locator {
    return this.page.getByTestId(`nav-${name}`);
  }

  async selectTheme(themeId: string): Promise<void> {
    await this.page.getByTestId('theme-trigger').click();
    await this.page.getByTestId(`theme-${themeId}`).click();
  }
}
```

## Fixtures

Extend base test with page objects and cleanup.

```typescript
// fixtures.ts
import { test as base } from '@playwright/test';
import { HomePage } from './pages/HomePage';

export const test = base.extend<{ homePage: HomePage }>({
  homePage: async ({ page }, use) => {
    const homePage = new HomePage(page);
    await use(homePage);
    // Cleanup runs after test
    await page.evaluate(() => localStorage.clear());
  },
});

export { expect } from '@playwright/test';
```

## Test Structure

```typescript
import { test, expect } from './fixtures';

test.describe('Theme Switching', () => {
  test.beforeEach(async ({ homePage }) => {
    await homePage.goto();
  });

  test('applies selected theme', async ({ homePage }) => {
    await test.step('select dark theme', async () => {
      await homePage.selectTheme('dark');
    });

    await test.step('verify theme applied', async () => {
      await expect(homePage.page.locator('html'))
        .toHaveAttribute('data-theme', 'dark');
    });
  });
});
```

## Visual Testing

```typescript
test('homepage renders correctly', async ({ page }) => {
  await page.goto('/');
  await expect(page).toHaveScreenshot('homepage.png', {
    maxDiffPixels: 50,
    threshold: 0.05,
  });
});

// Skip non-Chromium for consistency
test.skip(({ browserName }) => browserName !== 'chromium',
  'Visual tests run only on Chromium');
```

## Configuration (playwright.config.ts)

```typescript
export default defineConfig({
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 0 : 1,
  workers: process.env.CI ? 2 : undefined,

  use: {
    screenshot: 'only-on-failure',
    trace: 'retain-on-failure',
  },

  expect: {
    toHaveScreenshot: {
      animations: 'disabled',
      maxDiffPixels: 50,
    },
  },

  webServer: {
    command: 'npm run serve',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

## Test Tags

Use tags for selective test runs.

```typescript
test('critical flow @smoke', async ({ page }) => { ... });
test('screenshot comparison @visual', async ({ page }) => { ... });
test('screen reader support @a11y', async ({ page }) => { ... });
```

Run with: `bunx playwright test --grep @smoke`

## Network Interception

```typescript
// Mock API responses
await page.route('**/api/user', (route) => {
  route.fulfill({ json: { name: 'Test User' } });
});

// Simulate failures
await page.route('**/*.css', (route) => {
  route.abort('failed');
});

// Cleanup after test
await page.unroute('**/api/user');
```

## Checklist

- [ ] Locators use `getByRole`, `getByTestId`, `getByLabel` (not CSS selectors)
- [ ] No `waitForTimeout()` calls (use auto-waiting assertions)
- [ ] Assertions use `expect()` from Playwright (not raw checks)
- [ ] Page objects encapsulate page-specific interactions
- [ ] Fixtures handle setup/cleanup via `use()` pattern
- [ ] Tests are independent (no shared state between tests)
- [ ] Visual tests disabled for non-Chromium browsers
- [ ] Network routes cleaned up in finally blocks
