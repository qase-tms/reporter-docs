---
title: getting support
deprecated: false
hidden: false
metadata:
  robots: index
---
We've covered debug mode and the log file. This section is about what to do with that information.

## Start with what you have

Most reporter issues fall into a few categories: results not appearing, results landing on the wrong test case, attachments missing, or the run not completing. Before reaching out to anyone, collect three things:

1. The **log file** (or console output with `debug: true`)
2. Your **`qase.config.json`** (remove the token)
3. The **reporter version** (from your `package.json`, `pom.xml`, `requirements.txt`, or equivalent)

These three pieces answer most of the questions support would ask in a first reply. Having them ready saves a round trip.

## Contact support

Qase support is available via live chat and email. Share the three items above along with a short description of what you expected vs. what happened. For most issues — a misconfigured option, a permissions problem, a version incompatibility — this is enough. The support team can cross-reference your config and logs against known issues and get you an answer quickly.

If the debug log shows the problem clearly (wrong project code, expired token, API returning 403), you may not even need to reach out. The Debug Mode article walks through reading those logs.

## For harder problems

Some issues aren't obvious from the logs alone. Maybe the problem only happens with a specific test structure, or a particular combination of framework options, or only in your CI environment. These are harder to diagnose through description alone because the details matter — the exact test file, the exact config, the exact sequence of events.

This is where the demo repositories help.

Each framework has a demo repo on the `qase-tms` GitHub organization — the same ones listed in the [How to Link](link) article. For example:

* `github.com/qase-tms/playwright-demo`
* `github.com/qase-tms/pytest-demo`
* `github.com/qase-tms/cucumberjs-demo`
* _(and so on for each supported framework)_

These repos are minimal, working setups. They have the reporter installed, a `qase.config.json`, and a handful of example tests. They run out of the box.

### The reproduction workflow

When an issue is complex or specific to your setup:

1. **Clone the demo repo** for your framework
2. **Add the minimum code needed to reproduce the problem** — a test file, a dependency, a config change. Strip away everything that isn't related to the issue.
3. **Push it to a branch** (on your own fork or a fresh repo)
4. **Share the branch link** with Qase support alongside your log file and config

This gives the support team (and engineering, if it gets escalated) something they can `git clone`, `npm install`, and run. No guessing about your project structure, no "can you try X and tell me what happens" back-and-forth. They see exactly what you see.

This step isn't mandatory. Many issues get resolved without it. But for the ones that don't — the subtle, environment-specific, hard-to-describe problems — a reproduction branch can cut the resolution time significantly. Instead of several exchanges narrowing down the problem, engineering gets a runnable reproduction on the first escalation.

### What makes a good reproduction

* **Minimal.** One test file that shows the problem. Not your entire test suite.
* **Self-contained.** Someone should be able to clone it, set their token and project code, and run it. No dependencies on your internal systems.
* **Documented.** A sentence or two in the PR or branch description: "Run `npx playwright test`. Expected: result appears in Qase. Actual: result is missing. Log file attached."

The demo repos are a good starting point because they already have the reporter wired up. You're adding the broken scenario on top of a known-working setup, which also helps isolate whether the problem is in your configuration or in the reporter itself.

## After the fix

When an issue gets resolved — whether through a config change, a workaround, or a reporter patch — the solution gets documented. If it was a bug, the fix lands in the reporter's changelog. If it was a configuration misunderstanding, it may end up in these docs.

If you used a reproduction branch, it served its purpose. The problem is captured, the fix is verified, and the next person who hits the same issue has a documented path forward.
