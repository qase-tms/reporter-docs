---
title: Empty test run, or Missing results?
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
If you're seeing empty test runs or runs with missing results in Qase, despite your tests running successfully in your CI pipeline, this guide will help troubleshoot the problem, identify why your test results aren't showing up in Qase, and fix it step by step.

## So, what's going on?

When your CI pipeline (like GitHub Actions or Jenkins) runs tests as usual with, but Qase test run is empty or has missing results, it usually means something's going wrong when the Qase reporter tries to send those results to the Qase TestOps API. 

The reporter does three main things:

1. Starts a new test run in Qase.
2. Sends the test results to Qase.
3. Marks the run as complete.

The issue often lies in that second step: sending the results. If something goes wrong here, Qase might not receive any results, leaving your test run empty. 

A common reason for this is that one or a few test results in the batch being sent to Qase are malformed or not in the format the API expects. When this happens, the entire batch gets rejected, and no results show up in Qase.

### What's a batch here?

The reporter uses this public API endpoint to push results into Qase: 

POST [https://api.qase.io/v1/result/{code}/{id}/bulk](https://developers.qase.io/reference/create-result-bulk)

This endpoint allows up to 2000 test results to be sent in a single API request, though we recommend a batch size of 100–400 for optimal performance. By default, the Qase reporter is set to a batch size of 200. This means it collects up to 200 test results, groups them together, and sends them in one API call to Qase.

<br />

## To track down the problem, we use two key options:

**Debug Mode**: This gives us detailed logs of what the reporter is doing (the payload of the API requests, including any errors) when it tries to send results to the Qase API.

**Batch Size**: By default, it sends `200` results at once. If one result in the batch is problematic, the whole batch gets rejected. Setting the batch size to `1` sends each result individually, so a single bad result won't block the others, helping us pinpoint the issue.

Our goal is to use these options to confirm whether the issue is with specific test results, identify any problematic tests, and ensure your results show up correctly in Qase. 

<br />

## Troubleshooting Steps

Follow these steps to diagnose and resolve the issue of missing test results in Qase. We'll start by gathering more information with debug mode and then use batch size adjustments to isolate any problematic tests.

<br />

### Step 1: Enable Debug Logging

First, let's turn on debug mode to get a detailed look at what the Qase reporter is doing. This will log every step, including attempts to send results to the Qase API and any errors that pop up.

To enable debug mode in your CI pipeline:

Add the environment variable `QASE_DEBUG=true` to your CI workflow. 

For example, in a GitHub Actions workflow, include the following:

```
env:  
  QASE_DEBUG: true
```

If you're using the `qase.config.json` file to define reporter options, you can add the debug setting there as well.

The debug logs will be saved to a file in your project's `<project-root>/logs/` directory. Make sure to configure your CI pipeline to save these logs as an artifact so you can review them. 

These logs will show:

1. System details (e.g., Python version, reporter version).
2. The config options that are being read by the reporter.
3. Test run creation details.
4. Results' payload and any errors or rejected API requests.

Now, run your tests again and check the logs for clues about why results aren't reaching Qase.

<br />

### Step 2: Verify Test Execution in CI Pipeline

Next, confirm that your tests are running as expected in your CI pipeline. Check the pipeline logs to see:

1. How many tests were executed.
2. The outcomes (e.g., passed, failed, skipped).
3. Any errors during test execution.

Compare these numbers with what you see in the Qase test run. 

If your CI logs show 27 tests (e.g., 20 passed, 7 failed) but Qase shows zero, it confirms the results aren't being sent properly.

<br />

### Step 3: Set Batch Size to 1

To isolate problematic test results, set the batch size to 1. This makes the reporter send each test result in its own API request, so a single bad result won't block the entire batch. 

You can set the batch size in the same way:  
Environment Variable: Set `QASE_TESTOPS_BATCH_SIZE=1` in your CI workflow, like this:

```
env:  
  QASE_TESTOPS_BATCH_SIZE: 1
```

Configuration File: Update qase.config.json with:

```
{  
  "testops": {
    "batch": {
      "size": 1
}
```

Run your tests again and check the Qase test run. If results now appear, it means one or more test results were causing the batch to be rejected when using the default batch size (200).

<br />

### Step 4: Compare Result Counts

If setting the batch size to 1 gets results showing in Qase, compare the number of results in Qase with those in your CI pipeline logs. 

For example:

**CI logs**: 27 tests (20 passed, 7 failed).  
**Qase**: 25 tests (18 passed, 7 failed).

A difference (e.g., 2 missing tests) suggests that specific test results are being rejected by the Qase API. 

This could be due to:

1. Malformed JSON in the test result.
2. Missing or invalid fields (e.g., test case ID, status).

Or, any other issue.

<br />

### Step 5: Identify Problematic Tests

To pinpoint the problematic tests:

**Check Debug Logs**: Look for errors or warnings in the debug logs related to specific test results, such as HTTP 400 Bad Request responses or API error messages.

**Match Test Cases**: Compare test case IDs or names between the CI pipeline and Qase to identify which tests are missing.

**Run Tests Individually**: Use a command like `QASE_TESTOPS_BATCH_SIZE=1 pytest <test_file>::<test_name>` to test suspected problematic tests one by one and see if they get rejected.

<br />

### Step 6: Contact support for assistance

For any problematic tests identified, examine the result payload for any apparent issues or reach out to support with your findings so far:

To help us resolve your issue quickly, please provide the following details in your initial message:

- **Plugins and Packages**: Let us know about any additional plugins or packages in your CI pipeline, such as `pytest-xdist` (used for parallel test execution), as they can sometimes affect the Qase reporter’s logging or result aggregation.

<br />

- **Configuration Files**: Share relevant configuration files, such as `qase.config.jso` or your CI workflow file (e.g., `.github/workflows/workflow.yml`). If your setup involves complex workflows, nested triggers, or anything unconventional, including these files will help us understand your environment.

<br />

- **Debug Logs and Screenshots**: Include all debug logs (generated with `QASE_DEBUG=true`), screenshots of the test result statistics from your CI pipeline, and a public link to the affected Qase test run (e.g., `https://app.qase.io/run/<project-code>/dashboard/<run-id>`).

<br />

- **Recent Changes**: Mention any recent updates to dependencies, CI configurations, or other changes in your setup that might impact the Qase reporter’s behavior.

<br />

Providing these details upfront will help us dive into the issue and get you back on track faster!