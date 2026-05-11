---
title: defect logging
deprecated: false
hidden: false
metadata:
  robots: index
---
When a test fails, you usually want to track it as a defect. The reporter can do this automatically — when enabled, every failed test result creates a defect in Qase.

<br />

## The risk: environment failures

Before enabling this, consider what happens when your CI environment fails. If the database is unreachable, the API is down, or credentials expire, your entire test run fails — not because the tests are broken, but because the environment is broken.

With `testops.defect` enabled, that one infrastructure problem becomes hundreds of defects. One per failed test. You then have to manually close them all, and your defect section becomes noisy and unusable.

This is the most common complaint about auto-defect: it works great for real test failures, but it amplifies environment failures into defect spam.

<br />

## How it works

When `testops.defect` is enabled, the reporter adds `defect: true` to each failed test result it sends to Qase. Qase then creates a defect and links it to both the test case and the test run.

The auto-created defect includes:

* **Title**: The test case title
* **Actual result**: The test's comment or error message
* **Severity**: The test case's default severity
* **Links**: Back to the test case and test run

<br />

## When to use it

**Stable environments.** If your CI environment is reliable and failures are almost always actual test failures, auto-defect saves time. You don't have to manually create defects for each failure.

**Targeted runs.** If you run a small, focused test suite (e.g., smoke tests on a staging environment), the blast radius is smaller. Even if something goes wrong, you're dealing with 10 defects, not 500.

**Regression suites with health checks.** If you run a large regression suite, add a pre-check that verifies the environment is healthy before the main run starts. If the health check fails, skip the run entirely — don't send results to Qase.

<br />

## When to leave it off

**Flaky environments.** If your CI environment has intermittent issues, auto-defect will create noise. You'll spend more time closing spurious defects than investigating real failures.

**Large suites.** The larger your test suite, the higher the cost of a mistake. A 500-test suite with an environment failure creates 500 defects. A 20-test suite creates 20.

**Early in adoption.** When you're first setting up the reporter, leave auto-defect off. Run a few cycles manually to understand your failure patterns. Once you're confident in your environment stability, consider enabling it.

<br />

## Alternatives

**Manual defect creation.** Review failed results in Qase and create defects selectively. This takes more time but gives you control over what becomes a defect.

**Post-run analysis.** Use the test run dashboard to identify patterns. If 50 tests fail with the same error message, it's likely an environment issue — create one defect, not 50.

**Test plans with review.** Use test plans to group tests, and review the run before marking it complete. If the run looks like an environment failure, delete it and re-run.

<br />

**Note:** This is separate from the "Default create/attach defect checkbox" setting in Qase's project settings. That setting affects manual testing in the UI — whether the defect checkbox is pre-checked when a tester marks a case as failed. The `testops.defect` config option is for automated tests only.
