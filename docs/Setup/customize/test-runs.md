---
title: test runs
deprecated: false
hidden: false
metadata:
  robots: index
---
Every time you run your tests with the reporter enabled, a test run appears in Qase. If you've followed the setup so far, you've probably already seen a few — they show up with names like `Automated run 2025-04-29T14:32:00Z` and pile up fast.

That's fine for getting started. But once your team is looking at Qase daily — checking what failed overnight, reviewing results before a release, figuring out which PR broke the login flow — those auto-generated timestamps stop being useful. You need runs that tell a story at a glance.

This page covers how to shape what the reporter creates.

## Naming your runs

The default title is a timestamp. It works, but it doesn't answer the question your teammate is actually asking: _"What was this run for?"_

```json
{
  "testops": {
    "run": {
      "title": "Nightly regression – staging"
    }
  }
}
```

In CI, you'll probably want the title to include dynamic context — the branch name, the build number, the commit. Set it as an environment variable:

```bash
export QASE_TESTOPS_RUN_TITLE="PR #${PR_NUMBER} – ${GITHUB_SHA:0:7}"
```

Now your runs list reads like a changelog instead of a log file.

A description adds context that doesn't fit in a title — what triggered this run, what it covers, anything someone would need six months from now when they're looking at historical results:

```bash
export QASE_TESTOPS_RUN_DESCRIPTION="Full regression triggered by deploy to staging. Branch: $BRANCH_NAME"
```

<br />

## Tagging runs

Tags let you filter and group runs in Qase. Think of them as the labels you wish your CI pipeline had.

```json
{
  "testops": {
    "run": {
      "tags": ["regression", "staging"]
    }
  }
}
```

Some patterns that work well:

* **By trigger**: `nightly`, `pr-check`, `release-candidate`
* **By scope**: `smoke`, `regression`, `e2e`
* **By target**: `staging`, `production`, `preview`

When your team has 200 runs and someone asks "show me all the nightly regressions from this month" — tags are how they find them.

<br />

## Run completion

By default, the reporter **completes** the run when your test suite finishes. A completed run is locked — no more results can be added. This is usually what you want: one suite execution, one run, done.

```json
{
  "testops": {
    "run": {
      "complete": true
    }
  }
}
```

Set `complete` to `false` when you're feeding results into the same run from multiple sources — say, a smoke suite runs first, then a full regression suite appends to the same run. The first job shouldn't lock the run before the second job gets to it. (More on this in the CI section.)

<br />

## Using an existing run

By default, the reporter creates a new run every time. But sometimes the run already exists — you created it manually in Qase, or another job created it earlier in the pipeline, or you're using a test plan that pre-creates runs.

```json
{
  "testops": {
    "run": {
      "id": 42,
      "complete": false
    }
  }
}
```

In CI, you'd typically pass this as an environment variable:

```bash
export QASE_TESTOPS_RUN_ID=42
```

When `run.id` is set, the reporter skips creation entirely and sends results straight to that run. Pair it with `complete: false` if other jobs will also contribute results.

> **Go users:** The Go reporter requires an existing run ID — it does not auto-create runs. Create one via the Qase UI, the API, or the `qasectl` CLI before running your tests.

<br />

## Linking runs to external systems

If your team tracks work in Jira, you can link a run to an external issue. This closes the loop between "we deployed PROJ-1234" and "here are the test results that validated it."

```json
{
  "testops": {
    "run": {
      "externalLink": {
        "type": "JiraCloud",
        "link": "PROJ-1234"
      }
    }
  }
}
```

Supported link types: `JiraCloud`, `JiraServer`, `GitHub`, `GitLab`, `Linear`, `Asana`, `Trello`, `YouTrack`, `CustomField`.

In CI, this is powerful when automated — extract the ticket ID from the branch name or PR title and pass it as an env var. Now every run is traceable back to the work that triggered it, without anyone doing it manually.

<br />

## Sharing results

If you want to share a run with someone who doesn't have a Qase account — a stakeholder, a client, a contractor — enable the public report link:

```json
{
  "testops": {
    "showPublicReportLink": true
  }
}
```

The reporter logs the public URL to the console after the run completes. Anyone with the link can view the results — no login required.

<br />

## What we recommend

For most teams running tests in CI, this config covers the common ground:

```json
{
  "mode": "off",
  "testops": {
    "project": "PROJ",
    "run": {
      "title": "Regression",
      "complete": true
    }
  }
}
```

Then in CI:

```bash
export QASE_MODE=testops
export QASE_TESTOPS_API_TOKEN=$QASE_TOKEN
export QASE_TESTOPS_RUN_TITLE="$CI_JOB_NAME – build #$CI_BUILD_NUMBER"
export QASE_TESTOPS_RUN_TAGS="nightly,staging"
```

The config file holds the structure. CI injects the context.
