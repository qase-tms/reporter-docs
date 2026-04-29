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
<!-- Phase 1 -->
<details style="margin-bottom:8px;border:1px solid #d3d1c7;border-radius:10px;overflow:hidden">
  <summary style="display:flex;align-items:center;gap:12px;padding:14px 16px;cursor:pointer;list-style:none;background:#f5f4f0">
    <span style="width:22px;height:22px;border-radius:50%;display:inline-flex;align-items:center;justify-content:center;font-size:11px;font-weight:500;flex-shrink:0;background:#E1F5EE;color:#085041">1</span>
    <span style="font-size:14px;font-weight:500;color:#1a1a18;flex:1">Start — create the run</span>
    <span style="font-size:12px;color:#888780">Suite begins</span>
  </summary>
  <div style="padding:16px;border-top:1px solid #d3d1c7">
    <p style="font-size:13px;color:#5f5e5a;margin:0 0 16px;line-height:1.6">When your suite begins, the reporter automatically calls the Qase API to create a new test run and holds onto the run ID for the rest of the session.</p>

    <div style="display:flex;flex-direction:column">
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#E1F5EE;color:#085041">Your tests</span></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span></div>
        <div style="width:100px"></div>
      </div>
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="width:100px;min-width:100px"></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">create test run</span>
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#FAECE7;color:#712B13">Qase API</span></div>
      </div>
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="width:100px;min-width:100px"></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <div style="flex:1;height:0;border-top:1.5px dashed #c8c6bc;position:relative"><span style="position:absolute;left:-1px;top:-5px;border:5px solid transparent;border-right:7px solid #c8c6bc"></span></div>
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">run ID: 123</span>
        </div>
        <div style="width:100px"></div>
      </div>
    </div>

    <div style="margin-top:12px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #1D9E75;font-size:12px;color:#5f5e5a;line-height:1.6">
      The reporter sets <code>QASE_TESTOPS_RUN_ID</code> as an env var — parallel workers can pick this up to report into the same run.
    </div>
  </div>
</details>

<!-- Phase 2 -->
<details style="margin-bottom:8px;border:1px solid #d3d1c7;border-radius:10px;overflow:hidden">
  <summary style="display:flex;align-items:center;gap:12px;padding:14px 16px;cursor:pointer;list-style:none;background:#f5f4f0">
    <span style="width:22px;height:22px;border-radius:50%;display:inline-flex;align-items:center;justify-content:center;font-size:11px;font-weight:500;flex-shrink:0;background:#EEEDFE;color:#3C3489">2</span>
    <span style="font-size:14px;font-weight:500;color:#1a1a18;flex:1">Report — collect results</span>
    <span style="font-size:12px;color:#888780">Per test</span>
  </summary>
  <div style="padding:16px;border-top:1px solid #d3d1c7">
    <p style="font-size:13px;color:#5f5e5a;margin:0 0 16px;line-height:1.6">As each test finishes, the reporter collects its result and places it in an internal queue. Nothing is sent to Qase yet — it waits to send results in batches.</p>

    <div style="display:flex;flex-direction:column">
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#E1F5EE;color:#085041">Your tests</span></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">test result</span>
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span></div>
        <div style="width:100px"></div>
      </div>
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">test result</span>
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px;min-width:100px"></div>
        <div style="width:100px"></div>
      </div>
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">test result</span>
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px;min-width:100px"></div>
        <div style="width:100px"></div>
      </div>
    </div>

    <div style="margin:4px 0;padding:6px 12px;border-radius:6px;background:#f5f4f0;border:1px dashed #c8c6bc;font-size:12px;color:#5f5e5a;text-align:center">
      Results queue up inside the reporter — not sent one-by-one
    </div>
    <div style="margin-top:12px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #7F77DD;font-size:12px;color:#5f5e5a;line-height:1.6">
      If a test fails, the reporter logs a direct link to the failure in your terminal — no hunting in the UI.
    </div>
  </div>
</details>

