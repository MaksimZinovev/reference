---
title: Playwright best practices
date: 2025-07-23 16:23:31.704621
background: bg-[#2858ee]
tags:
  - playwright
  - testing
  - best-practices
categories:
  - Programming
  - Testing
intro: |
  Playwright best practices.
plugins:
  - copyCode
  - tooltip
  - mermaid
---

## Playwright — Best Practices for Writing Tests

_Based on Checkly’s “Best Practices for Writing Tests in Playwright”
(Updated: Aug 14, 2025)._ ([Checkly][1])

### Table of Contents

- [Keep Tests Short](#keep-tests-short)
- [Keep Tests Focused](#keep-tests-focused)
- [Keep Tests Independent](#keep-tests-independent)
- [Readable Tests](#readable-tests)
- [Diagram](#diagram)
- [Takeaways](#takeaways)

## Keep Tests Short {.cols-3}

### Checklist (short, simple, readable)

- One primary user flow
- ≤ \~10 actions & a few asserts
- No verbose helpers inline
- Prefer small, named utils
- Fail fast; avoid noise
- Update cost stays low ([Checkly][1])

### Example — Short Login Test

```ts
// tests/login.spec.ts
import { test, expect } from '@playwright/test';

test('user logs in', async ({ page }) => {
  await page.goto('/login');
  await page.getByLabel('Email').fill('u@example.com');
  await page.getByLabel('Password').fill('secret');
  await page.getByRole('button', { name: 'Sign in' }).click();
  await expect(page).toHaveURL(/\/dashboard$/); // clear, quick
});
```

### Keep Helpers Out of the Way

```ts
// prefer: small, named utils
export async function signIn(page, email, pass) {
  await page.getByLabel('Email').fill(email);
  await page.getByLabel('Password').fill(pass);
  await page.getByRole('button', { name: 'Sign in' }).click();
}
```

## Keep Tests Focused {.cols-3}

### Anti-pattern — Combined Features (hard to read)

```ts
// ❌ mixes checkout + coupons; failures unclear
test('checkout + coupon', async ({ page }) => {
  await addItem(page);
  await applyCoupon(page, 'SAVE10'); // feature A
  await checkout(page); // feature B
  await expect(page.getByText('Thanks')).toBeVisible();
});
```

### Refactor — One Feature per Test (clear intent)

```ts
// ✅ coupon feature
test('apply coupon at cart', async ({ page }) => {
  await addItem(page);
  await applyCoupon(page, 'SAVE10');
  await expect(page.getByText('10% off')).toBeVisible();
});

// ✅ checkout flow
test('complete checkout', async ({ page }) => {
  await addItem(page);
  await checkout(page);
  await expect(page.getByText('Thanks')).toBeVisible();
});
```

### Rule of Thumb

- Each test answers **one question**
- Assertions stick to that feature
- Split when asserts span features ([Checkly][1])

## Keep Tests Independent {.cols-3}

### Anti-pattern — Chained Tests

```ts
// ❌ test2 depends on test1 creating a user
test('create user', async () => {
  /* ... */
});
test('login as created user', async () => {
  /* ... */
});
test('checkout as that user', async () => {
  /* ... */
});
```

### Refactor — Self-Contained with Setup

```ts
import { test, expect, request } from '@playwright/test';

test.beforeEach(async ({}, testInfo) => {
  // create fresh user for each test
  // fast, reliable via API when possible
  const req = await request.newContext();
  await req.post('/api/users', { data: { email: unique(), pass: 'x' } });
});

test('login works', async ({ page }) => {
  await page.goto('/login');
  await signIn(page, currentEmail(), 'x');
  await expect(page).toHaveURL(/dashboard$/);
});

test('checkout works', async ({ page }) => {
  await page.goto('/login');
  await signIn(page, currentEmail(), 'x');
  await checkout(page);
  await expect(page.getByText('Thanks')).toBeVisible();
});
```

### Why Independence?

- Run in **any order / parallel**
- Easier maintenance & triage
- No hidden coupling across files ([Checkly][1])

## Readable Tests {.cols-2}

### Naming & Structure Tips

```ts
// clear titles communicate status fast
test.describe('Cart — coupons', () => {
  test('shows applied discount', async ({ page }) => {
    /* ... */
  });
});

// prefer clear locators (user-visible)
page.getByRole('button', { name: 'Pay now' });
page.getByLabel('Email').fill('u@example.com');
```

### Small, Reusable Setup (no duplication)

```ts
// factor shared setup without coupling tests
async function seedUser(req, data) {
  return req.post('/api/users', { data });
}
// call from beforeEach or fixtures (per test)
```

> Readability = faster updates & quicker interpretation. ([Checkly][1])

## Diagram {.cols-2}

### Dependent vs. Independent Tests

```mermaid
flowchart TB
  subgraph Coupled (bad)
    A[test1: create user] --> B[test2: login as user]
    B --> C[test3: checkout as user]
  end

  subgraph Independent (good)
    A2[test1: create user]
    B2[test2: setup: create → login]
    C2[test3: setup: create → login → checkout]
  end
```

_Independent tests can run in parallel and in any order._ ([Checkly][1])

## Takeaways {.cols-1 .marker-round}

- Keep tests **short**, **focused**, **independent**
- Prefer API setup/teardown when possible
- Optimize for human readability & quick triage ([Checkly][1])

[1]: https://www.checklyhq.com/learn/playwright/writing-tests/ 'Best Practices for Writing Tests in Playwright'

## Any Suggestions?

- [💡 I Got an IDEA](https://github.com/Fechin/reference/blob/main/source/_posts/playwright-best-practices.md)

<style>
em { font-size: 0.785em; }
strong {font-weight: 400;}
ul.collapsible > li > pre { padding-left: 0; padding-right: 0; font-size: 0.925em;}
</style>
