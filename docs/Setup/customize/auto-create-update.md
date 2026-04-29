---
title: auto create & update
deprecated: false
hidden: false
metadata:
  robots: index
---
Up to this point, you've been learning how to enrich your automated tests with metadata — titles, fields, steps, suites, parameters. You might be wondering: _where does all that metadata actually go?_

The answer depends on a setting in Qase that most teams never think about — until something unexpected happens.

## The problem this solves

Most teams start their automation journey in one of two places:

**Scenario A: "We have test cases in Qase, and now we're automating them."** You've been managing test cases manually — writing descriptions, assigning priorities, organizing into suites. Now you're writing automated tests and linking them with `QaseId`. The test cases already exist. You just want results to flow in.

**Scenario B: "We write tests in code first, and Qase should reflect what we have."** Your team is automation-first. Tests live in the repo. You don't want to create test cases in Qase by hand and then link them — that's double the work. You want Qase to learn about your tests from the results themselves.

Auto-create and auto-update exist for Scenario B — and for the many teams that live somewhere in between.

## Auto-create: letting Qase learn from your tests

When the reporter sends a result that doesn't match any existing test case, Qase has a choice: store the result as an orphan, or create a test case for it automatically.

With **auto-create enabled** (the default), Qase creates a test case from the result. Everything you annotated in code — title, suite, description, fields, steps, parameters — becomes a real test case in your repository. The next time that test runs, Qase matches it to the case it created, and you start building history.

This is where the metadata work from earlier pages pays off. If you annotated your test with `@qase.fields(("priority", "high"), ("layer", "API"))`, the auto-created test case in Qase will have priority "High" and layer "API." If you defined steps, the test case will have steps. Your code becomes the source of truth, and Qase reflects it.

### How Qase matches results to test cases

When a result arrives, Qase tries to find an existing test case using a two-step lookup:

