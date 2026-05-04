---
title: status mapping
deprecated: false
hidden: false
metadata:
  robots: index
---
## The five statuses

Every test result sent to Qase carries one of five statuses:

| Status    | Meaning                                                                                                         |
| --------- | --------------------------------------------------------------------------------------------------------------- |
| `passed`  | Test ran and all assertions passed                                                                              |
| `failed`  | Test ran but an assertion did not pass — likely a bug in the application                                        |
| `invalid` | Test could not complete due to a non-assertion error — likely a problem with the test itself or the environment |
| `skipped` | Test was skipped (e.g., `@Disabled`, `pytest.mark.skip`, conditional skip)                                      |
| `blocked` | Test was blocked from running                                                                                   |

The one that surprises most people is `invalid`. If you've only ever seen "passed" and "failed" from your test runner, seeing a third failure category in Qase can be confusing.

<br />

## What "invalid" actually means

Your test framework reports everything that isn't green as "failed." The reporter looks deeper. It inspects the error type to determine whether the failure was an assertion — something your test explicitly checked — or something else entirely.

An assertion failure means the test ran correctly but the application didn't behave as expected. A `NullPointerException`, a `TypeError: Cannot read property of undefined`, a network timeout, a missing step definition — those aren't assertion failures. They mean the test couldn't even finish what it was trying to do.

The reporter classifies the first kind as `failed` and the second as `invalid`.

This distinction matters because the two categories call for different responses. A `failed` test means someone should look at the application. An `invalid` test means someone should look at the test code or the test environment. When your dashboard shows 12 failures and 3 invalids, you know that 3 of those aren't product bugs — they're infrastructure noise. That's useful information when you're deciding what to investigate first.

The detection works automatically. The reporter checks the exception type against known assertion libraries:

* **Java**: JUnit (`org.junit`), AssertJ (`org.assertj`), OpenTest4J (`org.opentest4j`)
* **JavaScript/Playwright**: `expect()` failures, `AssertionError`, Playwright's `expect` matchers. Timeouts and interrupted tests are classified as `failed` when the runner explicitly reports `timedOut` or `interrupted` status.
* **Python**: `AssertionError` and subclasses
* **PHP**: `PHPUnit\Framework\AssertionFailedError`, PHP's `AssertionError`

Everything else becomes `invalid`.

<br />

## When you don't want the distinction

Some teams prefer simplicity. They want all non-passing results to be `failed`, period. Maybe their workflow doesn't distinguish between "the app is broken" and "the test is broken" — both need fixing either way.

That's what status mapping is for.

```json
{
  "statusMapping": {
    "invalid": "failed"
  }
}
```

This transforms every `invalid` result into `failed` before it reaches Qase. The mapping happens inside the reporter, before results are sent. Qase never sees the original status.

You can also set it via environment variable:

```bash
QASE_STATUS_MAPPING="invalid=failed"
```

The format is `from=to`, comma-separated for multiple mappings.

<br />

## Other mapping scenarios

**Treat skipped tests as passed** — useful when your pass rate calculation shouldn't be penalized by intentionally skipped tests:

```json
{
  "statusMapping": {
    "skipped": "passed"
  }
}
```

**Multiple mappings at once:**

```json
{
  "statusMapping": {
    "invalid": "failed",
    "blocked": "skipped"
  }
}
```

**Validation rules:**

* Both source and target must be valid statuses (`passed`, `failed`, `skipped`, `blocked`, `invalid`)
* Mapping a status to itself is ignored
* Case-sensitive — use lowercase
* Invalid mappings are ignored with a warning; they don't break the reporter

<br />

## Status filtering: dropping results entirely

Status mapping transforms a result's status. Status filtering removes the result from reporting altogether.

If you don't want skipped tests cluttering your Qase dashboard at all — not mapped to something else, just gone — use `statusFilter`:

```json
{
  "testops": {
    "statusFilter": ["skipped", "blocked"]
  }
}
```

Results with those statuses won't be sent to Qase. They still run locally, they still appear in your framework's console output, but Qase never receives them.

Environment variable:

```bash
QASE_TESTOPS_STATUS_FILTER="skipped,blocked"
```

<br />

## The pipeline: mapping happens first

When both are configured, the order is:

```
test finishes → reporter determines status → mapping applied → filter checked → sent (or dropped)
```

This means mapping can change whether a result gets filtered. Example:

```json
{
  "statusMapping": {
    "invalid": "failed"
  },
  "testops": {
    "statusFilter": ["invalid"]
  }
}
```

Here, `invalid` results get mapped to `failed` first. By the time the filter runs, there are no `invalid` results left to filter. All former-invalid results are now `failed` and get sent normally.

If you reversed the intent — you want to drop invalids but also map them — the mapping wins because it runs first.

<br />

## When to use which

**Use status mapping when** you want to change how results are categorized but still want them reported. The most common case: `"invalid": "failed"` for teams that don't need the distinction.

**Use status filtering when** you want to reduce noise by not reporting certain results at all. Common case: filtering out `skipped` tests so your Qase dashboard only shows tests that actually ran.

**Use both when** you want to reclassify some statuses and drop others. Just remember the order: mapping first, then filtering.

<br />

## Debugging

Enable debug mode to see both features in action:

```
Status mapping applied: invalid -> failed
Checking filter: status="failed", filter=["skipped"]
Filter result: NOT FILTERED
```

If a result disappears and you're not sure why, the debug log will show whether it was mapped to something unexpected or filtered out.
