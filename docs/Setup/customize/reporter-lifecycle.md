---
title: reporter lifecycle
deprecated: false
hidden: false
metadata:
  robots: index
---
Every Qase reporter follows the same three-phase lifecycle: **start**, **report**, **complete**. Understanding this flow is the foundation for everything else in this section — once you see what happens by default, you'll know exactly which parts to customize.

<Image align="center" width="500px" src="https://files.readme.io/6ff8405d7e9d4168d56aff3935850111fa7074ced2f7c56950d2561e8d6f1dbc-reporter_lifecycle_overview.svg" />

### Start

When your test suite begins, the reporter creates a new test run in Qase. This happens automatically — you don't trigger it. The run appears in your Qase dashboard immediately, titled something like "Automated run 2025-01-15T10:30:00Z."

Behind the scenes, the reporter calls the Qase API to create the run, gets back a run ID, and holds onto it for the rest of the session. If you're watching your terminal, you won't see much — the reporter is intentionally quiet unless something fails.

One thing worth knowing: the reporter also sets the run ID as an environment variable (`QASE_TESTOPS_RUN_ID`) after creating the run. This matters if you're running parallel test workers — they can pick up the same run ID and report into a single run instead of creating separate ones.

### Report

As each test finishes, the reporter collects the result — title, status, duration, steps, attachments, parameters, everything you've annotated — and adds it to an internal queue.

The reporter doesn't wait until the end to send results. It uploads them in **batches** while your tests are still running. By default, a batch is sent every 200 results. For most test suites, this means results appear in Qase progressively — you can watch the dashboard fill up in near-real-time while the suite is still executing.

If a test fails, the reporter logs a direct link to that failure in the Qase dashboard. You'll see it in your terminal output:

```
See why this test failed: https://app.qase.io/run/DEMO/dashboard/123?source=logs&search=DEMO-42
```

This is one of the small things that makes automated reporting worth the setup — you don't have to go hunting for the failure in the UI.

### Complete

After the last test finishes, the reporter flushes any remaining results that haven't been sent yet, waits for all uploads to finish, and then completes the run.

Completing a run is what transitions it from "In Progress" to a final status (Passed or Failed) in Qase. It's also what triggers any downstream behaviors you've configured in your Qase project — like notifications or webhook integrations.

By default, the reporter **always completes the run**. This is the right behavior for most setups. But there are cases where you don't want this — for example, if multiple CI jobs report into the same run and only the last one should complete it. We'll cover that in the Test Runs page.

### The whole flow, in detail

