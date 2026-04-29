---
title: How does it work?
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  pages:
    - slug: prerequisites
      title: Prerequisites
      type: basic
---
Qase reporters sit between your test framework and Qase. When your tests run, the reporter listens to your framework's events — test started, test passed, test failed — and sends the results to Qase in the background. You don't change how you write tests or how you run them.

<Image align="center" width="700px" src="https://files.readme.io/86e933aa8dcabb7a9e40b31b11f4ebe59fa95cf5162bd9293f88d88e83e03f87-reporter_lifecycle_overview_1.svg" />

Here's what happens under the hood:

1. **Your tests start.** The reporter creates a test run in Qase (or you can instruct it to report to an existing run).
2. **Tests execute.** The reporter collects each result — pass, fail, skip — along with any metadata you've added: steps, attachments, fields.
3. **Results are sent in batches.** While tests are still running, the reporter uploads results to Qase in the background. You don't wait until the end.
4. **Tests finish.** The reporter sends any remaining results and completes the test run.

The reporter never interferes with your tests. If it can't reach Qase — bad token, network issue, API outage — your tests still run and finish normally. Reporting is non-blocking.

Reporters are available for all major test frameworks across JavaScript, Python, Java, C#, Go, Kotlin, and PHP. The setup is the same idea everywhere: install a package, point it at your Qase project, run your tests.
