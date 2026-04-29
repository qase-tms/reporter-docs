---
title: The Config file
deprecated: false
hidden: false
metadata:
  robots: index
---
The config file is called `qase.config.json`. Place it in your project root — next to your `package.json`, `pom.xml`, `pyproject.toml`, or whatever your project uses. Every Qase reporter across every language looks for this same file.

Here's a config that covers what you need to get started:

```json
{
  "mode": "testops",
  "fallback": "report",
  "debug": false,
  "testops": {
    "project": "DEMO",
    "run": {
      "title": "Automated regression",
      "complete": true
    },
    "batch": {
      "size": 200
    }
  }
}
```

Let's walk through it.

### `mode`

Controls whether the reporter is active and where it sends results. You'll use `testops` most of the time — it sends results directly to Qase. Set it to `off` when you don't want reporting (e.g., local development). We cover `report` mode in its own section later.

### `fallback`

What the reporter does if the primary mode fails. Set this to `report` — if the API is unreachable, the reporter saves results locally instead of losing them. You can upload them to Qase later. If you leave it as `off`, failed connections just mean lost results.

### `testops.run`

Controls the test run that gets created in Qase:

* **`title`** — what the run is called in Qase. Defaults to "Automated run" with a timestamp, which is fine for getting started but gets noisy fast. We recommend setting a meaningful title, especially in CI where you might include the branch name or build number.
* **`complete`** — whether the reporter closes the run when tests finish. Defaults to `true`, and you almost always want that. Set it to `false` only if you're sending results from multiple machines into the same run (in that case, you'd complete the run separately).

### `testops.batch`

Results are sent to Qase in batches, not one at a time. The `size` controls how many results go in each batch. The default of `200` works for most teams. If you're running a very large suite (10,000+ tests) and want results to appear in Qase sooner, lower it. If you're on a slow network, raise it to reduce the number of API calls.

### `debug`

Set to `true` when something isn't working. The reporter will log detailed information about what it's sending and what responses it gets back. We cover this in the Troubleshooting section.

***

### What's not in this file

You'll notice the API token isn't here. Keep it in an environment variable (`QASE_TESTOPS_API_TOKEN`) — it's a secret and shouldn't live in version control.

There are more options available — environments, run tags, test plans, status mapping, and others. We cover them in later sections as they become relevant. For now, this is everything you need to start reporting.

> **Tip:** Start minimal and add options as you need them. The reporter has sensible defaults for everything except the token and project code.
