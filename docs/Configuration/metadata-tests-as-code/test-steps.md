---
title: test steps
deprecated: false
hidden: false
metadata:
  robots: index
---
Without steps, a failed test tells you one thing: it failed. You open the result in Qase, see a red status, maybe an error message and a stack trace. Then you go read the test code to figure out what actually happened. If someone else wrote the test, that's a longer conversation.

**Steps change this**. They turn a test result from a verdict into a narrative — a sequence of named actions, each with its own status, duration, and (optionally) attachments. When a test fails, you don't just know _that_ it failed — you know it got through login, loaded the dashboard, clicked "Export," and broke on "Verify download completed."

 That's the difference between "go investigate" and "I already know where to look."

<br />

### What the reporter captures per step

Each step records:

* **Name** — what you called it
* **Status** — passed or failed, determined automatically (did the step throw an exception?)
* **Attachments** — screenshots, logs, or data you attach inside the step go _with_ that step, not the overall test
* **Error details** — if the step failed, the exception and stack trace are captured at the step level

You don't need to manage any of this manually. The reporter handles timing and status. You just define the boundaries.

<br />

### In practice

**Debugging across teams.** The person who wrote the test isn't always the person investigating the failure. Steps give anyone on the team — QA, dev, even a manager looking at a release report — a readable account of what happened without opening an IDE.

**Flaky test diagnosis.** A test that fails intermittently is hard to debug from pass/fail alone. With steps, you start seeing patterns: it's always the "Wait for dashboard to load" step, or it's always the third API call. Steps turn "it's flaky" into "the third-party service times out under load."

**Living documentation.** Well-named steps describe what a test _does_, not just what it's _called_. When your test has steps like "Add item to cart → Proceed to checkout → Enter payment details → Confirm order," anyone reading the result in Qase understands the test's intent — even months later, even if they've never seen the code.

### A quick example

In Playwright, you can use either the native `test.step()` or `qase.step()` — both are captured:

```typescript
import { test, expect } from '@playwright/test';
import { qase } from 'playwright-qase-reporter';

test('User can complete checkout', async ({ page }) => {
  await qase.step('Navigate to product page', async () => {
    await page.goto('https://store.example.com/products/widget');
  });

  await qase.step('Add item to cart', async () => {
    await page.click('[data-testid="add-to-cart"]');
    await expect(page.locator('.cart-count')).toHaveText('1');
  });

  await qase.step('Complete checkout', async () => {
    await page.click('[data-testid="checkout"]');
    await page.fill('#email', 'buyer@example.com');
    await page.click('[data-testid="confirm"]');
  });

  await qase.step('Verify order confirmation', async () => {
    await expect(page.locator('.order-status')).toContainText('confirmed');
  });
});
```

If the checkout step fails, the Qase result shows three green steps and one red one. You know exactly where to look.

### Nested steps

Steps can contain other steps. This is useful for complex flows where a high-level action (like "Authenticate") is made up of smaller actions (like "Open login page," "Enter credentials," "Click submit"). The reporter captures the hierarchy, so in Qase you see a collapsible tree:

```
Authentication flow              [PASSED]
  ├─ Open login page             [PASSED]
  ├─ Enter credentials           [PASSED]
  └─ Submit login form           [PASSED]
Complete checkout                [FAILED]
  ├─ Add item to cart            [PASSED]
  ├─ Enter payment details       [PASSED]
  └─ Confirm order               [FAILED]
```

> **Tip:** Keep nesting to two or three levels. Deeper than that and the report becomes harder to scan than the code itself.

### When to add steps — and when not to

Not every test needs steps. A simple unit test that asserts one thing is self-explanatory — wrapping it in a step adds noise, not clarity.

Steps earn their keep in **end-to-end and integration tests** — tests that do multiple things in sequence, where the failure point isn't obvious from the test name alone. If your test touches more than one page, calls more than one API, or has a setup-action-verify structure that spans more than a few lines, steps will save you time when something breaks.

> **How we'd approach it:** Start by adding steps to your most-investigated tests — the ones that fail often or that multiple people need to understand. Don't retrofit your entire suite at once. Let the habit build naturally as you write new tests.

For the exact step syntax in your framework, check the demo repository linked in the Get Started section.

***

Steps are supported across all ecosystems with consistent behavior — each step captures name, status, duration, and attachments:

**Playwright** supports both native `test.step()` and `qase.step()`: [26-cite-0](#26-cite-0)

**Python** supports both decorator and context manager patterns for steps: [26-cite-1](#26-cite-1)

The Python step implementation shows the internal lifecycle — `start_step` → `yield` → `finish_step`, with automatic exception handling: [26-cite-2](#26-cite-2)

**Java** uses `@Step` annotations with AspectJ interception for automatic timing and status: [26-cite-3](#26-cite-3)

**Java** supports parameter interpolation in step names (`{paramName}` syntax): [26-cite-4](#26-cite-4)

**C#** uses `[Step]` attributes on methods: [26-cite-5](#26-cite-5)

**Go** has a dedicated `Step()` function with nested step support via a step stack: [26-cite-6](#26-cite-6)

**PHP (Pest)** supports simple markers, callbacks, nested steps, and expected results: [26-cite-7](#26-cite-7)

**BDD/Cucumber** frameworks automatically capture Gherkin Given/When/Then steps — no annotations needed: [26-cite-8](#26-cite-8)

Attachments inside steps are automatically routed to that step (not the overall test): [26-cite-9](#26-cite-9)

Best practices from the Java docs — atomic steps, descriptive names, limit nesting, attach evidence within steps: [26-cite-10](#26-cite-10)

How does this land? Ready for #11 (Enriching Results: Attachments)?
