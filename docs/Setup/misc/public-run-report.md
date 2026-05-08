---
title: public run report
deprecated: false
hidden: false
metadata:
  robots: index
---
Your nightly regression finishes at 3am. The CI pipeline posts a message in Slack: "Regression complete — 4 failures." Your manager, a product owner, and a client success engineer all want to see the details. None of them have Qase accounts. None of them should need one.

You could screenshot the dashboard and paste it into the thread. You could export a PDF. You could add them all as read-only users to your Qase workspace. All of these work. None of them are good.

<br />

### What this option does

When `showPublicReportLink` is enabled, the reporter does one extra thing after completing the test run: it makes the run dashboard publicly accessible and prints the URL to the console.

The URL looks like this:

```
Public report link: https://app.qase.io/public/report/a1b2c3d4e5f6
```

Anyone with that link can view the run dashboard — pass/fail stats, the full test tree, individual case results with steps, attachments, defects, team stats, and time estimations. They see exactly what you see on the run dashboard, minus the ability to edit anything. No login. No Qase account. No header navigation or access to any other project data.

The link is hash-based. It's not guessable, but it's not secret either — anyone who has it can view the report. Treat it like a Google Docs "anyone with the link" share.

<br />

### When to use it

A few common situations:

* **CI notifications.** Your pipeline finishes, the reporter prints the public link, and your notification script picks it up and posts it to Slack, Teams, or email. Stakeholders click through directly to the results.
* **Sprint reviews and release sign-offs.** You share the link with product owners or release managers who need to see test coverage for a specific build. They get the full dashboard without needing a Qase seat.
* **Client-facing reports.** If you're an agency or consultancy running tests on behalf of a client, the public link lets them see results without onboarding them into your workspace.
* **Incident investigation.** Something broke in production. You run a targeted test suite, share the public link in the incident channel. Everyone involved can see exactly what passed and what didn't.

If none of these apply to you — if everyone who needs to see results already has a Qase account — leave it off. It's disabled by default for a reason: not every run should be publicly accessible.

<br />

### Configuration

In `qase.config.json`:

```json
{
  "testops": {
    "showPublicReportLink": true
  }
}
```

Or via environment variable:

```bash
export QASE_TESTOPS_SHOW_PUBLIC_REPORT_LINK=true
```

The option defaults to `false`. It only takes effect when `run.complete` is `true` — the reporter enables public access as the last step of the completion phase, after all results have been uploaded and the run has been marked complete.

If the API call to enable public access fails (permissions issue, network error), the reporter logs a warning and moves on. It doesn't affect the run or the results.

<br />

### Using the link in CI

The public report URL is printed to stdout at INFO level, so it appears even without debug mode. In a CI pipeline, you can capture it from the console output and forward it wherever you need.

A common pattern: enable `showPublicReportLink` only in CI (via environment variable), and leave it off locally. You don't need a public link when you're running tests on your laptop.

```yaml
# GitHub Actions example
- name: Run tests
  env:
    QASE_MODE: testops
    QASE_TESTOPS_SHOW_PUBLIC_REPORT_LINK: true
```

<br />

### Revoking access

Enabling `showPublicReportLink` from the reporter is a one-way action — it turns on public access for that run. To revoke it, go to the run dashboard in Qase, click the share button, and toggle public access off. The link will stop working immediately.

There's no reporter config to disable a previously-shared link. This is intentional — revoking access is a manual decision, not something that should happen automatically on the next test run.
