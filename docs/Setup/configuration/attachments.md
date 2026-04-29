---
title: attachments
deprecated: false
hidden: false
metadata:
  robots: index
---
Attachments turn a test result from a verdict into evidence. A result that says "failed" tells you something went wrong. A result with a screenshot of the broken page, the API response body, and the browser console log tells you _what_ went wrong — without anyone having to reproduce it first.

<br />

### The debugging tax

Every team has a version of this workflow: a test fails in CI, someone opens the pipeline log, scrolls through hundreds of lines of output, can't find the relevant bit, checks out the branch, runs the test locally, and it passes. Two hours gone.

Attachments short-circuit this. When a test fails with a screenshot of the page, a dump of the API response, or a snippet of the application log, the person investigating starts with context instead of hunting for it. Multiply that across a team and across weeks, and the time saved is significant.

<br />

### What you can attach

You can attach anything the reporter can read — files from disk or content from memory:

* **Screenshots** — what the UI looked like at a specific moment
* **API responses** — the actual payload your test received
* **Logs** — application logs, browser console output, network traces
* **Data files** — JSON, XML, CSV, HTML reports, PDFs

There are two ways to attach:

* **From a file path** — point to a file on disk. The reporter reads it and uploads it. MIME type is inferred automatically.
* **From memory** — pass content (string or bytes) directly. You provide the filename and MIME type.

Here's a Playwright example showing both:

```typescript
import { test, expect } from '@playwright/test';
import { qase } from 'playwright-qase-reporter';

test('Checkout flow', async ({ page }) => {
  await page.goto('https://example.com/checkout');

  // Attach a file from disk
  qase.attach({ paths: './test-data/order.json' });

  // Attach a screenshot from memory
  const screenshot = await page.screenshot();
  qase.attach({
    name: 'checkout-page.png',
    content: screenshot,
    contentType: 'image/png',
  });

  await expect(page.locator('.order-summary')).toBeVisible();
});
```

<br />

### Attachments and steps

If you read the previous section on steps, you already know that steps give structure to your test execution. Attachments follow the same structure — when you attach something inside a step, it goes to that step. When you attach outside a step, it goes to the overall test result.

This is automatic. There's no separate "attach to step" method. The reporter knows where you are in the execution and routes accordingly.

```typescript
test('Login flow', async ({ page }) => {
  await qase.step('Enter credentials', async () => {
    await page.fill('#email', 'user@example.com');
    await page.fill('#password', 'secret');

    // This screenshot attaches to the "Enter credentials" step
    const screenshot = await page.screenshot();
    qase.attach({
      name: 'credentials-entered.png',
      content: screenshot,
      contentType: 'image/png',
    });
  });

  await qase.step('Submit form', async () => {
    await page.click('button[type="submit"]');
  });
});
```

When someone reviews this result in Qase, they see the screenshot right next to the step it belongs to — not floating at the bottom of the result with no context.

<br />

### Attach on failure, not on every run

The most common pattern — and the one we recommend — is attaching evidence when a test fails, not on every run. Screenshots of passing tests may add to the noise, best to restrict them to retries and failures. 

Most frameworks give you a way to detect failure and attach conditionally. In Playwright, a common pattern is a try/catch that captures evidence before re-throwing:

```typescript
test('Product page loads', async ({ page }) => {
  try {
    await page.goto('https://example.com/products');
    await expect(page.locator('.product-grid')).toBeVisible();
  } catch (error) {
    const screenshot = await page.screenshot({ fullPage: true });
    qase.attach({
      name: 'failure-screenshot.png',
      content: screenshot,
      contentType: 'image/png',
    });
    throw error;
  }
});
```

<br />

<Callout icon="💡">
  **How we'd do it:** Consider setting up a global fixture or hook that captures a screenshot and browser console log on every failure automatically. That way, individual tests don't need try/catch blocks — the evidence collection is centralized and consistent.
</Callout>

<br />

### Upload limits

The Qase API enforces these limits per upload request:

* **32 MB** per individual file
* **128 MB** total per request
* **20 files** per request

The reporter handles batching automatically — if you attach 30 files, it splits them into two requests. Files over 32 MB are silently skipped with an error in the debug log. You don't need to manage this yourself, but it's worth knowing if you're attaching large video recordings or heap dumps. Compress first, or consider whether a summary would serve better than the raw file.

<br />

### What makes a good attachment

Not everything deserves to be attached. A 50 MB video of a passing test adds upload time and storage cost without helping anyone. A 200 KB screenshot of a broken page saves hours of debugging.

Good attachments are:

* **Relevant** — they show something you can't see from the test name and status alone
* **Scoped** — attached to the step where the action happened, not dumped at the test level
* **Conditional** — captured on failure (or on specific conditions), not on every run
* **Named well** — `fail-input-password.png` tells you more than `screenshot-1.png`

For the exact attachment syntax in your framework, check the demo repository linked in the Get Started section.
