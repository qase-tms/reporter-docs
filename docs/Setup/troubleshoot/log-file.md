---
title: log file
deprecated: false
hidden: false
metadata:
  robots: index
---
The previous section covered debug mode — what it prints and why. This section is about where that output goes when it's written to disk, and how to use the file in practice.

<br />

## Why a file

Console output scrolls past. In CI, it's buried in thousands of lines from the test runner, the build tool, and whatever else runs in your pipeline. If you need to go back to it — to search for a specific API response, to compare two runs, to share with someone — you're scrolling through a CI job log trying to find the `[Qase]` lines.

The log file is the same content, written to a file in your project directory. It's there after the run finishes. You can download it, search it, attach it to a support ticket, diff it against yesterday's file.

<br />

## Where it lives

Every reporter writes to a `logs/` directory in your project root. The file name varies by ecosystem:

| Ecosystem             | File name                 | Example                               |
| --------------------- | ------------------------- | ------------------------------------- |
| JavaScript/TypeScript | `log.txt`                 | `./logs/log.txt`                      |
| Python                | `{prefix}_{YYYYMMDD}.log` | `./logs/_20250115.log`                |
| Java                  | `log.txt`                 | `./logs/log.txt`                      |
| PHP                   | `log_{Y-m-d}.log`         | `./logs/log_2025-01-15.log`           |
| Go                    | `logs_{timestamp}.log`    | `./logs/logs_2025-01-15_14-04-05.log` |

The reporter creates the `logs/` directory automatically if it doesn't exist. You don't need to set anything up.

Add `logs/` to your `.gitignore`. You don't want log files in version control.

<br />

## Enabling it

File logging is off by default in most ecosystems. Two ways to turn it on:

**Through the config file:**

```json
{
  "logging": {
    "file": true
  }
}
```

**Through an environment variable:**

```bash
QASE_LOGGING_FILE=true
```

There's a shortcut: setting `debug: true` automatically enables file logging. If you followed the Debug Mode article and turned on debug, you already have a log file.

> **Note:** PHP and Go enable file logging by default. If you're using those ecosystems, the file is already being written.

<br />

## What's in the file

The same content that appears in the console — config dump, lifecycle events, API requests and responses, errors. The format is slightly different because the file doesn't include ANSI color codes.

A Playwright example. The console shows:

```
[INFO] qase: Test run 12345 started
[DEBUG] qase: Config: {"mode":"testops","testops":{"project":"DEMO","api":{"token":"abc***wxyz"},...}}
[DEBUG] qase: Host data: {"node":"20.11.0","os":"linux","package":"playwright-qase-reporter","version":"3.0.0"}
```

The log file (`./logs/log.txt`) contains the same lines with timestamps:

```
[2025-01-15T14:32:00.123Z] [INFO] qase: Test run 12345 started
[2025-01-15T14:32:00.125Z] [DEBUG] qase: Config: {"mode":"testops","testops":{"project":"DEMO","api":{"token":"abc***wxyz"},...}}
[2025-01-15T14:32:00.126Z] [DEBUG] qase: Host data: {"node":"20.11.0","os":"linux","package":"playwright-qase-reporter","version":"3.0.0"}
```

The timestamps are the main difference. In the console, you're reading output as it happens. In the file, the timestamps let you reconstruct timing after the fact — how long the API took to respond, whether there was a gap between test completion and result upload, etc.

Java's log format includes more context — thread ID and caller class — because Java tests often run in parallel threads:

```
[2025-01-15 14:32:00.123] [INFO] [Thread-14] [io.qase.commons.reporters.TestopsReporter] Test run 12345 started
```

<br />

## Saving the log file as a CI artifact

This is the main reason the log file exists. In CI, you want to save it alongside your test results so you can download it later without re-running the job.

**GitHub Actions:**

```yaml
- name: Run tests
  run: npx playwright test
  env:
    QASE_MODE: testops
    QASE_DEBUG: true
    QASE_TESTOPS_API_TOKEN: ${{ secrets.QASE_API_TOKEN }}
    QASE_TESTOPS_PROJECT: DEMO

- name: Upload Qase logs
  if: always()
  uses: actions/upload-artifact@v4
  with:
    name: qase-logs
    path: logs/
```

The `if: always()` matters. Without it, the upload step is skipped when tests fail — which is exactly when you need the log file most.

**GitLab CI:**

```yaml
test:
  script:
    - npx playwright test
  artifacts:
    when: always
    paths:
      - logs/
```

Same idea: `when: always` ensures the log is saved regardless of test outcome.

<br />

## Searching the file

The log file is plain text. Standard tools work:

```bash
# Find all errors
grep "ERROR" logs/log.txt

# Find API responses
grep "status" logs/log.txt

# Find what config the reporter used
grep "Config:" logs/log.txt
```

When you're looking at a problem — say, results didn't appear in Qase — the log file gives you a searchable record. Instead of re-running the job with debug on and watching the console, you already have the file from the original run.

<br />

## Java: log rotation

Java's logger rotates the log file when it exceeds 100MB. The current file is always `logs/log.txt`. When it rotates, the old file becomes `log.txt.1`, the previous one becomes `log.txt.2`, and so on, up to 5 backup files. This prevents the log directory from growing without bound in long-running test suites or CI pipelines that reuse the same workspace.

Other ecosystems don't rotate. Python and PHP use date-stamped filenames instead, so each day's run gets its own file. JavaScript and Go use a single file that grows until you delete it.

<br />

## Console-only or file-only

You can run console and file logging independently. If your CI pipeline already captures stdout and you don't want the extra file:

```json
{
  "logging": {
    "console": true,
    "file": false
  }
}
```

Or if you want the file but don't want Qase output cluttering the console (useful when your test runner's own output is what you're watching):

```json
{
  "logging": {
    "console": false,
    "file": true
  }
}
```

<br />

## What's next

Having the log file is step one. The next article covers a structured approach to troubleshooting — how to use the log file (and other tools) to diagnose problems quickly, and how to share findings with Qase support or file an issue on GitHub when you hit something that needs a fix.
