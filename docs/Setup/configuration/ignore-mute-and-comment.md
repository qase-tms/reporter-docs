---
title: ignore, mute and comment
deprecated: false
hidden: false
metadata:
  robots: index
---
Not every test result belongs in your dashboard. Some tests are work-in-progress. Some are known-flaky and you don't want them blocking a release. Some need extra context that only the test itself knows at runtime. These three features let you control the signal-to-noise ratio of your reporting.

<br />

### Ignore — "don't report this test"

You have tests that run locally for development purposes but shouldn't appear in Qase at all. Setup helpers, experimental tests, tests you're actively rewriting. They execute — they just don't report.

```typescript
test('Prototype for new checkout flow', async ({ page }) => {
  qase.ignore();
  // test runs normally, but Qase never sees it
  await page.goto('https://example.com/checkout-v2');
});
```

This is different from skipping. A skipped test shows up in Qase with a "Skipped" status. An ignored test doesn't show up at all — as if the reporter wasn't there.

Use ignore when:

* A test is under active development and not ready for tracking
* You have utility tests that validate test infrastructure, not product behavior
* You're migrating tests and want to phase in reporting gradually

<br />

### Mute — "report it, but don't let it fail the run"

This is the one you'll reach for most often in practice. You have a test that's flaky, or it covers a known bug that hasn't been fixed yet. You want to keep tracking it — you want to see when it passes, when it fails, whether it's getting better or worse. But you don't want it turning your test run red and blocking a deploy.

A muted test is reported to Qase with its real status (passed, failed, whatever it does). But it's excluded from the run's overall pass/fail calculation. A run with 99 passed tests and 1 muted failure is a passed run.

```typescript
test(qase(42, 'Dashboard chart renders correctly'), async ({ page }) => {
  qase.mute();
  // this test is flaky due to animation timing
  await page.goto('https://example.com/dashboard');
  await expect(page.locator('.chart')).toBeVisible();
});
```

Muting is also available from the Qase UI — you can mute a test case from its properties or directly from the test run dashboard without touching code. The reporter-side `mute()` call and the UI-side mute button achieve the same thing.

Use mute when:

* A test is flaky and you're working on stabilizing it
* A test covers a known bug — you want the failure tracked but not blocking CI
* You're rolling out a new feature and expect temporary failures during the transition

> **Tip:** Muting is a better default than ignoring for problematic tests. Ignored tests are invisible — you might forget they exist. Muted tests stay visible, keep accumulating history, and remind you they need attention.

<br />

### Comments — "add context only the test knows"

When a test fails at 2 AM in CI, the person investigating needs more than a stack trace. They need to know what was happening: which user was logged in, what data was generated, what the API returned. Comments let you attach runtime context to a test result.

```typescript
test('User can complete purchase', async ({ page }) => {
  const user = await createTestUser();
  qase.comment(`Test user: ${user.email}`);

  const order = await createOrder(user);
  qase.comment(`Order ID: ${order.id}, total: ${order.total}`);

  await page.goto(`/orders/${order.id}`);
  await expect(page.locator('.order-status')).toHaveText('Confirmed');
});
```

Comments appear in the test result details in Qase. You can add multiple comments — they accumulate in order. Think of them as a lightweight runtime log that travels with the result.

Use comments when:

* You generate dynamic test data and want to record what was used
* You want to capture environment details (API endpoint, feature flags, database state)
* You're debugging a failure and want to leave breadcrumbs for the next person

> **Note:** Comments are for human-readable context. For structured evidence (screenshots, logs, API responses), use attachments instead.
>
> <br />

### Quick comparison

| Feature     | Reported to Qase? | Affects run status? | When to use                                 |
| ----------- | ----------------- | ------------------- | ------------------------------------------- |
| **Ignore**  | No                | No                  | WIP tests, utility tests, phased migration  |
| **Mute**    | Yes               | No                  | Flaky tests, known bugs, temporary failures |
| **Comment** | Yes (as metadata) | No                  | Runtime context, debugging breadcrumbs      |

For the exact syntax in your framework, check the demo repository.
