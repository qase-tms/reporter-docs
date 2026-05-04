---
title: capture logs
deprecated: false
hidden: false
metadata:
  robots: index
---
### When a test fails

The result in Qase tells you _that_ it failed and _where_ — the assertion, the error message, the stack trace. But it doesn't tell you what was happening _around_ the failure.

Your test code probably writes to stdout and stderr during execution. HTTP response bodies. Database query results. `console.log` breadcrumbs you added while debugging. Warnings from libraries. Connection timeouts. All of that output exists during the test run, then disappears.

If you're running locally, you can scroll up in your terminal. If you're running in CI, you can dig through thousands of log lines to find the relevant section. If someone else is investigating the failure a week later, that CI log may already be gone.

<br />

### What capture logs does

When enabled, the reporter collects everything your test wrote to stdout and stderr and attaches it to the test result as `stdout.log` and `stderr.log` files. They appear alongside your other attachments — screenshots, traces, API responses — directly on the result in Qase.

That's it. No filtering, no formatting. Raw output, preserved where it's useful.

<br />

### When to enable it

**API and integration tests.** These tests often log request/response pairs, status codes, headers. When a test fails because the API returned something unexpected, the log attachment shows you exactly what came back — without re-running anything.

**Tests with debug logging.** If your test code or application code writes diagnostic info to the console — connection strings, feature flag states, cache hits/misses — capture logs preserves that context on every run. When a test that passed yesterday fails today, you can compare the two log attachments side by side.

**Flaky tests.** The hardest part of debugging a flaky test is that it doesn't fail when you're watching. With capture logs enabled, every run — pass or fail — has its output preserved. When the flake happens, the evidence is already collected.

**CI environments.** CI logs are ephemeral and shared across all tests. Finding the output for one specific test means searching through hundreds of lines from other tests running in the same job. Capture logs isolates each test's output and attaches it to the right result.

<br />

### When to leave it off

Capture logs adds attachment upload overhead. If your tests don't write meaningful output — pure UI tests that only interact through the browser, simple unit tests with no logging — the stdout/stderr files will be empty or trivial. No point uploading empty files on every run.

Also consider volume. If you have 2,000 tests and each produces a log file, that's 2,000+ extra attachments per run. For large suites, you might enable it selectively — only on a subset of tests that benefit from it, or only when investigating a specific issue.

<br />

### A practical pattern

A common approach: leave capture logs off by default, enable it via environment variable when you need to investigate.

```bash
# Normal CI run — no log capture
npx playwright test

# Investigation mode — capture everything
QASE_CAPTURE_LOGS=true npx playwright test
```

This keeps your normal runs lean and gives you a diagnostic lever to pull when something goes wrong.

<br />

### Configuration

The option is called `captureLogs` in most reporters, set via `QASE_CAPTURE_LOGS` environment variable or in `qase.config.json`:

```json
{
  "captureLogs": true
}
```

One exception: in Pytest, the option lives under `framework.pytest.captureLogs` in the config file, though the environment variable `QASE_CAPTURE_LOGS` works the same way. Check your framework's demo repo for the exact placement.