<HTMLBlock>{`
<style>
  .stepper{font-family:var(--font-sans,system-ui,sans-serif);padding:20px 24px 24px}
  .steps-nav{display:flex;gap:0;margin-bottom:24px;border-radius:10px;overflow:hidden;border:1px solid var(--color-border-tertiary)}
  .step-btn{flex:1;padding:10px 8px;font-size:12px;font-weight:500;border:none;cursor:pointer;background:var(--color-background-secondary);color:var(--color-text-secondary);transition:background .15s,color .15s;border-right:1px solid var(--color-border-tertiary)}
  .step-btn:last-child{border-right:none}
  .step-btn.active{background:var(--color-background-primary);color:var(--color-text-primary)}
  .step-btn .dot{display:block;width:6px;height:6px;border-radius:50%;margin:0 auto 5px;background:var(--color-border-secondary);transition:background .15s}
  .step-btn.active .dot{background:#1D9E75}
  .panel{display:none}.panel.active{display:block}
  .phase-label{font-size:11px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;color:var(--color-text-tertiary);margin-bottom:6px}
  .phase-title{font-size:18px;font-weight:500;color:var(--color-text-primary);margin-bottom:4px}
  .phase-desc{font-size:13px;color:var(--color-text-secondary);margin-bottom:20px;line-height:1.6}
  .flow{display:flex;flex-direction:column;gap:0}
  .flow-row{display:flex;align-items:center;gap:0;min-height:44px}
  .actor{width:110px;min-width:110px;text-align:center}
  .actor-pill{display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500}
  .pill-teal{background:#E1F5EE;color:#085041}
  .pill-purple{background:#EEEDFE;color:#3C3489}
  .pill-coral{background:#FAECE7;color:#712B13}
  @media(prefers-color-scheme:dark){
    .pill-teal{background:#085041;color:#9FE1CB}
    .pill-purple{background:#3C3489;color:#CECBF6}
    .pill-coral{background:#712B13;color:#F5C4B3}
  }
  .msg-track{flex:1;position:relative;height:44px;display:flex;align-items:center}
  .msg-arrow{position:absolute;display:flex;align-items:center;gap:6px;font-size:12px;color:var(--color-text-secondary);width:100%}
  .msg-arrow.right .line{flex:1;height:1px;background:var(--color-border-secondary);position:relative}
  .msg-arrow.right .line::after{content:'';position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid var(--color-border-secondary)}
  .msg-arrow.left .line{flex:1;height:1px;background:var(--color-border-secondary);position:relative}
  .msg-arrow.left .line::before{content:'';position:absolute;left:-1px;top:-4px;border:5px solid transparent;border-right:7px solid var(--color-border-secondary)}
  .msg-arrow.dashed .line{background:none;border-top:1.5px dashed var(--color-border-secondary)}
  .msg-arrow.dashed .line::after{border-left-color:var(--color-border-secondary)}
  .msg-arrow.dashed .line::before{border-right-color:var(--color-border-secondary)}
  .msg-label{font-size:12px;color:var(--color-text-secondary);white-space:nowrap}
  .note-band{margin:4px 0;padding:7px 14px;border-radius:6px;background:var(--color-background-secondary);border:1px dashed var(--color-border-secondary);font-size:12px;color:var(--color-text-secondary);text-align:center}
  .actor-col{width:110px;min-width:110px;display:flex;flex-direction:column;align-items:center;gap:2px}
  .spacer-line{width:1px;height:12px;background:var(--color-border-tertiary)}
  .callout{margin-top:16px;padding:10px 14px;border-radius:8px;background:var(--color-background-secondary);border-left:3px solid #1D9E75;font-size:12px;color:var(--color-text-secondary);line-height:1.6}
  .callout.purple{border-left-color:#7F77DD}
  .callout.coral{border-left-color:#D85A30}
  .nav-footer{display:flex;justify-content:space-between;align-items:center;margin-top:20px}
  .nav-btn{padding:7px 18px;border-radius:8px;border:1px solid var(--color-border-secondary);background:var(--color-background-primary);color:var(--color-text-primary);font-size:13px;cursor:pointer;font-family:inherit}
  .nav-btn:disabled{opacity:.35;cursor:default}
  .step-counter{font-size:12px;color:var(--color-text-tertiary)}
</style>
<div class="stepper">
  <div class="steps-nav">
    <button class="step-btn active" onclick="goTo(0)"><span class="dot"></span>Start</button>
    <button class="step-btn" onclick="goTo(1)"><span class="dot"></span>Report</button>
    <button class="step-btn" onclick="goTo(2)"><span class="dot"></span>Batch upload</button>
    <button class="step-btn" onclick="goTo(3)"><span class="dot"></span>Complete</button>
  </div>

  <!-- Panel 0: Start -->
  <div class="panel active" id="panel-0">
    <div class="phase-label">Phase 1 of 4</div>
    <div class="phase-title">Start — create the run</div>
    <div class="phase-desc">When your suite begins, the reporter automatically calls the Qase API to create a new test run and holds onto the run ID for the rest of the session.</div>
    <div class="flow">
      <div class="flow-row">
        <div class="actor"><span class="actor-pill pill-teal">Your tests</span></div>
        <div class="msg-track">
          <div class="msg-arrow right"><div class="line"></div></div>
        </div>
        <div class="actor"><span class="actor-pill pill-purple">Reporter</span></div>
        <div style="width:110px"></div>
      </div>
      <div class="flow-row" style="min-height:8px"></div>
      <div class="flow-row" style="padding-left:110px">
        <div class="actor" style="width:110px;min-width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow right"><span class="msg-label">create test run</span><div class="line"></div></div>
        </div>
        <div class="actor"><span class="actor-pill pill-coral">Qase API</span></div>
      </div>
      <div class="flow-row" style="padding-left:110px">
        <div class="actor" style="width:110px;min-width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow left dashed"><div class="line"></div><span class="msg-label">run ID: 123</span></div>
        </div>
        <div class="actor"></div>
      </div>
    </div>
    <div class="callout">The reporter also sets <code>QASE_TESTOPS_RUN_ID</code> as an environment variable — parallel workers can pick this up to all report into the same run.</div>
  </div>

  <!-- Panel 1: Report -->
  <div class="panel" id="panel-1">
    <div class="phase-label">Phase 2 of 4</div>
    <div class="phase-title">Report — collect results</div>
    <div class="phase-desc">As each test finishes, the reporter collects its result and places it in an internal queue. Nothing is sent to Qase yet — it waits to send results in batches.</div>
    <div class="flow">
      <div class="flow-row">
        <div class="actor"><span class="actor-pill pill-teal">Your tests</span></div>
        <div class="msg-track">
          <div class="msg-arrow right"><span class="msg-label">test result</span><div class="line"></div></div>
        </div>
        <div class="actor"><span class="actor-pill pill-purple">Reporter</span></div>
        <div style="width:110px"></div>
      </div>
      <div class="flow-row">
        <div style="width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow right"><span class="msg-label">test result</span><div class="line"></div></div>
        </div>
        <div class="actor"></div>
        <div style="width:110px"></div>
      </div>
      <div class="flow-row">
        <div style="width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow right"><span class="msg-label">test result</span><div class="line"></div></div>
        </div>
        <div class="actor"></div>
        <div style="width:110px"></div>
      </div>
    </div>
    <div class="note-band">Results queue up inside the reporter — not sent one-by-one</div>
    <div class="callout purple">If a test fails, the reporter logs a direct link to the failure in your terminal output so you don't have to hunt for it in the UI.</div>
  </div>

  <!-- Panel 2: Batch upload -->
  <div class="panel" id="panel-2">
    <div class="phase-label">Phase 3 of 4</div>
    <div class="phase-title">Batch upload — results appear in real time</div>
    <div class="phase-desc">Once the queue hits 200 results (the default), the reporter uploads the batch to Qase while your suite is still running. Results appear progressively in the dashboard.</div>
    <div class="flow">
      <div class="flow-row">
        <div style="width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow right"><span class="msg-label">upload 200 results</span><div class="line"></div></div>
        </div>
        <div class="actor"><span class="actor-pill pill-coral">Qase API</span></div>
      </div>
      <div class="flow-row" style="min-height:8px"></div>
    </div>
    <div class="note-band">Threshold reached → flush → keep running → repeat</div>
    <div style="margin-top:8px" class="note-band">Tests still running while this upload happens</div>
    <div class="callout purple">The upload is non-blocking — your test suite doesn't pause. Results trickle into your Qase dashboard in near real time.</div>
  </div>

  <!-- Panel 3: Complete -->
  <div class="panel" id="panel-3">
    <div class="phase-label">Phase 4 of 4</div>
    <div class="phase-title">Complete — close the run</div>
    <div class="phase-desc">After the last test, the reporter flushes any remaining results and marks the run as complete. This transitions it from "In Progress" to Passed or Failed.</div>
    <div class="flow">
      <div class="flow-row">
        <div style="width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow right"><span class="msg-label">upload remaining results</span><div class="line"></div></div>
        </div>
        <div class="actor"><span class="actor-pill pill-coral">Qase API</span></div>
      </div>
      <div class="flow-row">
        <div style="width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow right"><span class="msg-label">complete run 123</span><div class="line"></div></div>
        </div>
        <div class="actor"></div>
      </div>
      <div class="flow-row">
        <div style="width:110px"></div>
        <div class="msg-track">
          <div class="msg-arrow left dashed"><div class="line"></div><span class="msg-label">run 123 → Passed / Failed</span></div>
        </div>
        <div class="actor"></div>
      </div>
    </div>
    <div class="callout coral">By default the reporter always completes the run. If multiple CI jobs report into the same run, you can disable auto-complete and let only the last job close it — covered in Test Runs.</div>
  </div>

  <div class="nav-footer">
    <button class="nav-btn" id="btn-prev" onclick="move(-1)" disabled>← Back</button>
    <span class="step-counter" id="step-counter">1 / 4</span>
    <button class="nav-btn" id="btn-next" onclick="move(1)">Next →</button>
  </div>
</div>
<script>
  let cur = 0;
  const total = 4;
  function goTo(i) {
    document.getElementById('panel-'+cur).classList.remove('active');
    document.querySelectorAll('.step-btn')[cur].classList.remove('active');
    cur = i;
    document.getElementById('panel-'+cur).classList.add('active');
    document.querySelectorAll('.step-btn')[cur].classList.add('active');
    document.getElementById('btn-prev').disabled = cur === 0;
    document.getElementById('btn-next').disabled = cur === total - 1;
    document.getElementById('step-counter').textContent = (cur+1) + ' / ' + total;
  }
  function move(d) { if(cur+d >= 0 && cur+d < total) goTo(cur+d); }
</script>
`}</HTMLBlock>

