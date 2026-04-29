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
<div id="rl-stepper">

  <!-- Nav tabs -->
  <div style="display:flex;gap:0;margin-bottom:24px;border-radius:10px;overflow:hidden;border:1px solid #e0ddd6">

    <button id="rl-btn-0" onclick="rlGoTo(0)" style="flex:1;padding:10px 8px;font-size:12px;font-weight:500;border:none;border-right:1px solid #e0ddd6;cursor:pointer;font-family:inherit;background:#ffffff;color:#1a1a18;transition:background .15s,color .15s">
      <span id="rl-dot-0" style="display:block;width:6px;height:6px;border-radius:50%;margin:0 auto 5px;background:#1D9E75"></span>
      Start
    </button>

    <button id="rl-btn-1" onclick="rlGoTo(1)" style="flex:1;padding:10px 8px;font-size:12px;font-weight:500;border:none;border-right:1px solid #e0ddd6;cursor:pointer;font-family:inherit;background:#f5f4f0;color:#5f5e5a;transition:background .15s,color .15s">
      <span id="rl-dot-1" style="display:block;width:6px;height:6px;border-radius:50%;margin:0 auto 5px;background:#c8c6bc"></span>
      Report
    </button>

    <button id="rl-btn-2" onclick="rlGoTo(2)" style="flex:1;padding:10px 8px;font-size:12px;font-weight:500;border:none;border-right:1px solid #e0ddd6;cursor:pointer;font-family:inherit;background:#f5f4f0;color:#5f5e5a;transition:background .15s,color .15s">
      <span id="rl-dot-2" style="display:block;width:6px;height:6px;border-radius:50%;margin:0 auto 5px;background:#c8c6bc"></span>
      Batch upload
    </button>

    <button id="rl-btn-3" onclick="rlGoTo(3)" style="flex:1;padding:10px 8px;font-size:12px;font-weight:500;border:none;border-right:none;cursor:pointer;font-family:inherit;background:#f5f4f0;color:#5f5e5a;transition:background .15s,color .15s">
      <span id="rl-dot-3" style="display:block;width:6px;height:6px;border-radius:50%;margin:0 auto 5px;background:#c8c6bc"></span>
      Complete
    </button>

  </div>

  <!-- Panel 0: Start -->
  <div id="rl-panel-0" style="display:block">
    <div style="font-size:11px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;color:#888780;margin-bottom:6px">Phase 1 of 4</div>
    <div style="font-size:18px;font-weight:500;color:#1a1a18;margin-bottom:4px">Start — create the run</div>
    <div style="font-size:13px;color:#5f5e5a;margin-bottom:20px;line-height:1.6">When your suite begins, the reporter automatically calls the Qase API to create a new test run and holds onto the run ID for the rest of the session.</div>

    <!-- Flow -->
    <div style="display:flex;flex-direction:column">

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#E1F5EE;color:#085041">Your tests</span>
        </div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span>
        </div>
        <div style="width:110px"></div>
      </div>

      <div style="display:flex;align-items:center;min-height:8px"></div>

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px;min-width:110px"></div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">create test run</span>
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#FAECE7;color:#712B13">Qase API</span>
        </div>
      </div>

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px;min-width:110px"></div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <div style="flex:1;height:0;border-top:1.5px dashed #c8c6bc;position:relative">
              <span style="position:absolute;left:-1px;top:-5px;border:5px solid transparent;border-right:7px solid #c8c6bc"></span>
            </div>
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">run ID: 123</span>
          </div>
        </div>
        <div style="width:110px;min-width:110px"></div>
      </div>

    </div>

    <div style="margin-top:16px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #1D9E75;font-size:12px;color:#5f5e5a;line-height:1.6">
      The reporter also sets <code>QASE_TESTOPS_RUN_ID</code> as an environment variable — parallel workers can pick this up to all report into the same run.
    </div>
  </div>

  <!-- Panel 1: Report -->
  <div id="rl-panel-1" style="display:none">
    <div style="font-size:11px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;color:#888780;margin-bottom:6px">Phase 2 of 4</div>
    <div style="font-size:18px;font-weight:500;color:#1a1a18;margin-bottom:4px">Report — collect results</div>
    <div style="font-size:13px;color:#5f5e5a;margin-bottom:20px;line-height:1.6">As each test finishes, the reporter collects its result and places it in an internal queue. Nothing is sent to Qase yet — it waits to send results in batches.</div>

    <div style="display:flex;flex-direction:column">

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#E1F5EE;color:#085041">Your tests</span>
        </div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">test result</span>
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span>
        </div>
        <div style="width:110px"></div>
      </div>

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">test result</span>
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px"></div>
      </div>

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">test result</span>
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px"></div>
      </div>

    </div>

    <div style="margin:4px 0;padding:7px 14px;border-radius:6px;background:#f5f4f0;border:1px dashed #c8c6bc;font-size:12px;color:#5f5e5a;text-align:center">
      Results queue up inside the reporter — not sent one-by-one
    </div>
    <div style="margin-top:16px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #7F77DD;font-size:12px;color:#5f5e5a;line-height:1.6">
      If a test fails, the reporter logs a direct link to the failure in your terminal output so you don't have to hunt for it in the UI.
    </div>
  </div>

  <!-- Panel 2: Batch upload -->
  <div id="rl-panel-2" style="display:none">
    <div style="font-size:11px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;color:#888780;margin-bottom:6px">Phase 3 of 4</div>
    <div style="font-size:18px;font-weight:500;color:#1a1a18;margin-bottom:4px">Batch upload — results appear in real time</div>
    <div style="font-size:13px;color:#5f5e5a;margin-bottom:20px;line-height:1.6">Once the queue hits 200 results (the default), the reporter uploads the batch to Qase while your suite is still running. Results appear progressively in the dashboard.</div>

    <div style="display:flex;flex-direction:column">
      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span>
        </div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">upload 200 results</span>
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#FAECE7;color:#712B13">Qase API</span>
        </div>
      </div>
    </div>

    <div style="margin:4px 0;padding:7px 14px;border-radius:6px;background:#f5f4f0;border:1px dashed #c8c6bc;font-size:12px;color:#5f5e5a;text-align:center">
      Threshold reached → flush → keep running → repeat
    </div>
    <div style="margin:8px 0 0;padding:7px 14px;border-radius:6px;background:#f5f4f0;border:1px dashed #c8c6bc;font-size:12px;color:#5f5e5a;text-align:center">
      Tests still running while this upload happens
    </div>
    <div style="margin-top:16px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #7F77DD;font-size:12px;color:#5f5e5a;line-height:1.6">
      The upload is non-blocking — your test suite doesn't pause. Results trickle into your Qase dashboard in near real time.
    </div>
  </div>

  <!-- Panel 3: Complete -->
  <div id="rl-panel-3" style="display:none">
    <div style="font-size:11px;font-weight:500;letter-spacing:.06em;text-transform:uppercase;color:#888780;margin-bottom:6px">Phase 4 of 4</div>
    <div style="font-size:18px;font-weight:500;color:#1a1a18;margin-bottom:4px">Complete — close the run</div>
    <div style="font-size:13px;color:#5f5e5a;margin-bottom:20px;line-height:1.6">After the last test, the reporter flushes any remaining results and marks the run as complete. This transitions it from "In Progress" to Passed or Failed.</div>

    <div style="display:flex;flex-direction:column">

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span>
        </div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">upload remaining results</span>
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px;text-align:center">
          <span style="display:inline-block;padding:5px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#FAECE7;color:#712B13">Qase API</span>
        </div>
      </div>

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px;min-width:110px"></div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">complete run 123</span>
            <div style="flex:1;height:1px;background:#c8c6bc;position:relative">
              <span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span>
            </div>
          </div>
        </div>
        <div style="width:110px;min-width:110px"></div>
      </div>

      <div style="display:flex;align-items:center;min-height:44px">
        <div style="width:110px;min-width:110px"></div>
        <div style="width:110px;min-width:110px"></div>
        <div style="flex:1;position:relative;height:44px;display:flex;align-items:center">
          <div style="display:flex;align-items:center;gap:6px;width:100%">
            <div style="flex:1;height:0;border-top:1.5px dashed #c8c6bc;position:relative">
              <span style="position:absolute;left:-1px;top:-5px;border:5px solid transparent;border-right:7px solid #c8c6bc"></span>
            </div>
            <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">run 123 → Passed / Failed</span>
          </div>
        </div>
        <div style="width:110px;min-width:110px"></div>
      </div>

    </div>

    <div style="margin-top:16px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #D85A30;font-size:12px;color:#5f5e5a;line-height:1.6">
      By default the reporter always completes the run. If multiple CI jobs report into the same run, you can disable auto-complete and let only the last job close it — covered in Test Runs.
    </div>
  </div>

  <!-- Footer nav -->
  <div style="display:flex;justify-content:space-between;align-items:center;margin-top:20px">
    <button id="rl-btn-prev" onclick="rlMove(-1)" disabled style="padding:7px 18px;border-radius:8px;border:1px solid #c8c6bc;background:#ffffff;color:#1a1a18;font-size:13px;cursor:pointer;font-family:inherit;opacity:.35">← Back</button>
    <span id="rl-counter" style="font-size:12px;color:#888780">1 / 4</span>
    <button id="rl-btn-next" onclick="rlMove(1)" style="padding:7px 18px;border-radius:8px;border:1px solid #c8c6bc;background:#ffffff;color:#1a1a18;font-size:13px;cursor:pointer;font-family:inherit;opacity:1">Next →</button>
  </div>

