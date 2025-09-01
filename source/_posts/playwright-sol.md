---
title: Playwright
date: 2025-07-23 16:23:31.704621
background: bg-[#2858ee]
tags:
  - playwright
  - testing
categories:
  - Programming
intro: |
  Playwright solutions articles.
---

## Playwright Test Troubleshooting {.cols-2}

### Overcoming Flakey Tests

Problem: Element not fully visible/interactive after click, causing timeouts, especially with elements at the bottom of the page.
HTML report logs show element is outside viewport despite scrolling attempts.

```log
TimeoutError: locator.click: Timeout 10000ms exceeded.
waiting for ...
  locator resolved to <...>
attempting click action
  scrolling into view if needed
  done scrolling
  element is outside of the viewport
retrying click action ...
```

### force: true

Alternative to ensure click interaction when auto-scrolling fails.
Can be applied to click action on a locator.

```js
await this.page
  .getByRole('option', { name: howManyItems, exact: true })
  .click({ force: true });
```

## Scrolling Strategies

### scrollIntoViewIfNeeded()

Playwright function to scroll an element into view.
May not work if browser incorrectly determines the element is already visible.

```js
// Attempted solution
await locator.scrollIntoViewIfNeeded();
```

### page.evaluate() {.col-span-2}

Execute code within the page's context.
Can be used to manually control scrolling with `window.scrollTo()`.
Accounts for lazy loading by scrolling incrementally.

```js
await this.page.evaluate(async () => {
  const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms));
  for (let i = 0; i < document.body.scrollHeight; i += 100) {
    window.scrollTo(0, i);
    await delay(100);
  }
});
```

## Playwright Codegen Workflow {.cols-3}

### Default Codegen Limitations {.col-span-1}

`npx playwright codegen` command.
Does not run setup files or load custom fixtures (e.g., authentication).
Cannot access pre-configured data or states.

```bash
npx playwright codegen
```

### Launching Codegen with Setup {.col-span-2}

Create an empty test with setup code (e.g., `test.use()` for storageState).
Add `await page.pause()` to pause execution.
Run the test in headed mode.

```js
import { test, expect } from '@playwright/test';

test.describe('Admin my account specs', () => {
  test.use({ storageState: '.auth/admin.json' });

  test('validate admin dashboard', async ({ page }) => {
    await page.goto('/admin/dashboard');
    await page.pause();
  });
});
```

### Running with Headed Mode {.col-span-2}

Use the `--headed` flag to run the test with a visible browser window.
Execution pauses at `page.pause()`, opening the Playwright Inspector.

```bash
npx playwright test tests/account/account.spec.ts --headed
```

### Using Playwright Inspector {.col-span-1}

After `page.pause()`, the Inspector opens.
Click "Record" to start recording interactions.
Recorded actions appear in a new script in the Inspector.
Copy generated code from the Inspector to your test file.
