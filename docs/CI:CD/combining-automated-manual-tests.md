---
title: Combining Automated + Manual tests
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
Testing in the real world is rarely all-manual or all-automated. Most teams use a mix — some tests are better off being automated (for speed, consistency, or coverage), while others are more practical as manual tests (for exploratory, usability, or edge-case validation).

Typically, the testing cycle kicks off right after a pull request is opened — the app is built, deployed to a staging environment, and then a suite of automated tests runs in the CI pipeline. These automated tests are usually the first gate: they give fast feedback, catch regressions, and confirm that the basics are intact. After that, manual testers jump in to validate the feature in staging, run deeper checks, and verify anything automation might have missed.

<br />

## The Problem: Separate Test Runs

When you’re using Qase for both types of testing, keeping all this activity inside a single test run may not appear to be straightforward. The Qase reporter in your CI setup will automatically create a test run, push results, and mark the run as complete — right after the automated tests finish. Meanwhile, your manual testers are left to start a separate run through the Qase UI, select test cases, assign them, and so on. So for one release cycle, you end up with two separate test runs — one for automation, and another for manual QA. Not ideal.

Let's see how we can combine both manual and automated testing into a single unified test run using Test Plans in Qase. This not only simplifies your workflow, but also gives your team a single source of truth for every release cycle.

<br />

## The Solution: Use Test Plans

Once you’ve integrated a Qase reporter (for example, the Pytest or Jest reporter), and it does exactly what it’s supposed to:

1. Starts a test run.
2. Sends results.
3. Marks the run as complete.

**Test Plans** allow you to group test cases for easy reuse. You can assign testers to specific cases, organize them by priority or feature, and we'll reuse the entire plan for each new build or release.

Think of it like this: instead of hunting down test cases one by one for every test run, you create the plan once, and just start the test run from it (or in our case, trigger it via the CI pipeline).

When you link a Test Plan to your automated test reporter, here’s what changes:

- The reporter still creates the test run automatically.
- But now, it uses your predefined test plan to do so.
- All test cases from the plan — both manual and automated — are included in the run.
- Automated results are marked as executed.
- Manual test cases remain open with “Untested” status.
- The run stays open until your team manually completes it.

Everything happens in one place!

<br />

## How to set this up?

### Step 1: Create a Test Plan

First, go to your Qase project and create a Test Plan:

- Add all the relevant test cases you want to cover — both automated and manual.
- Assign your team members for the manual cases, if needed.
- Save the plan and [note the Test Plan ID](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code?location=conversation#h_d016877ffb) (you’ll need this in the next step).

<br />

### Step 2: Update Your Reporter Configuration

Next, you’ll configure your reporter to create the test run from your test plan.

Add these variables in your workflow:

```yaml YAML
env:
  QASE_TESTOPS_PLAN_ID: 123  # Replace with your actual plan ID  
  QASE_TESTOPS_RUN_COMPLETE: false   # Don’t auto-complete the run
```

You can also specify the option in the `qase.config.json` file under `testops.plan.id` if you'd like.

⚠️ Important: Setting `QASE_TESTOPS_RUN_COMPLETE` to false ensures that the test run stays open after the automated tests finish, allowing your manual testers to do their part.

<br />

### Step 3: Run Your Pipeline

Once configured:

- The reporter creates a new test run from the test plan.
- All cases from the plan are included in the test run and they'll be in the untested status. If any of the test cases in the plan have parameters, the resulting test run will contain all possible combinations of the parameter values.
- Automated tests are marked as passed/failed based on execution results.
- Manual tests will still remain untested, so your team can jump in, update results, and eventually close the run.