</div>

<script>
(function(){
  var cur = 0, total = 4;
  window.rlGoTo = function(i) {
    document.getElementById('rl-panel-' + cur).style.display = 'none';
    var oldBtn = document.getElementById('rl-btn-' + cur);
    oldBtn.style.background = '#f5f4f0';
    oldBtn.style.color = '#5f5e5a';
    document.getElementById('rl-dot-' + cur).style.background = '#c8c6bc';
    cur = i;
    document.getElementById('rl-panel-' + cur).style.display = 'block';
    var newBtn = document.getElementById('rl-btn-' + cur);
    newBtn.style.background = '#ffffff';
    newBtn.style.color = '#1a1a18';
    document.getElementById('rl-dot-' + cur).style.background = '#1D9E75';
    var prev = document.getElementById('rl-btn-prev');
    var next = document.getElementById('rl-btn-next');
    prev.disabled = cur === 0;
    prev.style.opacity = cur === 0 ? '.35' : '1';
    next.disabled = cur === total - 1;
    next.style.opacity = cur === total - 1 ? '.35' : '1';
    document.getElementById('rl-counter').textContent = (cur + 1) + ' / ' + total;
  };
  window.rlMove = function(d) { if(cur + d >= 0 && cur + d < total) rlGoTo(cur + d); };
})();
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