<HTMLBlock>{`
<style>
  .dm{font-family:var(--font-sans,system-ui,sans-serif);padding:24px 20px}
  .dm-row{display:flex;align-items:flex-start;justify-content:center;gap:0}
  .dm-col{display:flex;flex-direction:column;align-items:center;gap:0}
  .dm-node{border-radius:10px;padding:10px 16px;border:1px solid;text-align:center;white-space:nowrap}
  .dm-node-title{font-size:12px;font-weight:500}
  .dm-node-sub{font-size:11px;margin-top:2px}
  .dm-diamond{width:110px;height:110px;position:relative;flex-shrink:0}
  .dm-diamond-inner{position:absolute;inset:0;display:flex;align-items:center;justify-content:center}
  .dm-diamond-inner span{font-size:12px;font-weight:500;text-align:center;line-height:1.3;padding:0 8px}
  .dm-diamond svg{position:absolute;inset:0;width:100%;height:100%}
  .v-line{width:1px;min-height:20px;background:var(--color-border-secondary);margin:0 auto}
  .v-line-dashed{width:1px;min-height:20px;border-left:1.5px dashed var(--color-border-secondary);margin:0 auto}
  .h-connector{height:1px;background:var(--color-border-secondary);flex:1;min-width:16px}
  .arrow-down::after{content:'';display:block;width:0;height:0;border-left:5px solid transparent;border-right:5px solid transparent;border-top:7px solid var(--color-border-secondary);margin:0 auto}
  .arrow-right::after{content:'';display:block;width:0;height:0;border-top:5px solid transparent;border-bottom:5px solid transparent;border-left:7px solid var(--color-border-secondary)}
  .arrow-left::after{content:'';display:block;width:0;height:0;border-top:5px solid transparent;border-bottom:5px solid transparent;border-right:7px solid var(--color-border-secondary)}
  .yes-no{font-size:10px;font-weight:500;letter-spacing:.04em;text-transform:uppercase;color:var(--color-text-tertiary)}

  .node-start{background:var(--color-background-secondary);border-color:var(--color-border-secondary)}
  .node-start .dm-node-title{color:var(--color-text-primary)}

  .node-q{background:#EEEDFE;border-color:#AFA9EC}
  .node-q .dm-node-title{color:#3C3489}

  .node-match{background:#E1F5EE;border-color:#5DCAA5}
  .node-match .dm-node-title{color:#085041}
  .node-match .dm-node-sub{color:#0F6E56}

  .node-create{background:#E1F5EE;border-color:#5DCAA5}
  .node-create .dm-node-title{color:#085041}
  .node-create .dm-node-sub{color:#0F6E56}

  .node-orphan{background:#F1EFE8;border-color:#B4B2A9}
  .node-orphan .dm-node-title{color:#444441}
  .node-orphan .dm-node-sub{color:#5F5E5A}

  @media(prefers-color-scheme:dark){
    .node-q{background:#3C3489;border-color:#7F77DD}
    .node-q .dm-node-title{color:#CECBF6}
    .node-match{background:#085041;border-color:#1D9E75}
    .node-match .dm-node-title{color:#9FE1CB}
    .node-match .dm-node-sub{color:#5DCAA5}
    .node-create{background:#085041;border-color:#1D9E75}
    .node-create .dm-node-title{color:#9FE1CB}
    .node-create .dm-node-sub{color:#5DCAA5}
    .node-orphan{background:#444441;border-color:#888780}
    .node-orphan .dm-node-title{color:#D3D1C7}
    .node-orphan .dm-node-sub{color:#B4B2A9}
  }
</style>

<div class="dm">

  <!-- START -->
  <div class="dm-col">
    <div class="dm-node node-start" style="min-width:160px">
      <div class="dm-node-title">Result arrives</div>
    </div>
    <div class="v-line" style="min-height:16px"></div>
    <div class="arrow-down"></div>

    <!-- Q1: Has QaseId? -->
    <div class="dm-diamond">
      <svg viewBox="0 0 110 110"><polygon points="55,6 104,55 55,104 6,55" fill="#EEEDFE" stroke="#AFA9EC" stroke-width="1"/></svg>
      <div class="dm-diamond-inner"><span style="color:#3C3489;font-size:11px;font-weight:500">Has<br>QaseId?</span></div>
    </div>

    <!-- Branch row: Yes left, No right -->
    <div style="display:flex;width:100%;align-items:center;justify-content:center;margin-top:0">

      <!-- YES branch (left) -->
      <div style="display:flex;flex-direction:column;align-items:center;width:200px">
        <div style="display:flex;align-items:center;width:100%;justify-content:flex-end">
          <span class="yes-no" style="margin-right:4px">yes</span>
          <div class="h-connector" style="max-width:40px"></div>
          <div class="arrow-down" style="transform:rotate(90deg);margin-right:-7px"></div>
        </div>
        <div style="width:1px;min-height:0"></div>

        <!-- Look up by ID -->
        <div style="display:flex;align-items:center;margin-right:0">
          <div class="dm-node node-q" style="min-width:130px;margin-top:8px">
            <div class="dm-node-title">Look up by ID</div>
          </div>
        </div>
        <div class="v-line" style="min-height:14px"></div>
        <div class="arrow-down"></div>

        <!-- Found? -->
        <div class="dm-diamond">
          <svg viewBox="0 0 110 110"><polygon points="55,6 104,55 55,104 6,55" fill="#EEEDFE" stroke="#AFA9EC" stroke-width="1"/></svg>
          <div class="dm-diamond-inner"><span style="color:#3C3489;font-size:11px;font-weight:500">Found?</span></div>
        </div>

        <!-- Found yes → match -->
        <div style="display:flex;align-items:center;width:100%;justify-content:center;gap:0">
          <div style="display:flex;flex-direction:column;align-items:center">
            <div style="display:flex;align-items:center">
              <span class="yes-no" style="margin-right:4px">yes</span>
              <div class="arrow-down"></div>
            </div>
            <div class="dm-node node-match" style="min-width:150px;margin-top:4px">
              <div class="dm-node-title">Match found</div>
              <div class="dm-node-sub">result linked to case</div>
            </div>
          </div>
        </div>

        <!-- Found no → treat as unlinked label -->
        <!-- handled via the no path below -->
      </div>

      <!-- Vertical centre spacer -->
      <div style="width:40px"></div>

      <!-- NO branch (right) -->
      <div style="display:flex;flex-direction:column;align-items:center;width:200px">
        <div style="display:flex;align-items:center;width:100%;justify-content:flex-start">
          <div class="arrow-down" style="transform:rotate(-90deg);margin-left:-7px"></div>
          <div class="h-connector" style="max-width:40px"></div>
          <span class="yes-no" style="margin-left:4px">no</span>
        </div>

        <!-- Look up by title + suite -->
        <div class="dm-node node-q" style="min-width:150px;margin-top:8px">
          <div class="dm-node-title">Look up by title + suite</div>
        </div>
        <div class="v-line" style="min-height:14px"></div>
        <div class="arrow-down"></div>

        <!-- Found? -->
        <div class="dm-diamond">
          <svg viewBox="0 0 110 110"><polygon points="55,6 104,55 55,104 6,55" fill="#EEEDFE" stroke="#AFA9EC" stroke-width="1"/></svg>
          <div class="dm-diamond-inner"><span style="color:#3C3489;font-size:11px;font-weight:500">Found?</span></div>
        </div>

        <div style="display:flex;flex-direction:column;align-items:center">
          <div style="display:flex;align-items:center">
            <span class="yes-no" style="margin-right:4px">yes</span>
            <div class="arrow-down"></div>
          </div>
          <div class="dm-node node-match" style="min-width:150px;margin-top:4px">
            <div class="dm-node-title">Match found</div>
            <div class="dm-node-sub">result linked to case</div>
          </div>
        </div>
      </div>

    </div>
  </div>

  <!-- Auto-create decision — centred below both "no" branches -->
  <div style="display:flex;justify-content:center;margin-top:20px;gap:60px">

    <!-- Left no path (ID not found) -->
    <div style="display:flex;flex-direction:column;align-items:center">
      <span class="yes-no">no → treat as unlinked</span>
      <div class="v-line" style="min-height:16px"></div>
      <div class="arrow-down"></div>

      <!-- Auto-create enabled? -->
      <div class="dm-diamond">
        <svg viewBox="0 0 110 110"><polygon points="55,6 104,55 55,104 6,55" fill="#EEEDFE" stroke="#AFA9EC" stroke-width="1"/></svg>
        <div class="dm-diamond-inner"><span style="color:#3C3489;font-size:11px;font-weight:500">Auto-create<br>enabled?</span></div>
      </div>

      <div style="display:flex;gap:32px;align-items:flex-start;margin-top:4px">
        <div style="display:flex;flex-direction:column;align-items:center">
          <div style="display:flex;align-items:center"><span class="yes-no" style="margin-right:4px">yes</span><div class="arrow-down"></div></div>
          <div class="dm-node node-create" style="min-width:130px;margin-top:4px">
            <div class="dm-node-title">New case created</div>
            <div class="dm-node-sub">metadata from code</div>
          </div>
        </div>
        <div style="display:flex;flex-direction:column;align-items:center">
          <div style="display:flex;align-items:center"><div class="arrow-down"></div><span class="yes-no" style="margin-left:4px">no</span></div>
          <div class="dm-node node-orphan" style="min-width:130px;margin-top:4px">
            <div class="dm-node-title">Caseless result</div>
            <div class="dm-node-sub">stored, not linked</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Right no path (title+suite not found) -->
    <div style="display:flex;flex-direction:column;align-items:center">
      <span class="yes-no">no</span>
      <div class="v-line" style="min-height:16px"></div>
      <div class="arrow-down"></div>

      <div class="dm-diamond">
        <svg viewBox="0 0 110 110"><polygon points="55,6 104,55 55,104 6,55" fill="#EEEDFE" stroke="#AFA9EC" stroke-width="1"/></svg>
        <div class="dm-diamond-inner"><span style="color:#3C3489;font-size:11px;font-weight:500">Auto-create<br>enabled?</span></div>
      </div>

      <div style="display:flex;gap:32px;align-items:flex-start;margin-top:4px">
        <div style="display:flex;flex-direction:column;align-items:center">
          <div style="display:flex;align-items:center"><span class="yes-no" style="margin-right:4px">yes</span><div class="arrow-down"></div></div>
          <div class="dm-node node-create" style="min-width:130px;margin-top:4px">
            <div class="dm-node-title">New case created</div>
            <div class="dm-node-sub">metadata from code</div>
          </div>
        </div>
        <div style="display:flex;flex-direction:column;align-items:center">
          <div style="display:flex;align-items:center"><div class="arrow-down"></div><span class="yes-no" style="margin-left:4px">no</span></div>
          <div class="dm-node node-orphan" style="min-width:130px;margin-top:4px">
            <div class="dm-node-title">Caseless result</div>
            <div class="dm-node-sub">stored, not linked</div>
          </div>
        </div>
      </div>
    </div>

  </div>

</div>
`}</HTMLBlock>

