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

The concept applies regardless of the automation tool being used. However, when tests are executed in parallel or through sharding, a common issue arises during reporting to Qase, instead of all results being grouped into a single test run, multiple runs may be created, one for each shard or worker.

This article explains how to address that situation using Playwright as the example, and how to ensure all results are reported back into one run in Qase.

***

## Common ways to run tests in parallel

Parallel execution can be set up in a few different ways, depending on the environment and tooling:

* **Parallel workers** – Tests are divided across multiple workers (threads or processes) on the same machine, each running a portion of the suite at the same time.
* **Sharding** – The test suite is split into shards, with each shard running on a different machine or container. This is especially useful in CI/CD pipelines where cloud infrastructure can spin up multiple runners in parallel.
* **Hybrid approaches** – Some teams combine both methods, using shards to distribute the load across machines and workers within each shard to maximize usage of resources.

***

## How the reporter usually works

The Qase reporter manages sending your automated test results back to Qase in a few simple steps.

1. **Create a new test run** – The first step is to create a new test run in Qase. If run creation fails for any reason, the reporter checks for any fallback configuration. If no fallback is defined, the process will fail, and results will not be reported.
2. **Package the test results** – Once the run is successfully created, the reporter gathers all test results from the executed tests.
3. **Format results for the API** – The results are then structured into the proper API payload format, ensuring that Qase can correctly interpret and store the test outcomes.
4. **Send results to Qase** – Finally, the formatted results are sent to Qase through the API, linking each test result to the corresponding test case in the system.

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
