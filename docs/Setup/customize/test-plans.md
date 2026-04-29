---
title: test plans
deprecated: false
hidden: false
metadata:
  robots: index
---
A test plan is a curated list of test cases that defines what needs to be tested for a specific goal — a release, a sprint, a feature sign-off. You build it in Qase by selecting the cases that matter, and optionally assigning team members to each one.

The reason test plans matter for automated reporting: they're the bridge between your manual testers and your automated suite. A single plan can contain both.

<br />

### The problem plans solve

Most teams have a mix of manual and automated tests. The manual ones live in Qase — exploratory tests, UX walkthroughs, edge cases that aren't worth automating. The automated ones live in your repo — regression suites, API checks, integration tests. Without a plan, these two worlds report separately. Your manual testers submit results through the Qase UI. Your CI pipeline submits results through the reporter. Two different runs, two different dashboards, no unified picture.

A test plan brings them together. You create one plan that includes both manual and automated cases. When you start a run from that plan, everyone — manual testers and your CI pipeline — reports into the same run. One dashboard, one completion percentage, one answer to "are we ready to ship?"

<br />

### How it works

<HTMLBlock>{`
<style>
  .tp{font-family:var(--font-sans,system-ui,sans-serif);padding:24px}
  .tp-row{display:flex;align-items:center;gap:0}
  .tp-box{border-radius:10px;padding:14px 18px;border:1px solid}
  .tp-label{font-size:11px;font-weight:500;letter-spacing:.05em;text-transform:uppercase;margin-bottom:4px}
  .tp-title{font-size:14px;font-weight:500;color:var(--color-text-primary);margin:0 0 2px}
  .tp-sub{font-size:12px;color:var(--color-text-secondary);margin:0}
  .tp-arrow{display:flex;flex-direction:column;align-items:center;gap:4px;padding:0 16px;flex-shrink:0}
  .tp-arrow-line{flex:1;width:1px;background:var(--color-border-secondary);min-height:32px;position:relative}
  .tp-arrow-line::after{content:'';position:absolute;bottom:-1px;left:-4px;border:5px solid transparent;border-top:7px solid var(--color-border-secondary)}
  .tp-arrow-h{height:1px;background:var(--color-border-secondary);width:48px;position:relative;flex-shrink:0}
  .tp-arrow-h::after{content:'';position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid var(--color-border-secondary)}
  .tp-arrow-label{font-size:11px;color:var(--color-text-tertiary);white-space:nowrap}
  .run-box{border:1px dashed var(--color-border-secondary);border-radius:12px;padding:16px;flex:1}
  .run-title{font-size:11px;font-weight:500;letter-spacing:.05em;text-transform:uppercase;color:var(--color-text-tertiary);margin-bottom:12px}
  .case-row{display:flex;align-items:center;justify-content:space-between;padding:8px 12px;border-radius:7px;margin-bottom:6px;border:1px solid}
  .case-name{font-size:12px;font-weight:500}
  .case-badge{font-size:11px;font-weight:500;padding:2px 8px;border-radius:20px}
  .case-auto-pass{background:#E1F5EE;border-color:#5DCAA5;}.case-auto-pass .case-name{color:#085041}.case-auto-pass .case-badge{background:#9FE1CB;color:#04342C}
  .case-auto-fail{background:#FCEBEB;border-color:#F09595;}.case-auto-fail .case-name{color:#791F1F}.case-auto-fail .case-badge{background:#F7C1C1;color:#501313}
  .case-manual{background:var(--color-background-secondary);border-color:var(--color-border-tertiary);}.case-manual .case-name{color:var(--color-text-secondary)}.case-manual .case-badge{background:var(--color-background-primary);color:var(--color-text-tertiary);border:1px solid var(--color-border-secondary)}
  .divider-label{display:flex;align-items:center;gap:8px;margin:10px 0}
  .divider-label hr{flex:1;border:none;border-top:1px dashed var(--color-border-tertiary);margin:0}
  .divider-label span{font-size:11px;color:var(--color-text-tertiary);white-space:nowrap}
  .qa-box{border-radius:10px;padding:14px 18px;border:1px solid}
</style>

<div class="tp">

  <!-- Top row: Plan → Reporter → Run -->
  <div style="display:flex;align-items:stretch;gap:0">

    <!-- Plan -->
    <div class="tp-box" style="background:#EEEDFE;border-color:#AFA9EC;min-width:130px;display:flex;flex-direction:column;justify-content:center">
      <div class="tp-label" style="color:#7F77DD">Test plan</div>
      <div class="tp-title" style="color:#3C3489">Curated cases</div>
      <div class="tp-sub" style="color:#534AB7">defined in Qase</div>
    </div>

    <!-- Arrow: Plan → Reporter -->
    <div style="display:flex;flex-direction:column;justify-content:center;align-items:center;padding:0 12px;gap:4px;flex-shrink:0">
      <span style="font-size:11px;color:var(--color-text-tertiary)">fetches</span>
      <div style="display:flex;align-items:center">
        <div style="width:40px;height:1px;background:var(--color-border-secondary);position:relative">
          <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid var(--color-border-secondary)"></span>
        </div>
      </div>
    </div>

    <!-- Reporter -->
    <div class="tp-box" style="background:#E1F5EE;border-color:#5DCAA5;min-width:150px;display:flex;flex-direction:column;justify-content:center">
      <div class="tp-label" style="color:#1D9E75">Reporter (CI)</div>
      <div class="tp-title" style="color:#085041">Creates the run</div>
      <div class="tp-sub" style="color:#0F6E56">plan.id set in config</div>
    </div>

    <!-- Arrow: Reporter → Run -->
    <div style="display:flex;flex-direction:column;justify-content:center;align-items:center;padding:0 12px;gap:4px;flex-shrink:0">
      <span style="font-size:11px;color:var(--color-text-tertiary)">creates + pushes</span>
      <div style="display:flex;align-items:center">
        <div style="width:40px;height:1px;background:var(--color-border-secondary);position:relative">
          <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid var(--color-border-secondary)"></span>
        </div>
      </div>
    </div>

    <!-- Run box -->
    <div class="run-box">
      <div class="run-title">Test run — linked to plan</div>

      <!-- Automated cases -->
      <div class="case-row case-auto-pass">
        <span class="case-name">Automated case</span>
        <span class="case-badge">passed</span>
      </div>
      <div class="case-row case-auto-pass">
        <span class="case-name">Automated case</span>
        <span class="case-badge">passed</span>
      </div>
      <div class="case-row case-auto-fail">
        <span class="case-name">Automated case</span>
        <span class="case-badge">failed</span>
      </div>

      <div class="divider-label">
        <hr/><span>manual cases — awaiting QA</span><hr/>
      </div>

      <!-- Manual cases -->
      <div class="case-row case-manual">
        <span class="case-name">Manual case</span>
        <span class="case-badge">untested</span>
      </div>
      <div class="case-row case-manual">
        <span class="case-name">Manual case</span>
        <span class="case-badge">untested</span>
      </div>
      <div class="case-row case-manual">
        <span class="case-name">Manual case</span>
        <span class="case-badge">untested</span>
      </div>
    </div>

  </div>

  <!-- Down arrow to Manual QA -->
  <div style="display:flex;justify-content:flex-end;padding-right:0">
    <div style="display:flex;flex-direction:column;align-items:center;gap:3px;padding:6px 0;width:calc(100% - 380px)">
      <div style="width:1px;height:24px;background:var(--color-border-secondary);position:relative">
        <span style="position:absolute;bottom:-1px;left:-4px;border:5px solid transparent;border-top:7px solid var(--color-border-secondary)"></span>
      </div>
      <span style="font-size:11px;color:var(--color-text-tertiary)">manual QA submits results</span>
    </div>
  </div>

  <!-- Manual QA box -->
  <div style="display:flex;justify-content:flex-end">
    <div class="qa-box" style="background:#FAECE7;border-color:#F0997B;width:calc(100% - 380px)">
      <div class="tp-label" style="color:#D85A30">Manual QA</div>
      <div class="tp-title" style="color:#712B13">Completes the run</div>
      <div class="tp-sub" style="color:#993C1D">works through untested cases in Qase UI — run closes when all cases have a result</div>
    </div>
  </div>

</div>
`}</HTMLBlock>