<br />

The important detail: **unlinked tests are matched by title and suite path together.** A test called "Login works" in suite "Auth > Smoke" is different from "Login works" in suite "Auth > Regression." This means your suite structure (from `describe` blocks, packages, or explicit `@qase.suite()` annotations) matters for matching — not just the test name.

This also means: **if you rename a test or move it to a different suite, Qase sees it as a new test.** The old test case stays, a new one is created, and you lose the history chain. This is the same identity concept from the [Linking Tests](#) page — linking with `QaseId` gives you a stable identity that survives refactoring. Auto-create gives you convenience, but the identity is fragile.

### What gets created

You control which fields flow from results into new test cases. In **Project Settings > Test Runs > Automated testing**, you'll find checkboxes for each field:

* **Always included:** Title (mandatory — every test case needs a name)
* **On by default:** Description, preconditions, postconditions, suite, steps, parameters, severity, priority, layer, type, behavior, muted/flaky status, tags
* **Automatic:** Cases created from automated results are marked as "Automated" by default (controlled by a separate toggle)

You can uncheck fields you don't want. For example, if your code annotations don't include severity and you'd rather set it manually in Qase, uncheck it — auto-create won't populate it, and you can fill it in later.

### Status filter: not every result creates a case

By default, only **passed** results create test cases. A test that fails on its first run doesn't get a case — the assumption is that you're still iterating on it.

You can change this to "All statuses" if you want every test to create a case regardless of outcome. Teams that use Qase for tracking test development (not just test execution) often prefer this.

## Auto-update: keeping Qase in sync with your code

Auto-create handles the first time. Auto-update handles every time after that.

When you change a test's description in code, add a step, or update its priority annotation — should the test case in Qase reflect that change? If your code is the source of truth, the answer is yes. That's what auto-update does.

**Auto-update is off by default.** This is intentional. Many teams have test cases that were carefully written by QA engineers — with detailed descriptions, edge case notes, and context that doesn't exist in code. Turning on auto-update without thinking could overwrite that work.

### When to turn it on

Auto-update makes sense when:

* Your team has adopted the **test-as-code** approach — metadata lives in annotations, and the repo is the source of truth
* You want Qase to stay in sync without anyone manually updating test cases after code changes
* Your automated tests are the primary (or only) documentation of what's being tested

Auto-update is risky when:

* You have manually curated test cases with rich descriptions that don't exist in code
* Multiple people edit the same test cases — some in Qase, some in code
* You're not yet annotating tests with enough metadata to replace what's in Qase

### Granular control

Auto-update gives you fine-grained control over what gets overwritten:

* **Which fields:** Choose exactly which fields can be updated from results. You might allow steps and parameters to sync from code, but keep description and priority under manual control.
* **Which statuses:** Update from passed results only, or from all statuses.
* **Which test types:** Update all test cases, only manual ones (to "automate" them), or only already-automated ones (to keep them current).

This granularity exists because the real world is messy. You might want auto-update for steps (they change with the code) but not for description (your QA lead writes better descriptions than your annotations). You can have both.

### The title conflict

There's one subtle interaction worth knowing about. Qase has a setting called **"Use test case titles from repository"** — when enabled, run results display the title stored in Qase, ignoring whatever the reporter sends. If you enable this _and_ enable auto-update for the title field, you'll see a warning: the title will be updated from the reporter anyway, because auto-update takes precedence.

This isn't a bug — it's a signal that you need to decide: is the code or Qase the authority on titles? Pick one.

## The bigger picture

Auto-create and auto-update are the bridge between two worlds: the code your developers write and the test management your QA team relies on. They're what make the metadata annotations from earlier pages _useful beyond reporting_ — your code doesn't just produce results, it maintains your test repository.

<HTMLBlock>{`
<style>
  .bp{font-family:var(--font-sans,system-ui,sans-serif);padding:28px 24px}
  .bp-row{display:flex;align-items:stretch;gap:0}
  .bp-node{border-radius:10px;border:1px solid;padding:14px 16px;display:flex;flex-direction:column;justify-content:center;flex:1}
  .bp-tag{font-size:10px;font-weight:500;letter-spacing:.05em;text-transform:uppercase;margin-bottom:5px}
  .bp-title{font-size:14px;font-weight:500;margin:0 0 4px}
  .bp-items{margin:0;padding:0;list-style:none;display:flex;flex-direction:column;gap:3px}
  .bp-items li{font-size:11px;display:flex;align-items:center;gap:5px}
  .bp-items li::before{content:'';width:4px;height:4px;border-radius:50%;flex-shrink:0}
  .bp-connector{display:flex;flex-direction:column;align-items:center;justify-content:center;padding:0 10px;flex-shrink:0;gap:4px}
  .bp-arrow-h{height:1px;width:32px;position:relative}
  .bp-arrow-h::after{content:'';position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid}
  .bp-connector-label{font-size:10px;text-transform:uppercase;letter-spacing:.04em;white-space:nowrap}

  .n-code{background:#EEEDFE;border-color:#AFA9EC}
  .n-code .bp-tag{color:#7F77DD}
  .n-code .bp-title{color:#3C3489}
  .n-code .bp-items li{color:#534AB7}
  .n-code .bp-items li::before{background:#7F77DD}

  .n-reporter{background:#E1F5EE;border-color:#5DCAA5}
  .n-reporter .bp-tag{color:#1D9E75}
  .n-reporter .bp-title{color:#085041}
  .n-reporter .bp-items li{color:#0F6E56}
  .n-reporter .bp-items li::before{background:#1D9E75}

  .n-qase{background:#FAEEDA;border-color:#FAC775}
  .n-qase .bp-tag{color:#BA7517}
  .n-qase .bp-title{color:#633806}
  .n-qase .bp-items li{color:#854F0B}
  .n-qase .bp-items li::before{background:#BA7517}

  .n-repo{background:#F1EFE8;border-color:#B4B2A9}
  .n-repo .bp-tag{color:#5F5E5A}
  .n-repo .bp-title{color:#2C2C2A}
  .n-repo .bp-items li{color:#5F5E5A}
  .n-repo .bp-items li::before{background:#888780}

  .n-team{background:#FAECE7;border-color:#F0997B}
  .n-team .bp-tag{color:#993C1D}
  .n-team .bp-title{color:#4A1B0C}
  .n-team .bp-items li{color:#712B13}
  .n-team .bp-items li::before{background:#D85A30}

  @media(prefers-color-scheme:dark){
    .n-code{background:#3C3489;border-color:#7F77DD}
    .n-code .bp-tag{color:#AFA9EC}
    .n-code .bp-title{color:#CECBF6}
    .n-code .bp-items li{color:#AFA9EC}
    .n-code .bp-items li::before{background:#AFA9EC}

    .n-reporter{background:#085041;border-color:#1D9E75}
    .n-reporter .bp-tag{color:#5DCAA5}
    .n-reporter .bp-title{color:#9FE1CB}
    .n-reporter .bp-items li{color:#5DCAA5}
    .n-reporter .bp-items li::before{background:#5DCAA5}

    .n-qase{background:#633806;border-color:#EF9F27}
    .n-qase .bp-tag{color:#FAC775}
    .n-qase .bp-title{color:#FAEEDA}
    .n-qase .bp-items li{color:#FAC775}
    .n-qase .bp-items li::before{background:#FAC775}

    .n-repo{background:#444441;border-color:#888780}
    .n-repo .bp-tag{color:#B4B2A9}
    .n-repo .bp-title{color:#D3D1C7}
    .n-repo .bp-items li{color:#B4B2A9}
    .n-repo .bp-items li::before{background:#B4B2A9}

    .n-team{background:#712B13;border-color:#D85A30}
    .n-team .bp-tag{color:#F5C4B3}
    .n-team .bp-title{color:#FAECE7}
    .n-team .bp-items li{color:#F0997B}
    .n-team .bp-items li::before{background:#F0997B}
  }
</style>

<div class="bp">
  <div class="bp-row">

    <!-- Your code -->
    <div class="bp-node n-code">
      <div class="bp-tag">Your code</div>
      <div class="bp-title">Annotations &amp; steps</div>
      <ul class="bp-items">
        <li>titles &amp; suites</li>
        <li>fields &amp; priority</li>
        <li>steps &amp; params</li>
      </ul>
    </div>

    <div class="bp-connector">
      <div class="bp-connector-label" style="color:var(--color-text-tertiary)">sends</div>
      <div class="bp-arrow-h" style="background:#c8c6bc"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
    </div>

    <!-- Reporter -->
    <div class="bp-node n-reporter">
      <div class="bp-tag">Reporter</div>
      <div class="bp-title">Results + metadata</div>
      <ul class="bp-items">
        <li>batches results</li>
        <li>creates run</li>
        <li>uploads attachments</li>
      </ul>
    </div>

    <div class="bp-connector">
      <div class="bp-connector-label" style="color:var(--color-text-tertiary)">feeds</div>
      <div class="bp-arrow-h" style="background:#c8c6bc"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
    </div>

    <!-- Qase -->
    <div class="bp-node n-qase">
      <div class="bp-tag">Qase</div>
      <div class="bp-title">Auto-create &amp; update</div>
      <ul class="bp-items">
        <li>matches or creates cases</li>
        <li>syncs metadata fields</li>
        <li>builds run history</li>
      </ul>
    </div>

    <div class="bp-connector">
      <div class="bp-connector-label" style="color:var(--color-text-tertiary)">builds</div>
      <div class="bp-arrow-h" style="background:#c8c6bc"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
    </div>

    <!-- Test repository -->
    <div class="bp-node n-repo">
      <div class="bp-tag">Test repository</div>
      <div class="bp-title">Cases &amp; history</div>
      <ul class="bp-items">
        <li>organised suites</li>
        <li>result trends</li>
        <li>flaky test tracking</li>
      </ul>
    </div>

    <div class="bp-connector">
      <div class="bp-connector-label" style="color:var(--color-text-tertiary)">gives</div>
      <div class="bp-arrow-h" style="background:#c8c6bc"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
    </div>

    <!-- Your team -->
    <div class="bp-node n-team">
      <div class="bp-tag">Your team</div>
      <div class="bp-title">Visibility &amp; planning</div>
      <ul class="bp-items">
        <li>coverage overview</li>
        <li>release confidence</li>
        <li>QA + dev alignment</li>
      </ul>
    </div>

  </div>
</div>
`}</HTMLBlock>

<br />

The practical adoption path for most teams:

1. **Start with auto-create on, auto-update off.** Let Qase learn about your tests. Review what gets created.
2. **Add metadata gradually.** As you annotate tests with titles, fields, and steps, the auto-created cases get richer.
3. **Turn on auto-update selectively.** Start with steps and parameters — they change with the code and are tedious to update manually. Leave description and priority under manual control until you're confident your annotations are good enough.
4. **Graduate to full sync** when your team agrees that code is the source of truth.

<br />

> **Where to find these settings:** Project Settings > Test Runs > Automated testing. Every setting on this page is per-project — you can have different policies for different projects.
