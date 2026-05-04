---
title: batching
deprecated: false
hidden: false
metadata:
  robots: index
---
Small test suites — 20, 50, even 100 tests — work fine with the defaults. You run your tests, the reporter sends results, everything finishes in a few seconds. You don't need to think about batch size.

It starts to matter when your suite grows. 500 tests. 1,000 tests. Tests with screenshots attached. Tests with video recordings. At that scale, the time the reporter spends uploading results becomes noticeable. Your CI job sits there for an extra 30 seconds, a minute, sometimes longer — not running tests, just uploading.

That's what batch size controls: how many results the reporter accumulates before sending them to the API in one request.

<br />

## How batching works

The reporter doesn't send each result individually. It collects results in memory and sends them in batches. The default batch size is **200** across all ecosystems.

```
Tests finish → Results accumulate → Batch threshold reached → Upload → Next batch → ... → Run complete
```

Two things trigger an upload:

1. **The batch is full.** If `batch.size` is 200 and the reporter has collected 200 results, it sends them immediately — even while tests are still running.
2. **The run is ending.** When all tests finish, the reporter sends whatever results remain (even if the batch isn't full), then completes the run.

This means with a 500-test suite and `batch.size: 200`, you'll see three uploads: 200, 200, and 100.

<br />

## The config

```json
{
  "testops": {
    "batch": {
      "size": 200
    }
  }
}
```

Or via environment variable:

```bash
QASE_TESTOPS_BATCH_SIZE=200
```

Valid range: `1` to `2000`. Values outside this range are rejected with a warning, and the default (200) is used instead.

<br />

## Choosing a batch size

**Smaller batches** (e.g., 10–50) mean results appear in Qase sooner. If you're watching a long-running suite in real time, you'll see results trickle in during execution rather than all at once at the end. The trade-off: more HTTP requests, more overhead per request.

**Larger batches** (e.g., 500–2000) mean fewer HTTP requests and less overhead. Good for suites that run fast and don't need real-time visibility. The trade-off: results appear in Qase only when a batch fills up or the run ends.

**The default (200)** is a reasonable middle ground for most teams. Change it when you have a specific reason:

* Set it lower if you want real-time visibility during long-running suites
* Set it higher if you have thousands of tests and want to minimize upload overhead
* Set it to `1` if you're debugging and want to see each result immediately (not recommended for production)

<br />

## Attachments are the real bottleneck

Batch size controls how many _results_ are sent per request. But the actual upload time is usually dominated by **attachments** — screenshots, videos, trace files.

Attachments are uploaded separately, in their own batches. The API enforces these limits per request:

* Up to **32 MB** per file
* Up to **128 MB** per request
* Up to **20 files** per request

Files exceeding 32 MB are skipped with a warning. The reporter automatically splits attachments into batches that respect these limits.

<br />

### Java-specific: parallel uploads and timeouts

The Java reporter has additional tuning options for attachment uploads:

```json
{
  "testops": {
    "batch": {
      "size": 200,
      "uploadThreads": 4,
      "uploadTimeout": 300
    }
  }
}
```

* **`uploadThreads`** (default: 4, range: 1–32) — number of parallel threads for uploading attachment batches. Higher values help when you have many attachments spread across batches.
* **`uploadTimeout`** (default: 300 seconds) — minimum timeout for uploads. The reporter scales this dynamically based on total attachment size (1 MB/s baseline), so large attachments get more time automatically.

These options are currently Java-only. Other ecosystems upload attachments sequentially.

<br />

### What happens when an upload fails

The reporter isolates batch failures. If one batch of attachments fails to upload, the other batches continue. After retries are exhausted, the reporter re-sends the affected test results _without_ their attachments — so you still get the pass/fail data even if the evidence didn't make it.

This is logged at WARN level:

```
Attachment upload failed after retries, result will be sent without attachments [file1.png, file2.png]
```

<br />

## Upload summary

At the end of a run, the Java reporter logs an aggregate summary:

```
Upload summary: 450 results, 120 attachments, 52428800 bytes, 8500 ms total, 0 failed batches
```

This tells you at a glance how much data was uploaded and whether anything failed. Other ecosystems log per-batch progress but don't yet have this aggregate summary.

<br />

### To sum up

For most teams, the defaults work. If you're tuning:

1. **Start with debug mode** to see how many batches are being sent and how long each takes
2. **If uploads are slow**, check whether it's results or attachments. Attachments are almost always the bottleneck
3. **If you have many large attachments**, consider whether all of them are necessary. A 5 MB screenshot per test across 500 tests is 2.5 GB of uploads
4. **If you're on Java with heavy attachments**, increase `uploadThreads` to 8 or 16 and see if parallel uploads help
