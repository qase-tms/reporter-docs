---
title: fallback
deprecated: false
hidden: false
metadata:
  robots: index
---
When the reporter can't reach the Qase API — bad token, network issue, outage — it has two options: lose the results, or save them locally. The `fallback` setting controls which.

```json
{
  "mode": "testops",
  "fallback": "report"
}
```

With `fallback` set to `report`, a failed API connection causes the reporter to switch to report mode mid-run. Your tests keep running. Results are written to disk instead of the API. You can upload them later with `qase-report`.

<Image align="center" src="https://files.readme.io/1f7eb31896c89a189087d9438706f5c5da89b43568b43a86a5285fb261c37c9f-report_mode_fallback_v4.svg" />

With `fallback` set to `off` (the default), a failed connection means results are silently dropped. Tests still run — the reporter never interrupts your suite — but nothing is recorded.

<br />

## What triggers a fallback

The switch happens when the reporter fails to create or communicate with a test run. Common causes:

* Expired or invalid API token
* Network timeout or firewall blocking `api.qase.io`
* Qase API downtime

The switch is permanent for that run. Once the reporter falls back, it stays on the fallback for the remainder of the suite. It won't retry the API.

<br />

## What happens to results already sent

If the reporter successfully created a run and sent some batches before the connection dropped, those results are already in Qase. The fallback captures everything from the failure point onward. You'll have a partial run in Qase and a local report with the rest.

This is worth knowing if you're investigating a run that looks incomplete — check whether a local report was generated alongside it.

<br />

## When to enable it

If your CI pipeline runs in an environment where network access to Qase is reliable, `fallback: "off"` is fine. You'll notice failures in the logs and can re-run.

If you run tests in environments with unreliable connectivity, or if losing results from a long test suite would be costly to reproduce, set `fallback: "report"`. The cost is a few JSON files on disk. The benefit is you never lose a run.

<br />

## Configuration

| Setting              | Value                   | Effect                                       |
| -------------------- | ----------------------- | -------------------------------------------- |
| `fallback: "report"` | Save to disk on failure | Results recoverable via `qase-report upload` |
| `fallback: "off"`    | Drop results on failure | Default behavior                             |

The fallback setting only applies when `mode` is `testops`. In `report` or `off` mode, there's nothing to fall back from.
