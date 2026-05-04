---
title: status filtering
deprecated: false
hidden: false
metadata:
  robots: index
---
### The noise problem

You have 500 tests. In your staging environment, 200 of them are skipped — they test features that don't apply there, or they depend on services that aren't available. You run the suite, and your Qase dashboard shows 300 results that matter and 200 skipped entries that don't.

The pass rate looks odd. The results list is cluttered. Someone on the team asks "why did 200 tests skip?" and now you're explaining infrastructure constraints instead of discussing actual test outcomes.

Or a different scenario: you're debugging a specific failure. You run the full suite but you only care about what broke. You don't need 490 passed results in Qase right now — you need the 10 failures, clean and isolated, so you can focus.

Status filtering lets you tell the reporter: don't send results with these statuses to Qase. The tests still run. The reporter still processes them. It just drops them before uploading.

<br />

### How it works

You specify a list of statuses to **exclude**. Any result matching those statuses is silently dropped — it never reaches the Qase API.

```json
{
  "testops": {
    "statusFilter": ["skipped"]
  }
}
```

Or via environment variable:

```bash
export QASE_TESTOPS_STATUS_FILTER="skipped"
```

With this config, passed, failed, and invalid results are sent. Skipped results are not.

The available statuses you can filter:

* `passed`
* `failed`
* `skipped`
* `blocked`
* `invalid`

An empty list (the default) means everything gets sent.

<br />

### When you'd use this

**Skipped tests in partial environments.** Your CI runs the full suite against a service that doesn't support every feature. Half the tests skip. You don't want those skipped entries in Qase because they're not meaningful — the tests weren't supposed to run there in the first place.

```json
{
  "testops": {
    "statusFilter": ["skipped", "blocked"]
  }
}
```

**Failures-only investigation.** Something broke in production. You run the regression suite and want Qase to show only what failed, so the team can triage without scrolling past hundreds of green results.

```bash
QASE_TESTOPS_STATUS_FILTER="passed,skipped" npx playwright test
```

Remove the filter once you're done investigating.

**Keeping dashboards clean during development.** You're building a new test suite. Half the tests are stubs that skip. You don't want those showing up in Qase until they're real.

<br />

### How it interacts with status mapping

If you're using both status mapping and status filtering, the order matters: **mapping happens first, then filtering**.

Say you have this config:

```json
{
  "statusMapping": {
    "invalid": "failed"
  },
  "testops": {
    "statusFilter": ["passed"]
  }
}
```

A test with `invalid` status gets mapped to `failed` first. Then the filter checks: is `failed` in the exclusion list? No. So it gets sent. If you expected `invalid` results to be filtered, you'd need to add `failed` to the filter — because by the time filtering runs, the status has already changed.

This pipeline is the same across all reporters. In the JavaScript commons, the `StatusProcessor` class makes it explicit — `applyMapping()` runs, then `shouldFilter()` checks the result:

```
Original status → Status mapping (rename) → Status filtering (drop) → Send or discard
```

<br />

### What you won't see

Filtered results leave no trace in Qase. They don't appear in the run dashboard, they don't count toward pass rates, they don't create test case history entries. From Qase's perspective, those tests never ran.

If you enable debug mode, the reporter logs each filtered result so you can verify the filter is working as expected. You'll see lines like:

```
Filtering out test result with status: skipped
Filtered 47 results by status filter
```

If you filter everything (all statuses in the list), the reporter will send nothing. The run will exist but be empty. The reporter logs "No results to send after filtering" and moves on.

<br />

### A note on when NOT to filter

Filtering is a blunt tool. It drops results entirely — no record, no history, no way to know those tests ran. If you need the results in Qase but just want them to not affect pass rates or run status, consider using **mute** instead (covered in the Ignore, Mute & Comments page). Muted tests are reported but don't impact the run outcome.

Use filtering when the results genuinely don't belong in Qase for that run. Use muting when the results belong but shouldn't influence the verdict.
