---
title: 'Metadata: tests as code'
deprecated: false
hidden: false
metadata:
  robots: index
---
When your reporter sends a result to Qase, it always includes the basics — test name, pass/fail, duration. That's enough to know something broke. It's not enough to know what broke, how much it matters, or who should care.

Metadata closes that gap. It turns a test result from a line in a log into something your whole team can act on.

<br />

### The test-as-code approach

Most teams start with automated tests that live in code and manual test cases that live in a test management tool. Over time, these drift apart. The manual test case says "verify login with SSO," but the automated test actually covers three login methods and was refactored twice since anyone updated the test case.

Metadata solves this by making your code the single source of truth. When you annotate a test with a title, a suite, a severity, and a description — that information flows into Qase every time the test runs. The test case in Qase stays current because it's populated from the code, not from a QA's memory of what the code does.

This matters most when the person writing the test isn't the person reading the results. An SDET writes `test_checkout_flow_edge_case_3`. A QA lead sees it fail in a release run. Without metadata, they're reading function names and guessing. With metadata, they see "Checkout: Apply expired coupon code — Severity: Critical" and know exactly what to do.

<br />

### What you can attach

Every Qase reporter supports the same set of metadata, regardless of language:

**Title** — A human-readable name for the test. Without it, Qase uses the function name, which is often written for developers, not for dashboards. A good title describes the _behavior_, not the implementation: "User can reset password via email" instead of `test_password_reset_email_flow`.

**Suite** — Where the test lives in your test case hierarchy. Without it, Qase infers suites from your file paths or describe blocks — which reflects your _code_ structure, not your _product_ structure. Explicit suites let you organize by feature ("Authentication", "Checkout", "Billing") regardless of how your files are laid out. Most frameworks support nested suites for deeper hierarchy.

**Fields** — This is where metadata gets powerful. Every test case in Qase has system fields:

* **Severity** and **Priority** — When 50 tests fail before a release, these tell you which 5 to look at first. Without them, every failure looks equally urgent.
* **Layer** (unit, API, UI) — Helps teams understand blast radius. A failing unit test means a function is broken. A failing UI test means a user flow is broken.
* **Description**, **Preconditions**, **Postconditions** — The documentation angle. If your test requires a logged-in user with an active subscription, say so in preconditions. This turns your test code into living documentation that non-technical stakeholders can read in Qase without opening your repo.

You can also set custom fields — any field you've defined in your Qase project can be populated from code.

**Tags** — Lightweight labels for filtering and grouping. Use them for cross-cutting concerns that don't fit into suites: `smoke`, `regression`, `flaky`, `payments`, `mobile`. Tags make it easy to answer "show me all failing smoke tests" without building a complex query.

**Ignore** — Tells the reporter to skip this test entirely. The test still runs in your framework, but no result is sent to Qase. Useful for utility tests, setup fixtures, or tests you're actively debugging and don't want polluting your run data.

**Comment** — A note attached to the result. Use it for dynamic context that changes per run — like the URL that was tested, or the seed value for a randomized test.

### An example

Here's a Playwright test with metadata that tells a complete story:

```typescript
import { test, expect } from '@playwright/test';
import { qase } from 'playwright-qase-reporter';

test('Expired coupon shows clear error message', async ({ page }) => {
  qase.id(42);
  qase.title('Checkout: Apply expired coupon code');
  qase.suite('Checkout\tCoupons');
  qase.fields({ severity: 'critical', priority: 'high', layer: 'e2e' });
  qase.comment('Coupon SUMMER2024 expired on 2024-09-01');

  await page.goto('/cart');
  await page.fill('[data-testid="coupon-input"]', 'SUMMER2024');
  await page.click('[data-testid="apply-coupon"]');
  await expect(page.locator('.coupon-error')).toContainText('expired');
});
```

Without the metadata, Qase would show: "Expired coupon shows clear error message — Failed." With it, Qase shows a critical checkout test, filed under Checkout > Coupons, with full context about what was tested and why it matters.

<br />

### Who writes this, and when

Metadata is written by whoever writes the test — typically developers or SDETs. It's consumed by a broader audience: QA leads triaging failures, managers assessing release readiness, on-call engineers investigating production incidents.

> **Tip:** You don't need to add every field to every test. Start with title and suite — they have the highest impact on readability. Add severity and priority to your critical paths. Add descriptions to complex tests that aren't self-explanatory. Build it up gradually.

The best time to add metadata is when you write the test. It's a small upfront cost — a few extra lines — that pays off every time someone other than you looks at the results. If you're retrofitting an existing suite, start with the tests that fail most often. Those are the ones people are already looking at, and they'll benefit most from context.

For the exact syntax in your framework, check the demo repository linked in the previous section. The concepts are the same everywhere — only the annotation style changes.

<br />

<Callout icon="💡">
  **How we'd do it:** If we were starting a new project, we'd add a linting rule or PR review checklist item: "every new test has a title and a suite." It's easier to maintain the habit than to retrofit 500 tests later.
</Callout>
