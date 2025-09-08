---
title: Parameterizing Playwright Projects
date: 2025-09-08 16:23:31.704621
background: bg-[#2858ee]
tags:
  - playwright
  - testing
  - best-practices
categories:
  - Programming
  - Testing
intro: |
  Why fixtures enable clean Playwright projects.
plugins:
  - copyCode
  - tooltip
---

## Parameterizing Playwright Projects - CheatSheet11

### Table of Contents

- [Fixture Setup](#fixture-setup)
- [Hide POM Init](#hide-pom-init)
- [Add an Option](#add-an-option)
- [Use Option in Tests](#use-option-in-tests)
- [Use Option in Fixture](#use-option-in-fixture)
- [Override: Global](#override-global)
- [Override: Project](#override-project)
- [Override: Per-test](#override-per-test)
- [Where Options Plug In (Diagram)](#where-options-plug-in-diagram)
- [Override Spots (Recap)](#override-spots-recap)

### Fixture Setup {.col-span-2}

#### base.ts — extend + export

```ts
// 1) import & alias
import { test as base } from '@playwright/test';
import { DashboardPage } from './poms/dashboard';

// 2) extend core with fixtures
export const test = base.extend({
  dashboardPage: async ({ page }, use) => {
    const pom = new DashboardPage(page, {
      /* cfg later */
    });
    await use(pom); // expose to tests
  }
});

export { expect } from '@playwright/test';
```

### Hide POM Init {.col-span-2}

#### Before → After (clean tests)

```ts
// BEFORE: init POM in every test
import { DashboardPage } from './poms/dashboard-page';
test('create check', async ({ page }) => {
  const dash = new DashboardPage(page, { email: 'a', password: '...' });
  await dash.login();
  await dash.createCheck();
});

// AFTER: fixture injects ready-to-use POM
test('create check', async ({ dashboardPage }) => {
  await dashboardPage.createCheck();
});
```

### Add an Option {.col-span-2}

#### Declare configurable option fixture

```ts
// base.ts
import { test as base } from '@playwright/test';
export const test = base.extend({
  // tuple: [value, { option: true }]
  user: [
    { email: 'stefan@checklyhq.com', password: '...' },
    { option: true } // mark configurable
  ],

  dashboardPage: async ({ page }, use) => {
    /* set later */
  }
});
export { expect } from '@playwright/test';
```

### Use Option in Tests {.col-span-2}

#### Consume option value

```ts
// any.spec.ts
import { test, expect } from './base';

test('create check', async ({ dashboardPage, user }) => {
  // `user` is available in tests
  await expect(user.email).toBeTruthy();
  await dashboardPage.createCheck();
});
```

### Use Option in Fixture {.col-span-2}

#### Wire option → POM constructor

```ts
// base.ts
import { test as base } from '@playwright/test';
import { DashboardPage } from './poms/dashboard';

export const test = base.extend({
  user: [{ email: 'stefan@checklyhq.com', password: '...' }, { option: true }],
  dashboardPage: async ({ page, user }, use) => {
    const pom = new DashboardPage(page, user); // use option here
    await pom.login();
    await use(pom);
  }
});
export { expect } from '@playwright/test';
```

### Override: Global

#### playwright.config.ts — `use` block

```ts
// playwright.config.ts
import { defineConfig } from '@playwright/test';
import type { TestOptions } from './base'; // if you typed it

export default defineConfig<TestOptions>({
  use: {
    user: { email: 'me@company.com', password: '...' } // global default
  }
});
```

### Override: Project

#### Per project (devices + user)

```ts
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  projects: [
    {
      name: 'user-a',
      use: {
        ...devices['Desktop Chrome'],
        user: { email: 'stefan@checklyhq.com', password: '...' } // per project
      }
    }
  ]
});
```

### Override: Per-test

#### One-off override via `test.use`

```ts
// any.spec.ts
import { test } from './base';

// local override for this file/scope
test.use({ user: { email: 'hello@checklyhq.com', password: '' } });

test('create check (other user)', async ({ dashboardPage }) => {
  await dashboardPage.createCheck();
});
```

### Where Options Plug In (Diagram)

```mermaid
flowchart LR
  A[base.extend\nuser:{...}, option:true] --> B[Fixtures\nconsume option]
  A --> C[Tests\nuse { user }]
  A --> D[Config use{}\n(global)]
  A --> E[Projects[].use{}\n(per project)]
  A --> F[test.use{}\n(per test/spec)]
  B --> G[Construct POM\nwith user]
  C --> H[Run steps\nwith dashboardPage]
```

### Override Spots (Recap) {.cols-2}

#### Table {.show-header .bold-first}

| Location         | Scope           |
| ---------------- | --------------- |
| `base.extend`    | default value   |
| `config.use`     | repo/global     |
| `projects[].use` | per project run |
| `test.use`       | per test/spec   |

#### Notes {.marker-round}

- Fixtures hide setup; tests stay clean.
- Mark with `{ option: true }` to make it overridable.
- Both tests _and_ fixtures can access the option.
- Use the same `use` API everywhere for consistency.

## Any Suggestions?

- [💡 I Got an IDEA](https://github.com/Fechin/reference/blob/main/source/_posts/playwright-best-practices.md)

<style>
em { font-size: 0.785em; }
strong {font-weight: 400;}
ul.collapsible > li > pre { padding-left: 0; padding-right: 0; font-size: 0.925em;}
</style>