1. **Create a plan in Qase** — pick the cases, assign team members to manual cases if you want.
2. **Start a run from the plan** — Qase creates a run pre-populated with the plan's cases.
3. **Your reporter sends automated results to the same run** — using `testops.run.id` set to the run that was created from the plan.
4. **Manual testers work through their cases** in the Qase UI — clicking pass, fail, blocked.

The run dashboard shows everything in one place: which cases are done, which are still untested, who's assigned to what, and the overall pass/fail breakdown. It doesn't matter whether a result came from a human clicking a button or a CI job sending an API call.

<br />

### Selective execution

Some reporters can go further. If you set `testops.plan.id` in your config, the reporter fetches the plan's case list from Qase and **filters your test suite to only run the tests that are in the plan**. Tests not in the plan are skipped entirely — they don't execute.

This is supported in **Java** (TestNG, JUnit5), **Python** (pytest), and **Robot Framework**. In these frameworks, the reporter intercepts the test collection phase and removes tests whose Qase IDs aren't in the plan.

```json
{
  "testops": {
    "plan": {
      "id": 42
    }
  }
}
```

Or as an environment variable:

```
QASE_TESTOPS_PLAN_ID=42
```

> **Important:** Selective execution only works for tests that have a Qase ID linked. Unlinked tests are excluded from the filtered run because the reporter has no way to match them to the plan.
>
> **Not all frameworks support selective execution.** JavaScript reporters, C#, Go, PHP, and Kotlin report results to a plan-linked run but don't filter the test collection. All your tests still execute — the plan association is metadata on the run, not a filter on execution.

<br />

### Two ways to use plans with reporters

| Approach                        | Config                                           | What happens                                                                                                     |
| ------------------------------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| **Report to a plan-linked run** | `testops.run.id` = the run created from the plan | Reporter sends results to an existing run. All tests execute. Manual + automated results merge in one dashboard. |
| **Selective execution**         | `testops.plan.id` = the plan ID                  | Reporter fetches the plan, filters tests, creates a new run linked to the plan, and only runs matching tests.    |

The first approach is more common — especially when you have manual testers working alongside automation. You create the run from the plan in Qase, hand the run ID to your CI job, and both sides report into the same place.

The second approach is useful when you want CI to run _exactly_ the tests in a plan — nothing more, nothing less. This is common for targeted regression: "run only the cases we've flagged for this release."

<br />

### When to use plans

* **Release sign-off** — "These 200 cases must pass before we ship. 150 are automated, 50 are manual. One plan, one run, one dashboard."
* **Sprint testing** — "This sprint touches auth and payments. Here's a plan with the relevant cases. QA owns the manual ones, CI handles the rest."
* **Compliance or audit** — "We need to prove these specific scenarios were tested. The plan is the checklist, the run is the evidence."

If you're a small team running only automated tests, you may not need plans at all — the reporter creates runs automatically and that's enough. Plans become valuable when you need to coordinate manual and automated testing, or when you need to define _exactly_ which tests should run for a specific purpose.
