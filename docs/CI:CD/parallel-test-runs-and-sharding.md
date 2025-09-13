---
title: Parallel execution and sharding
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
As test suites grow larger, execution time often becomes a challenge. Running hundreds of tests one after another can slow down feedback cycles, which is why many teams adopt parallel execution or sharding. These approaches distribute tests across multiple workers or shards, allowing them to run simultaneously and finish faster.

Parallel execution can be set up in a few different ways, depending on the environment and tooling:

* Parallel workers – Tests are divided across multiple workers (threads or processes) on the same machine, each running a portion of the suite simultaneously.
* Sharding – The test suite is split into shards, with each shard running on a different machine or container. This is especially useful in CI/CD pipelines where cloud infrastructure can spin up multiple runners in parallel.

Consider a typical sharded workflow: tests are distributed across 4 shards, and individual reports are merged into a single consolidated HTML report upon completion.

[Example workflow for a standard Sharding setup](https://github.com/qase-tms/playwright-demo/blob/main/.github/workflows/standard-sharding.yml)

This article uses Playwright as the example, but the same steps can be applied to almost all other reporters.

***

#### Problem:

When tests are executed in parallel or through sharding, a common issue arises during reporting to Qase, instead of all results being grouped into a single test run, multiple runs are created, one for each shard or worker.

<br />

#### How the Reporter Currently Works

For each test runner instance that gets executed as a process, the reporter works alongside it. It first creates a test run in Qase, packages up the results it receives from that test runner, and publishes them to Qase. Finally, it marks the test run as complete (_if you’ve enabled that in your reporter configuration_).

When you run tests in parallel or in shards, multiple processes get created for the test runner, and naturally, the same happens for the reporter. Each reporter instance creates its own test run and publishes whatever results it gets from its particular test runner instance. This results in multiple runs being created in Qase.

Going back to our earlier example, if you just use the reporter as-is with default settings, you’ll end up with four different test runs for your four shards. Here’s what that looks like in Qase:

[Example workflow where multiple test runs are created in Qase](https://github.com/qase-tms/playwright-demo/blob/main/.github/workflows/qase-multiple-runs.yml)

<Image align="center" className="border" border={true} width="90% " src="https://files.readme.io/099d886256b53b2bed66b3b312b84b9fc4a47b5bcbdd702a36a67c48e4654b3e-image.png" />

<br />

#### The solution

What we really need is for all these results from each shard to be reported into a single test run. Let’s see how we can make that happen.

The root issue is that each reporter instance is creating its own test run. So if we can create a test run first and then get all reporter instances to report to that same run, we’ve solved our problem.

Here’s something useful to know about all Qase reporters: before they go ahead and creates a test run, they check if a `run_id` is already provided, either through the config file or as a value in this environment variable: `QASE_TESTOPS_RUN_ID`

If it finds a value there, it skips creating a new run and uses that particular run ID to report results into it.

What we’ll do is create a test run as a separate step before any tests actually run, before the test runner even kicks in. You could use a simple curl request to the Qase API, but for GitHub workflows, we have GitHub actions that are much more elegant to use.

<br />

Ideally, what we'll need is for all of these results from each shared to be reported to a single test run. Let's see how we can acheive that.

The root of the problem is that each reporter instance creating it's own test run. So, if we're able to first have a test run create and get all reporter instances to report to the same test run, then the problem is solved.

A detail to know about the reporter is that before going to create a test run each time, it check if a run_id is provided, either through the config file, or as an value against this environemtn variable: QASE_TESTOPS_RUN_ID

If a value is given, it skips creation of run and uses this partiuclar run id to report results into it.

What we'll do here, is create a test run as a separate prior step before any tests are run, before the tst runner kicks in. While you can use a simple curl request [https://developers.qase.io/reference/create-run#/](https://developers.qase.io/reference/create-run#/), for github workflows, we have github actions that are more elegant to use.

this workflow uses the same, for your reference: URL

It creates a test run and uses ther esulttant run id and gives it as a env varible in the step where the tests are run. There after once all tests are run, one more steps mark this test run as closed, using the same run id. 

The report in Qase looks like this. Instead of 4 separate test runs, we get a single test run that has all ther esults.

<Image align="center" className="border" border={true} width="90% " src="https://files.readme.io/72aaf66a6023e59aca114821009de285ea37ede738960ea80e36b23e2dbe1304-image.png" />

<Image align="center" width="90% " src="https://files.readme.io/2c532c346c9f819b578d973b900899eca5baa12a3319875f9cde5120537032c3-image.png" />

<br />

One thing to keep in mind. One more reporter option QASE_TESTOPS_RUN_COMPLETE will need to be set to false, what this option does is ti marks the test run as complete once all the results are sent in. Because we have multiple instances of test runner, each will try to mark the test run as complete as soon as it finishes reporting. We don't want that as it gives us a wrong run end time. We're anyway using a dedicaed step to mark it as complete. So, make sure this particular option is not set to true. if you're not using this option then it's okay, because the default value for this is false.

<br />

Reference: 

All workflows used in this article: https://github.com/qase-tms/playwright-demo/tree/main/.github/workflows

The branch 'docs/sharding' in this repo contains an example test, you an try running: https://github.com/qase-tms/playwright-demo/tree/docs/sharding

Github actions for Qase endpoints: https://github.com/qase-tms/gh-actions
