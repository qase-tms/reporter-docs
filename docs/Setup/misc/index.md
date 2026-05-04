---
title: Additional options
deprecated: false
hidden: false
metadata:
  robots: index
---
As your reporting matures, you'll likely find one or two that solve a specific problem you're running into.

In this section, we cover:

**Status Mapping** — Your test framework might produce statuses that don't match what you want to see in Qase. A test that throws a non-assertion error might show as `invalid` when you'd rather it showed as `failed`. Status mapping lets you remap these without changing your test code.

**Defect Integration** — When a linked test fails, the reporter can automatically flag it as a known defect in Qase. Useful for teams that track regressions through Qase's defect workflow rather than (or alongside) an external issue tracker.

**Root Suite** — If you run multiple test suites or frameworks in the same Qase project, their suite hierarchies can collide. Root suite adds a prefix to every suite name the reporter sends, keeping things separated. A small option that prevents a messy test repository.

**Capture Logs** — The reporter can capture stdout and stderr from your tests and attach them to results. When a test fails in CI and you need to see what it printed, this saves you from digging through CI logs.

**Public Report Links** — After a run completes, the reporter can print a public link to the results. Handy for sharing with stakeholders who don't have Qase accounts.

**External Links** — Attach a URL to the test run itself — typically a link back to the CI build that produced it. When someone looks at a run in Qase and asks "which build was this?", the link is right there.

Each of these is a short read. Pick the ones relevant to your situation.
