---
title: report mode as a fallback
deprecated: false
hidden: false
metadata:
  robots: index
---
Sometimes you can't send results to Qase during the test run. Maybe your security team won't allow outbound API calls from the build server. Maybe you're on a plane :) Maybe you're iterating locally and don't want every experimental run cluttering your project's history. Or maybe you just want to see what the reporter captures before you commit to sending it anywhere.

Report mode is for all of these situations. Instead of talking to the Qase API, the reporter writes everything — results, steps, attachments, metadata — to a local JSON file. Same data, no network required.

<Image align="center" src="https://files.readme.io/1f7eb31896c89a189087d9438706f5c5da89b43568b43a86a5285fb261c37c9f-report_mode_fallback_v4.svg" />

<br />

## How it works

Set the mode to `report`:

```
QASE_MODE=report
```

When your tests finish, the reporter writes a JSON file to `./build/qase-report` (or wherever you configure it). That file contains exactly what would have been sent to Qase — every result, every step, every attachment path. Nothing is lost.

You can customize the output location:

```json
{
  "mode": "report",
  "report": {
    "connection": {
      "local": {
        "path": "./my-reports",
        "format": "json"
      }
    }
  }
}
```

<br />

## What you can do with the report

This is where it gets interesting. A JSON file sitting on disk isn't very useful by itself. The `qase-report` CLI turns it into something you can actually work with.

**View it locally as an interactive dashboard:**

```bash
npx qase-report open ./build/qase-report
```

This opens a local HTML report in your browser — a full dashboard with pass/fail breakdown, step timelines, attachments, and history. It's useful for debugging a flaky test without leaving your terminal, or for reviewing results on a branch before merging. If a colleague asks "what broke in the last run?" and they don't have a Qase account, you can share this with them.

**Upload it to Qase later:**

```bash
npx qase-report upload ./build/qase-report \
  --project PROJ \
  --token <your-token>
```

This sends the saved results to Qase as if the reporter had done it in real time. The test run appears in your dashboard, complete with all metadata, steps, and attachments. Nobody can tell the difference.

**Export it as a static HTML file:**

```bash
npx qase-report generate ./build/qase-report -o ./report.html
```

This produces a single `.html` file you can attach to a PR, email to a stakeholder, or archive alongside your build artifacts. It renders the same dashboard — no server needed, just open it in a browser.

<br />

## When teams actually use this

**Local development.** You're writing tests and want to see what the reporter captures without sending anything to Qase. Set `mode: report` in your config file, set `QASE_MODE=testops` in CI. Locally, you get a JSON file you can inspect or open as a dashboard. In CI, results go to Qase. Same config file, different behavior — exactly how the layered configuration system was designed to work.

**Air-gapped or restricted environments.** Some organizations run tests on build servers that can't make outbound API calls. Report mode lets the reporter do its job without a network. A separate pipeline step — or even a manual process — uploads the results afterward.

**PR review evidence.** Generate a static HTML report and attach it to your pull request. Reviewers see exactly what passed, what failed, and what the steps looked like — without needing Qase access. This is especially useful for open-source projects or cross-team reviews where not everyone has a Qase seat.

**Archival and compliance.** Some teams need to keep test evidence alongside their build artifacts for audit purposes. The JSON report is a complete, portable record of the test run. The HTML export makes it human-readable without any tooling.

> **Tip:** Report mode pairs well with the fallback setting. If you set `fallback: report`, the reporter will automatically switch to report mode if it can't reach the Qase API — so you never lose results, even if there's an outage.