<!-- Phase 3 -->
<details style="margin-bottom:8px;border:1px solid #d3d1c7;border-radius:10px;overflow:hidden">
  <summary style="display:flex;align-items:center;gap:12px;padding:14px 16px;cursor:pointer;list-style:none;background:#f5f4f0">
    <span style="width:22px;height:22px;border-radius:50%;display:inline-flex;align-items:center;justify-content:center;font-size:11px;font-weight:500;flex-shrink:0;background:#EEEDFE;color:#3C3489">3</span>
    <span style="font-size:14px;font-weight:500;color:#1a1a18;flex:1">Batch upload — results appear in real time</span>
    <span style="font-size:12px;color:#888780">Every 200 results</span>
  </summary>
  <div style="padding:16px;border-top:1px solid #d3d1c7">
    <p style="font-size:13px;color:#5f5e5a;margin:0 0 16px;line-height:1.6">Once the queue hits 200 results, the reporter uploads the batch to Qase while your suite is still running. Results appear progressively in the dashboard.</p>

    <div style="display:flex;flex-direction:column">
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">upload 200 results</span>
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#FAECE7;color:#712B13">Qase API</span></div>
      </div>
    </div>

    <div style="margin:8px 0 4px;padding:6px 12px;border-radius:6px;background:#f5f4f0;border:1px dashed #c8c6bc;font-size:12px;color:#5f5e5a;text-align:center">
      Threshold reached → flush → keep running → repeat
    </div>
    <div style="margin:4px 0;padding:6px 12px;border-radius:6px;background:#f5f4f0;border:1px dashed #c8c6bc;font-size:12px;color:#5f5e5a;text-align:center">
      Tests still running while this upload happens
    </div>
    <div style="margin-top:12px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #7F77DD;font-size:12px;color:#5f5e5a;line-height:1.6">
      The upload is non-blocking — your suite doesn't pause. Results trickle into Qase in near real time.
    </div>
  </div>
</details>

<!-- Phase 4 -->
<details style="margin-bottom:8px;border:1px solid #d3d1c7;border-radius:10px;overflow:hidden">
  <summary style="display:flex;align-items:center;gap:12px;padding:14px 16px;cursor:pointer;list-style:none;background:#f5f4f0">
    <span style="width:22px;height:22px;border-radius:50%;display:inline-flex;align-items:center;justify-content:center;font-size:11px;font-weight:500;flex-shrink:0;background:#FAECE7;color:#712B13">4</span>
    <span style="font-size:14px;font-weight:500;color:#1a1a18;flex:1">Complete — close the run</span>
    <span style="font-size:12px;color:#888780">Suite ends</span>
  </summary>
  <div style="padding:16px;border-top:1px solid #d3d1c7">
    <p style="font-size:13px;color:#5f5e5a;margin:0 0 16px;line-height:1.6">After the last test, the reporter flushes any remaining results and marks the run complete — transitioning it from "In Progress" to Passed or Failed.</p>

    <div style="display:flex;flex-direction:column">
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#EEEDFE;color:#3C3489">Reporter</span></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">upload remaining results</span>
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px;min-width:100px;text-align:center"><span style="display:inline-block;padding:4px 10px;border-radius:20px;font-size:11px;font-weight:500;background:#FAECE7;color:#712B13">Qase API</span></div>
      </div>
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="width:100px;min-width:100px"></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">complete run 123</span>
          <div style="flex:1;height:1px;background:#c8c6bc;position:relative"><span style="position:absolute;right:-1px;top:-4px;border:5px solid transparent;border-left:7px solid #c8c6bc"></span></div>
        </div>
        <div style="width:100px"></div>
      </div>
      <div style="display:flex;align-items:center;min-height:40px">
        <div style="width:100px"></div>
        <div style="width:100px;min-width:100px"></div>
        <div style="flex:1;display:flex;align-items:center;gap:6px;padding:0 4px">
          <div style="flex:1;height:0;border-top:1.5px dashed #c8c6bc;position:relative"><span style="position:absolute;left:-1px;top:-5px;border:5px solid transparent;border-right:7px solid #c8c6bc"></span></div>
          <span style="font-size:12px;color:#5f5e5a;white-space:nowrap">run 123 → Passed / Failed</span>
        </div>
        <div style="width:100px"></div>
      </div>
    </div>

    <div style="margin-top:12px;padding:10px 14px;border-radius:8px;background:#f5f4f0;border-left:3px solid #D85A30;font-size:12px;color:#5f5e5a;line-height:1.6">
      By default the reporter always completes the run. If multiple CI jobs share a run, you can disable auto-complete and let only the last job close it — covered in Test Runs.
    </div>
  </div>
</details>
`}</HTMLBlock>

<br />

### What you can customize

Now that you see the default flow, here's what each page in this section lets you change:

* **Test Runs** — the run title, description, tags, and whether the reporter completes the run at the end. You can also point the reporter at an existing run instead of creating a new one.
* **Environments** — attach an environment slug to the run so Qase knows whether these results came from staging, production, or your local machine.
* **Test Plans** — create the run under a specific test plan, tying automated results to a planned testing cycle.
* **Qase Project Settings** — control what Qase does with results _after_ they arrive: auto-creating test cases, auto-updating existing ones, and which statuses trigger creation.
* **Configurations** — tag the run with browser/OS/device combinations so you can compare results across configurations.
