---
title: Linking Tests
deprecated: false
hidden: false
metadata:
  robots: index
---
A test case ID is the stable thread between your code and Qase — it survives renames, moves, and refactors.

### What reporting gives you

When you set up a reporter, every test run becomes visible to your whole team — not just a green/red badge buried in a CI log, but structured results that anyone can read, filter, and act on. That's the baseline: visibility.

But visibility alone doesn't answer the harder questions. _Is this feature tested? When did this test start failing? Is it safe to release?_ Those answers require history — and history requires Qase to know that the test you ran today is the same test you ran last week.

That's what linking does.

<br />

### How Qase matches results to test cases

When a result arrives, Qase tries to match it to an existing test case in two steps:

1. **By ID** — if your test has a Qase ID (e.g., `@QaseId(42)`), the match is exact and permanent. Rename the test, move it to a different file, refactor the suite — the ID still points to the same case.
2. **By name** — if there's no ID, Qase falls back to matching by test title and suite path. This works until you rename something. When you do, Qase sees a "new" test and the old one's history stops.

The ID-based match is the one you can trust long-term. The name-based match is a convenience that gets you started without touching your code.

<br />

### Without linking: auto-creation

If you just run your tests without adding any IDs, Qase doesn't discard the results — it creates test cases for you automatically. This is on by default for new projects. The reporter sends the test name and suite path, and Qase creates a matching case.

A few things to know about auto-creation:

* By default, cases are only created from **passed** results. If a test fails on its very first run, no case is created until it passes. You can change this to "all statuses" in your project settings.
* Subsequent runs match by name. As long as the test name and suite path stay the same, results accumulate on the same case.
* Auto-created cases are automatically marked as "automated" in Qase.

This is a good starting point. You get visibility and basic history without changing a single line of test code.

<br />

### With linking: stable history

When you add a Qase ID to a test, you're saying "this test _is_ case 42, no matter what." The benefits compound over time:

* **Refactoring is safe.** Rename the test, move it to a new file, restructure your suites — the history stays intact because the ID doesn't change.
* **History becomes meaningful.** Instead of fragmented records across renamed tests, you get one continuous timeline per case. You can see when a test started flaking, how long it's been failing, and whether a fix actually held.
* **Traceability goes both ways.** From the code, you know which Qase case a test covers. From Qase, you know which automated test covers a case. When someone asks "is this feature tested?", the answer is a link, not a guess.

The syntax for adding an ID is different in every framework — your framework guide has the specifics. The concept is the same everywhere: one ID, one stable connection.

> **Note:** Qase can also update existing test cases from your automated results (e.g., syncing steps or descriptions back into the case). This is off by default — look for "Update test cases from automated results" in your project settings if you want it.

<br />

## How to link?

Each framework has its own syntax for IDs — and for everything else you'll learn in the next sections (titles, suites, fields, steps, attachments). Rather than documenting every variation here, we maintain a demo repository for each supported reporter.

Find your reporter's repo from the **Quick Start** page.
