---
title: Dump
deprecated: false
hidden: true
metadata:
  robots: index
---
<br />

# Complete Configuration Options Reference

## Platform Availability Legend

| Symbol  | Meaning                                                                                   |
| ------- | ----------------------------------------------------------------------------------------- |
| **JS**  | JavaScript (Playwright, Cypress, Jest, Mocha, WDIO, CucumberJS, Newman, TestCafe, Vitest) |
| **PY**  | Python (pytest, Behave, Robot Framework, Tavern)                                          |
| **JV**  | Java (JUnit 4/5, TestNG, Cucumber 3–7)                                                    |
| **CS**  | C# (xUnit v2/v3, NUnit, MSTest)                                                           |
| **PHP** | PHP (PHPUnit, Pest, Codeception)                                                          |
| **GO**  | Go (stdlib `testing`)                                                                     |

***

## Group 1: Reporter Lifecycle — _Controls whether and how the reporter operates_

These options determine the reporter's operating mode, fallback behavior, and diagnostic output. They don't affect what data is sent or how it's processed — they control the reporter itself.

| #  | Option              | Config Key        | Env Var                | Default                      | Platforms | Notes                                                                                            |                                                                   |
| :- | :------------------ | :---------------- | :--------------------- | :--------------------------- | :-------- | :----------------------------------------------------------------------------------------------- | :---------------------------------------------------------------- |
| 1  | **Mode**            | `mode`            | `QASE_MODE`            | `off`                        | All       | `testops`, `report`, `off`. JS/PY also support `testops_multi`.                                  |                                                                   |
| 2  | **Fallback mode**   | `fallback`        | `QASE_FALLBACK`        | `off`                        | All       | Activated when primary mode fails (auth error, network error). Go only allows `report` or `off`. |                                                                   |
| 3  | **Debug logging**   | `debug`           | `QASE_DEBUG`           | `false`                      | All       | Enables verbose logging of payloads, API calls, config.                                          |                                                                   |
| 4  | **Console logging** | `logging.console` | `QASE_LOGGING_CONSOLE` | `true`                       | All       | Enable/disable reporter log output to console.                                                   |                                                                   |
| 5  | **File logging**    | `logging.file`    | `QASE_LOGGING_FILE`    | `false` (or same as `debug`) | All       | Enable/disable writing logs to `<project-root>/logs/`. PHP defaults to `true`.                   | [6-cite-0](#6-cite-0) [6-cite-1](#6-cite-1) [6-cite-2](#6-cite-2) |

***

## Group 2: API Connection — _Controls how the reporter connects to Qase_

These options establish the connection to the Qase API. Without these, `testops` mode cannot function.

| #  | Option                    | Config Key                   | Env Var                            | Default                  | Platforms       | Notes                                   |                                             |
| :- | :------------------------ | :--------------------------- | :--------------------------------- | :----------------------- | :-------------- | :-------------------------------------- | :------------------------------------------ |
| 6  | **API token**             | `testops.api.token`          | `QASE_TESTOPS_API_TOKEN`           | —                        | All             | **Required** for `testops` mode.        |                                             |
| 7  | **API host**              | `testops.api.host`           | `QASE_TESTOPS_API_HOST`            | `qase.io`                | All             | For enterprise/self-hosted instances.   |                                             |
| 8  | **Enterprise flag**       | `testops.api.enterprise`     | `QASE_TESTOPS_API_ENTERPRISE`      | `false`                  | JS, JV, CS, PHP | Affects URL construction. Not in Go/PY. |                                             |
| 9  | **API timeout (seconds)** | `testops.api.timeoutSeconds` | `QASE_TESTOPS_API_TIMEOUT_SECONDS` | `0` (OkHttp default 10s) | **JV only**     | HTTP connect/read/write timeout.        | [6-cite-3](#6-cite-3) [6-cite-4](#6-cite-4) |

***

## Group 3: Test Run Management — _Controls the test run created in Qase_

These options define the test run that results are reported into. They affect what the user sees in the Qase UI at the run level, not at the individual result level.

| #  | Option                      | Config Key                                 | Env Var                                            | Default                     | Platforms           | Notes                                                                                |                                                                                         |
| :- | :-------------------------- | :----------------------------------------- | :------------------------------------------------- | :-------------------------- | :------------------ | :----------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------- |
| 10 | **Project code**            | `testops.project`                          | `QASE_TESTOPS_PROJECT`                             | —                           | All                 | **Required** for `testops` mode.                                                     |                                                                                         |
| 11 | **Run ID**                  | `testops.run.id`                           | `QASE_TESTOPS_RUN_ID`                              | —                           | All                 | If set, results go to an existing run. **Required** in Go (no auto-create).          |                                                                                         |
| 12 | **Run title**               | `testops.run.title`                        | `QASE_TESTOPS_RUN_TITLE`                           | `Automated run <date>`      | JS, PY, JV, CS, PHP | Not in Go (no auto-create).                                                          |                                                                                         |
| 13 | **Run description**         | `testops.run.description`                  | `QASE_TESTOPS_RUN_DESCRIPTION`                     | `<Framework> automated run` | JS, PY, JV, CS, PHP | Not in Go.                                                                           |                                                                                         |
| 14 | **Auto-complete run**       | `testops.run.complete`                     | `QASE_TESTOPS_RUN_COMPLETE`                        | `true`                      | JS, PY, JV, CS, PHP | Set to `false` for Test Plan workflows (manual testers complete the run). Not in Go. |                                                                                         |
| 15 | **Run tags**                | `testops.run.tags`                         | `QASE_TESTOPS_RUN_TAGS`                            | `[]`                        | JS, PY, JV, CS, PHP | Comma-separated in env var. Not in Go.                                               |                                                                                         |
| 16 | **External link type**      | `testops.run.externalLink.type`            | `QASE_TESTOPS_RUN_EXTERNAL_LINK_TYPE`              | —                           | JS, PY, JV, CS, PHP | `jiraCloud` or `jiraServer`. Not in Go.                                              |                                                                                         |
| 17 | **External link URL**       | `testops.run.externalLink.link`            | `QASE_TESTOPS_RUN_EXTERNAL_LINK` / `_URL`          | —                           | JS, PY, JV, CS, PHP | Env var name varies: JS uses `_LINK`, PY uses `_URL`, CS uses `_URL`. Not in Go.     |                                                                                         |
| 18 | **Plan ID**                 | `testops.plan.id`                          | `QASE_TESTOPS_PLAN_ID`                             | —                           | JS, PY, JV, CS, PHP | Creates run from a Test Plan. Not in Go.                                             |                                                                                         |
| 19 | **Configuration values**    | `testops.configurations.values`            | `QASE_TESTOPS_CONFIGURATIONS_VALUES`               | `[]`                        | JS, PY, JV, CS, PHP | Format: `group=value,group2=value2`. Not in Go.                                      |                                                                                         |
| 20 | **Create configurations**   | `testops.configurations.createIfNotExists` | `QASE_TESTOPS_CONFIGURATIONS_CREATE_IF_NOT_EXISTS` | `false`                     | JS, PY, JV, CS, PHP | Auto-create config groups/values if missing. Not in Go.                              |                                                                                         |
| 21 | **Show public report link** | `testops.showPublicReportLink`             | `QASE_TESTOPS_SHOW_PUBLIC_REPORT_LINK`             | `false`                     | JS, PY, JV, CS, PHP | Enables public URL and logs it after run completion. Not in Go.                      | [6-cite-5](#6-cite-5) [6-cite-6](#6-cite-6) [6-cite-7](#6-cite-7) [6-cite-8](#6-cite-8) |

**Note on Go**: Go does not auto-create runs. Options 12–21 are irrelevant because the run must be pre-created via `qasectl` or the API. Only `run.id` is used. [6-cite-9](#6-cite-9)

**Note on external link env var inconsistency**: The env var for the external link URL differs across platforms:

* JS: `QASE_TESTOPS_RUN_EXTERNAL_LINK` (JSON object in env)
* PY: `QASE_TESTOPS_RUN_EXTERNAL_LINK_TYPE` + `QASE_TESTOPS_RUN_EXTERNAL_LINK_URL` (two separate vars)
* JV: `QASE_TESTOPS_RUN_EXTERNAL_LINK_TYPE` + `QASE_TESTOPS_RUN_EXTERNAL_LINK` (two separate vars)
* CS: `QASE_TESTOPS_RUN_EXTERNAL_LINK_TYPE` + `QASE_TESTOPS_RUN_EXTERNAL_LINK_URL` (two separate vars)
* PHP: `QASE_TESTOPS_RUN_EXTERNAL_LINK_TYPE` + `QASE_TESTOPS_RUN_EXTERNAL_LINK_URL` (two separate vars) [6-cite-10](#6-cite-10) [6-cite-11](#6-cite-11)

***

## Group 4: Result Delivery — _Controls how results are batched and sent to the API_

These options affect the mechanics of uploading results — batch sizes, timeouts, threading, and whether attachments are included.

| #  | Option                       | Config Key                    | Env Var                             | Default                             | Platforms   | Notes                                                                                 |                                                                         |
| :- | :--------------------------- | :---------------------------- | :---------------------------------- | :---------------------------------- | :---------- | :------------------------------------------------------------------------------------ | :---------------------------------------------------------------------- |
| 22 | **Batch size**               | `testops.batch.size`          | `QASE_TESTOPS_BATCH_SIZE`           | 200 (JS/PY/JV/CS/PHP), **100** (Go) | All         | Java validates: 1–2000, rejects out-of-range with warning. Others have no validation. |                                                                         |
| 23 | **Upload timeout (seconds)** | `testops.batch.uploadTimeout` | `QASE_TESTOPS_BATCH_UPLOAD_TIMEOUT` | `300`                               | **JV only** | Minimum timeout; scales up with attachment size at 1 MB/s.                            |                                                                         |
| 24 | **Upload threads**           | `testops.batch.uploadThreads` | `QASE_TESTOPS_BATCH_UPLOAD_THREADS` | `4`                                 | **JV only** | Parallel threads for attachment upload. Range: 1–32.                                  |                                                                         |
| 25 | **Upload attachments**       | `testops.uploadAttachments`   | `QASE_TESTOPS_UPLOAD_ATTACHMENTS`   | `true`                              | **JS only** | When `false`, skips all attachment uploads. Other platforms always upload.            |                                                                         |
| 26 | **Create defects**           | `testops.defect`              | `QASE_TESTOPS_DEFECT`               | `false`                             | All         | Auto-create defects for failed test cases in Qase.                                    | [6-cite-12](#6-cite-12) [6-cite-13](#6-cite-13) [6-cite-14](#6-cite-14) |

***

## Group 5: Result Transformation — _Controls how results are modified before sending_

These options transform the test results between the test framework and the API. They change what the result looks like, not where it goes.

| #  | Option                 | Config Key             | Env Var                      | Default | Platforms   | Notes                                                                                                           |                                                 |
| :- | :--------------------- | :--------------------- | :--------------------------- | :------ | :---------- | :-------------------------------------------------------------------------------------------------------------- | :---------------------------------------------- |
| 27 | **Status mapping**     | `statusMapping`        | `QASE_STATUS_MAPPING`        | —       | All         | Remap statuses: e.g., `{"invalid": "failed"}`. Applied before status filter. Applied to both results and steps. |                                                 |
| 28 | **Status filter**      | `testops.statusFilter` | `QASE_TESTOPS_STATUS_FILTER` | `[]`    | All         | Exclude results with specified statuses from being sent. Applied after status mapping.                          |                                                 |
| 29 | **Exclude parameters** | `excludeParams`        | `QASE_EXCLUDE_PARAMS`        | `[]`    | **PY only** | Comma-separated list of parameter names to strip from results.                                                  | [6-cite-15](#6-cite-15) [6-cite-16](#6-cite-16) |

***

## Group 6: Result Enrichment — _Controls what metadata is attached to each result_

These options add contextual data to results that affects how they appear in Qase and how they're matched to test cases.

| #  | Option                              | Config Key                      | Env Var                               | Default                            | Platforms   | Notes                                                                                                                                                |                                                                         |
| :- | :---------------------------------- | :------------------------------ | :------------------------------------ | :--------------------------------- | :---------- | :--------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------- |
| 30 | **Environment**                     | `environment`                   | `QASE_ENVIRONMENT`                    | —                                  | All         | Environment slug from Qase. Attached to the test run.                                                                                                |                                                                         |
| 31 | **Root suite**                      | `rootSuite`                     | `QASE_ROOT_SUITE`                     | —                                  | All         | Prepended to all suite paths. Affects test case matching/creation.                                                                                   |                                                                         |
| 32 | **Capture logs**                    | `captureLogs`                   | `QASE_CAPTURE_LOGS`                   | `false`                            | JS, GO      | Captures stdout/stderr as attachments. **Note**: JS README has a bug — config key is listed as `testops.defect` but the actual key is `captureLogs`. |                                                                         |
| 33 | **Profilers**                       | `profilers`                     | `QASE_PROFILERS`                      | `[]`                               | JS, PY      | Enable profilers (e.g., `["network"]`).                                                                                                              |                                                                         |
| 34 | **Network profiler: skip domains**  | `networkProfiler.skip_domains`  | —                                     | `[]`                               | **JS only** | Domains to exclude from network profiling.                                                                                                           |                                                                         |
| 35 | **Network profiler: track on fail** | `networkProfiler.track_on_fail` | —                                     | —                                  | **JS only** | Only capture network requests for failed tests.                                                                                                      |                                                                         |
| 36 | **Network profiler: exclude hosts** | (nested in profilers config)    | `QASE_PROFILER_NETWORK_EXCLUDE_HOSTS` | `[]`                               | **PY only** | Hosts to exclude from network profiling.                                                                                                             |                                                                         |
| 37 | **Execution plan path**             | `executionPlan.path`            | `QASE_EXECUTION_PLAN_PATH`            | `./build/qase-execution-plan.json` | **PY only** | Path to local execution plan file. Note: loading from local file is not yet implemented; plans are loaded from API via `plan.id`.                    | [6-cite-17](#6-cite-17) [6-cite-18](#6-cite-18) [6-cite-19](#6-cite-19) |

***

## Group 7: Local Report Mode — _Controls file-based report output_

These options apply when `mode` is `report` (or when the reporter falls back to report mode).

| #  | Option            | Config Key                 | Env Var                         | Default               | Platforms | Notes                                          |                         |
| :- | :---------------- | :------------------------- | :------------------------------ | :-------------------- | :-------- | :--------------------------------------------- | :---------------------- |
| 38 | **Report driver** | `report.driver`            | `QASE_REPORT_DRIVER`            | `local`               | All       | Only `local` is supported.                     |                         |
| 39 | **Report path**   | `report.connection.path`   | `QASE_REPORT_CONNECTION_PATH`   | `./build/qase-report` | All       | Directory where JSON report files are written. |                         |
| 40 | **Report format** | `report.connection.format` | `QASE_REPORT_CONNECTION_FORMAT` | `json`                | All       | `json` or `jsonp`.                             | [6-cite-20](#6-cite-20) |

***

## Group 8: Multi-Project Mode — _Controls reporting to multiple Qase projects simultaneously_

Available only in JS and PY. These options are used when `mode` is `testops_multi`.

| #  | Option                      | Config Key                                  | Env Var                                              | Default              | Platforms       | Notes                                           |                                                 |
| :- | :-------------------------- | :------------------------------------------ | :--------------------------------------------------- | :------------------- | :-------------- | :---------------------------------------------- | :---------------------------------------------- |
| 41 | **Default project**         | `testops_multi.default_project`             | `QASE_TESTOPS_MULTI_DEFAULT_PROJECT` (PY) / N/A (JS) | First project        | **JS, PY only** | Tests without explicit project mapping go here. |                                                 |
| 42 | **Projects array**          | `testops_multi.projects`                    | —                                                    | `[]`                 | **JS, PY only** | Array of project configs. Config file only.     |                                                 |
| 43 | **Project code**            | `testops_multi.projects[].code`             | —                                                    | —                    | **JS, PY only** | Required per project.                           |                                                 |
| 44 | **Project run title**       | `testops_multi.projects[].run.title`        | —                                                    | Auto-generated       | **JS, PY only** |                                                 |                                                 |
| 45 | **Project run description** | `testops_multi.projects[].run.description`  | —                                                    | Auto-generated       | **JS, PY only** |                                                 |                                                 |
| 46 | **Project run complete**    | `testops_multi.projects[].run.complete`     | —                                                    | `true`               | **JS, PY only** |                                                 |                                                 |
| 47 | **Project run ID**          | `testops_multi.projects[].run.id`           | —                                                    | —                    | **JS, PY only** | Use existing run.                               |                                                 |
| 48 | **Project run tags**        | `testops_multi.projects[].run.tags`         | —                                                    | `[]`                 | **JS, PY only** |                                                 |                                                 |
| 49 | **Project external link**   | `testops_multi.projects[].run.externalLink` | —                                                    | —                    | **JS, PY only** |                                                 |                                                 |
| 50 | **Project plan ID**         | `testops_multi.projects[].plan.id`          | —                                                    | —                    | **JS, PY only** |                                                 |                                                 |
| 51 | **Project environment**     | `testops_multi.projects[].environment`      | —                                                    | Global `environment` | **JS, PY only** |                                                 | [6-cite-21](#6-cite-21) [6-cite-22](#6-cite-22) |

***

## Group 9: Framework-Specific Options — _Options unique to specific test frameworks_

### JavaScript Frameworks

| #  | Option                                   | Config Key                            | Frameworks | Default               | Notes                                                       |                                                 |
| :- | :--------------------------------------- | :------------------------------------ | :--------- | :-------------------- | :---------------------------------------------------------- | :---------------------------------------------- |
| 52 | **Playwright: add browser as parameter** | `framework.browser.addAsParameter`    | Playwright | `false`               | Adds Playwright project name (browser) as a test parameter. |                                                 |
| 53 | **Playwright: browser parameter name**   | `framework.browser.parameterName`     | Playwright | `"browser"`           | Custom name for the browser parameter.                      |                                                 |
| 54 | **Playwright: mark as flaky**            | `framework.markAsFlaky`               | Playwright | `false`               | Mark tests as flaky if they passed after retries.           |                                                 |
| 55 | **Cypress: screenshots folder**          | `framework.cypress.screenshotsFolder` | Cypress    | `cypress/screenshots` | Path to Cypress screenshots directory.                      |                                                 |
| 56 | **Cypress: videos folder**               | `framework.cypress.videosFolder`      | Cypress    | `cypress/videos`      | Path to Cypress videos directory.                           | [6-cite-23](#6-cite-23) [6-cite-24](#6-cite-24) |

### Python Frameworks

| #  | Option                            | Config Key                           | Env Var                          | CLI                                | Frameworks          | Default        | Notes                                                  |                                                 |
| :- | :-------------------------------- | :----------------------------------- | :------------------------------- | :--------------------------------- | :------------------ | :------------- | :----------------------------------------------------- | :---------------------------------------------- |
| 57 | **pytest: capture logs**          | `framework.pytest.captureLogs`       | `QASE_PYTEST_CAPTURE_LOGS`       | `--qase-pytest-capture-logs`       | pytest              | `false`        | Capture pytest log/stdout/stderr as attachments.       |                                                 |
| 58 | **pytest: xfail status (failed)** | `framework.pytest.xfailStatus.xfail` | `QASE_PYTEST_XFAIL_STATUS_XFAIL` | `--qase-pytest-xfail-status-xfail` | pytest              | `skipped`      | Status slug for xfail-marked tests that fail.          |                                                 |
| 59 | **pytest: xfail status (passed)** | `framework.pytest.xfailStatus.xpass` | `QASE_PYTEST_XFAIL_STATUS_XPASS` | `--qase-pytest-xfail-status-xpass` | pytest              | `passed`       | Status slug for xfail-marked tests that pass.          |                                                 |
| 60 | **Playwright video**              | (from CLI `--video`)                 | —                                | `--video`                          | pytest (Playwright) | `off`          | `on`, `retain-on-failure`, `off`. Auto-attaches video. |                                                 |
| 61 | **Playwright trace**              | (from CLI `--tracing`)               | —                                | `--tracing`                        | pytest (Playwright) | `off`          | `on`, `retain-on-failure`, `off`. Auto-attaches trace. |                                                 |
| 62 | **Playwright output dir**         | (from CLI `--output`)                | —                                | `--output`                         | pytest (Playwright) | `test-results` | Directory for Playwright artifacts.                    | [6-cite-25](#6-cite-25) [6-cite-26](#6-cite-26) |

***

## Configuration Priority Order (per platform)

| Platform | Priority (highest → lowest)                                                                                  |                                                                         |
| :------- | :----------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------- |
| **JS**   | Framework config (playwright.config.ts / cypress.config.js / jest.config.js) → Env vars → `qase.config.json` |                                                                         |
| **PY**   | CLI options (pytest/tavern only) → Env vars → `qase.config.json`                                             |                                                                         |
| **JV**   | System properties (`-DQASE_*`) → Env vars → `qase.config.json`                                               |                                                                         |
| **CS**   | System properties → Env vars → `qase.config.json`                                                            |                                                                         |
| **PHP**  | Env vars → `qase.config.json`                                                                                |                                                                         |
| **GO**   | Env vars → `qase.config.json` (searches CWD + parent dirs up to 5 levels)                                    | [6-cite-27](#6-cite-27) [6-cite-28](#6-cite-28) [6-cite-29](#6-cite-29) |

***

## Cross-Platform Availability Matrix (Summary)

| Option Group                   | JS  | PY  | JV           | CS  | PHP | GO         |
| ------------------------------ | --- | --- | ------------ | --- | --- | ---------- |
| Reporter Lifecycle (1–5)       | All | All | All          | All | All | All        |
| API Connection (6–8)           | All | 6–7 | All          | All | All | 6–7        |
| API Timeout (9)                | —   | —   | Yes          | —   | —   | —          |
| Run Management (10–21)         | All | All | All          | All | All | 10–11 only |
| Batch Size (22)                | 200 | 200 | 200 (1–2000) | 200 | 200 | 100        |
| Upload Timeout/Threads (23–24) | —   | —   | Yes          | —   | —   | —          |
| Upload Attachments toggle (25) | Yes | —   | —            | —   | —   | —          |
| Result Transformation (27–28)  | All | All | All          | All | All | All        |
| Exclude Params (29)            | —   | Yes | —            | —   | —   | —          |
| Capture Logs (32)              | Yes | —   | —            | —   | —   | Yes        |
| Profilers/Network (33–37)      | Yes | Yes | —            | —   | —   | —          |
| Execution Plan Path (37)       | —   | Yes | —            | —   | —   | —          |
| Multi-Project (41–51)          | Yes | Yes | —            | —   | —   | —          |
| Framework-Specific (52–62)     | Yes | Yes | —            | —   | —   | —          |

***

## Notable Inconsistencies Found

1. **`captureLogs` config key bug in JS README**: The JS commons README maps `captureLogs` to config key `testops.defect` — this is a documentation error. The actual config key is `captureLogs` at the root level. [6-cite-30](#6-cite-30)

2. **External link env var naming**: Three different patterns across platforms (see Group 3, option 17).

3. **Batch size default**: Go defaults to 100, all others to 200. [6-cite-13](#6-cite-13)

4. **`logging.file` default**: PHP defaults to `true`, all others default to `false` (or same as `debug`). [6-cite-31](#6-cite-31)

5. **Go's config file search**: Go searches parent directories up to 5 levels deep. All other platforms only check CWD. [6-cite-32](#6-cite-32)

6. **`executionPlan.path` in Python**: Documented and configurable, but loading from a local file path is explicitly noted as "not yet implemented" in source. Plans are loaded from the API via `plan.id`. [6-cite-33](#6-cite-33)