<br />

### What you can customize

Now that you see the default flow, here's what each page in this section lets you change:

* **Test Runs** — the run title, description, tags, and whether the reporter completes the run at the end. You can also point the reporter at an existing run instead of creating a new one.
* **Environments** — attach an environment slug to the run so Qase knows whether these results came from staging, production, or your local machine.
* **Test Plans** — create the run under a specific test plan, tying automated results to a planned testing cycle.
* **Qase Project Settings** — control what Qase does with results _after_ they arrive: auto-creating test cases, auto-updating existing ones, and which statuses trigger creation.
* **Configurations** — tag the run with browser/OS/device combinations so you can compare results across configurations.

***

The lifecycle is identical across all language ecosystems. The `startTestRun` → `addResult` (with batching) → `completeTestRun` pattern is implemented in every commons library:

**JavaScript** — creates the run, sets `QASE_TESTOPS_RUN_ID`, batches results, completes with optional public report link: [33-cite-0](#33-cite-0) [33-cite-1](#33-cite-1) [33-cite-2](#33-cite-2)

**Python** — same lifecycle, with threaded batch uploads and semaphore-limited concurrency: [33-cite-3](#33-cite-3) [33-cite-4](#33-cite-4) [33-cite-5](#33-cite-5)

**Java** — uses a dedicated upload executor thread with dynamic timeout computation based on attachment volume: [33-cite-6](#33-cite-6) [33-cite-7](#33-cite-7) [33-cite-8](#33-cite-8)

**PHP** — state-managed run creation with fallback on failure, batch flushing on completion: [33-cite-9](#33-cite-9) [33-cite-10](#33-cite-10) [33-cite-11](#33-cite-11)

The run creation in JavaScript shows all the options that feed into the API call — title, description, tags, environment, plan, configurations: [33-cite-12](#33-cite-12) [33-cite-13](#33-cite-13)

The `CoreReporter` wraps the lifecycle with fallback protection — if the main reporter fails at any phase, it falls back gracefully: [33-cite-14](#33-cite-14) [33-cite-15](#33-cite-15)

How do these two pieces land? Ready for the next sub-page (Test Runs)?
