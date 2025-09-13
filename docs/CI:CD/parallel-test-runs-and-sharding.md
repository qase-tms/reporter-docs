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
As test suites grow larger, execution time often becomes a challenge. Running hundreds of tests one after another can slow down feedback cycles, which is why many teams adopt parallel execution or sharding. These approaches distribute tests across multiple workers or shards, allowing them to run at the same time and finish faster.

This article uses Playwright as the example, but the same steps can be applied to almost all other reporters.

Parallel execution can be set up in a few different ways, depending on the environment and tooling:

* **Parallel workers** – Tests are divided across multiple workers (threads or processes) on the same machine, each running a portion of the suite at the same time.
* **Sharding** – The test suite is split into shards, with each shard running on a different machine or container. This is especially useful in CI/CD pipelines where cloud infrastructure can spin up multiple runners in parallel.

Here's an example of a workflow where sharing is used, with a usual HTML reporter in playwright: tests are distrbuted across 4 shards and all the individual reports are then merged into a single consolidated report at the end.

***

**Problem**: When tests are executed in parallel or through sharding, a common issue arises during reporting to Qase, instead of all results being grouped into a single test run, multiple runs are created, one for each shard or worker.

**The reporter works like this** – for each test runner instance that is executed as a process, the reporter works in tandem, to first create a test run in Qase, package the results it receives from the test runner to publish them to Qase. Finally, marks the test run as complete (_if enabled in your reporter configuration)_.

When tests are run in parallel, or in shards, multiple process are created for the test runner and so it follows that for ther reporter too. Each reporter instances creates it's own test run and publishes whatever results it gets from that particular instance of test runner. This results in multiple runs. 

Continuing on the previous example, if you were to simply use the reporter as it is, with default, what you'll get is four different test runs for four shards. Here's how it looks like, in Qase: 

Here's the example of a workflow that simply uses the reporter without any additional configuration:

<br />

<Image align="center" className="border" border={true} width="90% " src="https://files.readme.io/099d886256b53b2bed66b3b312b84b9fc4a47b5bcbdd702a36a67c48e4654b3e-image.png" />

Ideally, what we'll need is for all of these results from each shared to be reported to a single test run. Let's see how we can acheive that.

The root of the problem is that each reporter instance creating it's own test run. So, if we're able to first have a test run create and get all reporter instances to report to the same test run, then the problem is solved.

A detail to know about the reporter is that before going to create a test run each time, it check if a run_id is provided, either through the config file, or as an value against this environemtn variable: QASE_TESTOPS_RUN_ID

If a value is given, it skips creation of run and uses this partiuclar run id to report results into it. 

What we'll do here, is create a test run as a separate prior step before any tests are run, before the tst runner kicks in. While you can use a simple curl request https://developers.qase.io/reference/create-run#/

<br />

**Create a new test run** – The first step is to create a new test run in Qase. If run creation fails for any reason, the reporter checks for any fallback configuration. If no fallback is defined, the process will fail, and results will not be reported.

1. **Package the test results** – Once the run is successfully created, the reporter gathers all test results from the executed tests.
2. **Format results for the API** – The results are then structured into the proper API payload format, ensuring that Qase can correctly interpret and store the test outcomes.
3. **Send results to Qase** – Finally, the formatted results are sent to Qase through the API, linking each test result to the corresponding test case in the system.

By default, the reporter automatically creates the test run and marks it as complete once results are sent. This default behavior can be modified with the following variables:

* If `QASE_TESTOPS_RUN_COMPLETE` is set to `False`, the test run will stay in progress and won't be marked as complete automatically.
* If `QASE_TESTOPS_RUN_ID` is already set, the reporter will use the existing run ID instead of creating a new run, ensuring all results go into the same test run.

***

## Handling parallel execution and sharding

When running tests in parallel or using sharding, the default reporter setup can create separate test runs for each parallel process or shard. To ensure all results are reported to a single run in Qase, a slightly different approach is needed:

1. **Create a new test run** – Before starting the parallel or sharded tests, create a new run in Qase and obtain its run ID.
2. **Set the run ID in the reporter configuration** – Pass the obtained `QASE_TESTOPS_RUN_ID` to each parallel process or shard, so that all results are linked to the same run.
3. **Turn off auto-complete** – Set `QASE_TESTOPS_RUN_COMPLETE` to `False` during this phase. This ensures the run remains open while multiple processes are writing results.
4. **Execute parallel/sharded tests** – Run the tests in parallel or across shards. Each process/shard will send its results to the same run in Qase.
5. **Close the run** – Once all parallel processes or shards have finished and reported their results, run a separate job to mark the test run as complete.

<br />

### Using GitHub Actions

If you are using GitHub Actions, there’s a Qase Action that makes managing test runs straightforward. With this action, you can:

* **Create a new test run** at the start of your workflow and obtain a run ID. Refer to the [Create Run documentation](https://github.com/qase-tms/gh-actions/tree/main/run-create) for step-by-step instructions.
* **Complete the test run** once all jobs have reported their results. See the [Complete Run documentation](https://github.com/qase-tms/gh-actions/tree/main/run-complete) for guidance on completing a run.

<br />
