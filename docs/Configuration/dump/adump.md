---
title: '[a]Dump'
deprecated: false
hidden: true
metadata:
  robots: index
---
# Table of Contents

* What are reporters?
* **Quick Start**
  * Choosing a Qase Reporter
  * Installation by Language (JS, Python, Java)
* **Configuration Guide**
  * Defining Settings (Env vs. JSON vs. Framework)
  * Configuration Precedence
* **Deep Dive: Configuration Layers**
  * Connection & Authentication
  * Test Run Definition
  * Result Enrichment (Environments & Mapping)
  * Artifacts (Logs, Video, Screenshots)
  * Delivery & Performance (Batching)
  * Multi-Project & Local Reporting
* **Framework-Specific Tweaks**
* **Scenarios & Troubleshooting**

<br />

***

<br />

# Quick Start

## Choosing a Qase Reporter

All Qase reporters are open source and maintained on our [GitHub](https://github.com/qase-tms) page.

Start there and select the reporter that matches your setup. The right choice depends on your project’s programming language and the test runner you’re using.

1. Identify the language repository — Find the repository that corresponds to your project’s stack.
2. Locate the framework package — Within that repository, navigate to the package for your specific test runner.
3. Follow the documentation — Each package includes installation and configuration instructions tailored to that framework.

**Notes**

To streamline development and versioning, we organize our codebases as monorepos, grouped by programming language. You’ll find pinned repositories for major languages such as JavaScript, Python, and Java.

Each language-specific monorepo typically contains:

* The Qase API client — the core library responsible for communicating with the Qase REST API
* The commons library — a shared package that implements logic common to all reporters in that language, ensuring consistent data formatting
* The reporters — framework-specific plugins (e.g., Pytest, Playwright, Cypress) that integrate with your test runner

***

## Get Your First Results into Qase

Once you have identified the appropriate reporter, you can begin pushing results to Qase.

You have a test suite that runs locally. This article gets results from that suite into Qase. Pick your framework below and follow the steps.

Before you start, make sure you have your API token and project code ready.

* API token — generate it in Profile → API Token
* Project code — found in your project (e.g. DEMO in the URL)

***

### Jump to your framework

**JavaScript** Playwright · Cypress · Jest · Mocha · WebdriverIO · Newman · CucumberJS

**Python** Pytest · Robot Framework · Behave · Tavern

**Java** JUnit 5 · JUnit 4 · TestNG · Cucumber

***

### JavaScript

#### Playwright

**1. Install**

```bash
npm install -D playwright-qase-reporter
```

**2. Set your environment variables**

```bash
export QASE_TESTOPS_API_TOKEN=your_api_token
export QASE_TESTOPS_PROJECT=your_project_code
export QASE_MODE=testops
```

```cmd
set QASE_TESTOPS_API_TOKEN=your_api_token
set QASE_TESTOPS_PROJECT=your_project_code
set QASE_MODE=testops
```

```powershell
$env:QASE_TESTOPS_API_TOKEN="your_api_token"
$env:QASE_TESTOPS_PROJECT="your_project_code"
$env:QASE_MODE="testops"
```

**3. Run your tests**

```bash
npx playwright test
```

[↑ Back to top](#quick-start)

***

#### Cypress

**1. Install**

```bash
npm install -D cypress-qase-reporter
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
npx cypress run
```

[↑ Back to top](#quick-start)

***

#### Jest

**1. Install**

```bash
npm install -D jest-qase-reporter
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
npx jest
```

[↑ Back to top](#quick-start)

***

#### Mocha

**1. Install**

```bash
npm install -D mocha-qase-reporter
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
npx mocha
```

[↑ Back to top](#quick-start)

***

#### WebdriverIO

**1. Install**

```bash
npm install -D wdio-qase-reporter
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
npx wdio run wdio.conf.js
```

[↑ Back to top](#quick-start)

***

#### Newman

**1. Install**

```bash
npm install -D newman-reporter-qase
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
newman run collection.json -r qase
```

⚑ FLAG: confirm exact Newman reporter flag syntax before publishing.

[↑ Back to top](#quick-start)

***

#### CucumberJS

**1. Install**

```bash
npm install -D cucumberjs-qase-reporter
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
npx cucumber-js
```

⚑ FLAG: confirm exact run command with reporter attached before publishing.

[↑ Back to top](#quick-start)

***

### Python

#### Pytest

**1. Install**

```bash
pip install qase-pytest
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
pytest
```

[↑ Back to top](#quick-start)

***

#### Robot Framework

**1. Install**

```bash
pip install robotframework-qase
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
robot tests/
```

⚑ FLAG: confirm whether an additional listener flag is required.

[↑ Back to top](#quick-start)

***

#### Behave

**1. Install**

```bash
pip install qase-behave
```

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
behave
```

⚑ FLAG: confirm exact run command with reporter attached.

[↑ Back to top](#quick-start)

***

#### Tavern

⚑ FLAG: install and run command not confirmed.

[↑ Back to top](#quick-start)

***

### Java

#### JUnit 5

**1. Install**

```xml
<dependency>
  <groupId>io.qase</groupId>
  <artifactId>qase-junit5-reporter</artifactId>
</dependency>
```

⚑ FLAG: confirm version.

**2. Set your environment variables**

(Same as above)

**3. Run your tests**

```bash
mvn test
```

[↑ Back to top](#quick-start)

***

#### JUnit 4

(Same structure)

[↑ Back to top](#quick-start)

***

#### TestNG

(Same structure)

[↑ Back to top](#quick-start)

***

#### Cucumber (Java)

(Same structure)

[↑ Back to top](#quick-start)

***

# Configuration

## What are reporter settings and where to define them

Reporter settings control how your test results are handled and how they appear in Qase. They define things like:

* which project your results are sent to
* what the test run is called in Qase
* whether results are sent immediately or saved locally
* whether a new run is created or an existing one is reused

Without these settings, the reporter does not know where to send data or how to behave during a run.

In the previous step, you set the minimum required values using environment variables. That was enough to get results into Qase, but it’s only one way to provide these settings.

***

### Why this matters

At the beginning, environment variables are the fastest way to get started. They work well for quick setups and CI pipelines.

As your setup grows, you may need something more structured. Environment variables are harder to track, not version-controlled, and can become inconsistent across environments.

A configuration file is a file in your project where you define reporter settings in a structured format (typically JSON). It lives alongside your code, can be committed to version control, and gives you a single place to define and review behavior.

Moving settings into a configuration file makes it easier to understand what’s configured without checking multiple environments.

Couple of ways to configure the reporter. The reporter reads settings from:

* environment variables

  Example:

  ```
  QASE_MODE=testops QASE_API_TOKEN=your_api_token QASE_PROJECT_CODE=DEMO
  ```

* a configuration file

```json
{
  "qase": {
    "mode": "testops",
    "testops": {
      "api": {
        "token": "your_api_token"
      },
      "project": "DEMO"
    }
  }
}
```

Both are supported at the same time. The difference is how you manage and organize the values.

***

### Reporter-specific configuration

In addition to environment variables and the main qase configuration, you can also define the same variables directly in your reporter configuration.

This is common when the reporter is initialized inside your test framework (for example, Playwright). In that case, you may already be passing options to the reporter, and it can be convenient to keep everything in one place.

For example, in Playwright:

```ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [
    ['playwright-qase-reporter', {
      mode: 'testops',
      testops: {
        api: {
          token: 'your_api_token'
        },
        project: 'DEMO'
      }
    }]
  ]
});
```

This configures the same values you could set using environment variables or a qase config file.

You would use reporter configuration when you want everything defined inside your test framework config, you are building a reusable setup or template, or you want configuration closer to the test runner.

For framework-specific details and more examples:

→ Reporter-specific configuration examples

***

### How the reporter reads settings

The reporter supports all three configuration sources at the same time:

* Environment variables
* Qase configuration (config file)
* Reporter configuration

If the same variable is defined in multiple places, the precedence is:

```
environment variables > qase config > reporter config
```

This means:

* environment variables always override everything else
* the qase config file overrides reporter configuration
* reporter configuration acts as the base layer

This setup lets you define defaults close to your test framework, keep shared configuration in a file, and still override values in CI when needed.

For example:

* define project in reporter config
* override it in the config file for shared environments
* override it again in CI using environment variables

***

### How Configuration Shapes Test Runs and Reporting

Qase reporter configuration defines how test results move from your test runner into a final destination, how they are grouped, and what additional data is attached along the way.

A typical flow starts with establishing a connection, then defining a test run, shaping how results appear, attaching artifacts, delivering data, and handling failures if something breaks. Each part of the configuration corresponds to one of these stages.

***

**Connection & Authentication**

This is the starting point. Before anything else can happen, the reporter needs to know where to send results and how to authenticate.

You configure this when integrating Qase for the first time. If these settings are incorrect, no results will be delivered regardless of the rest of the configuration.

```bash
QASE_MODE=testops
QASE_TESTOPS_API_TOKEN=your_token
QASE_TESTOPS_PROJECT=DEMO
```

```json
{
  "qase": {
    "mode": "testops",
    "testops": {
      "api": {
        "token": "your_token"
      },
      "project": "DEMO"
    }
  }
}
```

Optional enterprise setup:

```bash
QASE_TESTOPS_API_HOST=custom.domain
QASE_TESTOPS_API_ENTERPRISE=true
```

```json
{
  "qase": {
    "testops": {
      "api": {
        "host": "custom.domain",
        "enterprise": true
      }
    }
  }
}
```

**Behavior**

The mode setting determines whether results are sent to Qase (testops), written locally (report), or disabled entirely (off). When using TestOps, both the API token and project code are required to authenticate and route data correctly.

Enterprise users provide a custom API host and enable the enterprise flag. Configuration values can be defined in multiple places, but environment variables take precedence over file-based configuration.

**Mental model**

This controls whether results are sent anywhere and how authentication is established.

***

**Test Run Definition**

Once a connection exists, results need a destination container. In Qase, this is a test run.

This part of the configuration determines whether a new run is created automatically or an existing one is reused, and how that run appears in the UI.

**Minimal configuration**

```bash
QASE_TESTOPS_RUN_TITLE="CI Run"
QASE_TESTOPS_RUN_COMPLETE=true
```

```json
{
  "qase": {
    "testops": {
      "run": {
        "title": "CI Run",
        "complete": true
      }
    }
  }
}
```

Using an existing run:

```bash
QASE_TESTOPS_RUN_ID=123
```

```json
{
  "qase": {
    "testops": {
      "run": {
        "id": 123
      }
    }
  }
}
```

**Behavior**

If a run ID is provided, results are appended to that run. If not, a new run is created automatically in most frameworks. Some environments, such as Go, require the run ID explicitly because auto-creation is not supported.

The run title, description, and tags define how the run is presented. The complete flag determines whether the run is closed automatically after execution. Setting it to false is useful when manual and automated results need to coexist in the same run.

Providing a plan ID initializes the run from an existing test plan, which may include manual test cases. External links allow associating the run with a pull request or issue tracker. Enabling the public report link generates a shareable URL and prints it during execution.

***

**Result Structure & Enrichment**

At this stage, results are already being generated. The concern shifts to how those results appear and how they are interpreted inside Qase.

This becomes relevant when the output structure does not match expectations, such as incorrect grouping, missing environment labels, or mismatched statuses.

**Minimal configuration**

```bash
QASE_ENVIRONMENT=staging
QASE_ROOT_SUITE=Backend
```

```json
{
  "qase": {
    "environment": "staging",
    "rootSuite": "Backend"
  }
}
```

Additional mapping:

```bash
QASE_STATUS_MAPPING='{"invalid":"failed"}'
QASE_TESTOPS_DEFECT=true
```

```json
{
  "qase": {
    "statusMapping": {
      "invalid": "failed"
    },
    "testops": {
      "defect": true
    }
  }
}
```

**Behavior**

The environment value labels the run and helps separate results across environments such as staging or production. The root suite introduces a top-level grouping that can reflect application modules or services.

Status mapping allows aligning framework-specific result states with Qase statuses. Status filtering removes unwanted results entirely before they are sent.

When the defect flag is enabled, failed tests automatically generate defects. Configuration values allow attaching structured metadata such as operating system or browser. If needed, missing configuration values can be created dynamically. In Python environments, specific parameters can be excluded from test identity to avoid duplicating results.

<br />

***

**Attachments & Artifacts**

Test results often require supporting evidence such as logs, screenshots, or videos. This part determines what gets collected and uploaded.

Users typically adjust these settings when artifacts are missing or when uploads become too large.

**Minimal configuration**

```bash
QASE_TESTOPS_UPLOAD_ATTACHMENTS=true
QASE_CAPTURE_LOGS=true
```

```json
{
  "qase": {
    "captureLogs": true,
    "testops": {
      "uploadAttachments": true
    }
  }
}
```

Framework-specific example:

```json
{
  "qase": {
    "framework": {
      "cypress": {
        "screenshotsFolder": "cypress/screenshots",
        "videosFolder": "cypress/videos"
      },
      "playwright": {
        "video": "retain-on-failure",
        "trace": "on",
        "output_dir": "test-results"
      },
      "pytest": {
        "captureLogs": true
      }
    }
  }
}
```

**Behavior**

Attachment uploads can be disabled entirely. Logs can be captured from standard output and error streams and attached to results.

Framework-specific settings determine where artifacts are located and when they are generated. Cypress requires correct folder paths. Playwright controls whether videos and traces are recorded and retained. Pytest can explicitly capture logs depending on configuration.

Artifacts increase the size of the data being sent, which can affect performance and upload time.

<br />

***

**Delivery & Performance**

Once results and artifacts are prepared, they need to be transmitted. This part determines how data is batched and sent.

These settings are usually left unchanged unless there are performance issues or missing results.

**Minimal configuration**

```bash
QASE_TESTOPS_BATCH_SIZE=200
```

```json
{
  "qase": {
    "testops": {
      "batch": {
        "size": 200
      }
    }
  }
}
```

Debugging scenario:

```bash
QASE_TESTOPS_BATCH_SIZE=1
```

**Behavior**

Results are grouped into batches before being sent to the API. The batch size determines how many results are included in a single request. Reducing the batch size to one helps isolate problematic test cases.

Some environments support parallel uploads through multiple threads. Timeouts can be configured for both batch uploads and underlying HTTP requests.

***

**Debugging & Failure Handling**

When something fails, visibility becomes critical. These settings control logging and fallback behavior.

They are typically used only when diagnosing issues.

**Minimal configuration**

```bash
QASE_DEBUG=true
QASE_LOGGING_CONSOLE=true
```

```json
{
  "qase": {
    "debug": true,
    "logging": {
      "console": true,
      "file": true
    }
  }
}
```

Fallback behavior:

```bash
QASE_FALLBACK=report
```

```json
{
  "qase": {
    "fallback": "report"
  }
}
```

**Behavior**

Debug mode prints detailed information about configuration, API requests, and responses. Console logging outputs activity during execution, while file logging persists logs to disk.

Fallback behavior defines what happens when the primary reporting mode fails. Switching to report mode writes results locally instead of sending them to Qase. Disabling fallback results in silent failure.

***

**Local Reporting Mode**

In some workflows, results are not sent to Qase at all. Instead, they are written to local files.

This approach is used in offline environments or when integrating with custom pipelines.

**Minimal configuration**

```bash
QASE_MODE=report
QASE_REPORT_CONNECTION_PATH=./build/qase-report
```

```json
{
  "qase": {
    "mode": "report",
    "report": {
      "driver": "local",
      "connection": {
        "path": "./build/qase-report",
        "format": "json"
      }
    }
  }
}
```

**Behavior**

When report mode is enabled, results are stored as files instead of being sent to an API. The driver defines how reports are written, and the connection settings define where they are stored and in what format.

JSON is the default format, with JSONP available when needed.

***

**Network Profiling**

Some workflows require visibility into HTTP traffic generated during test execution.

This configuration enables capturing network activity and attaching it to results.

**Minimal configuration**

```json
{
  "qase": {
    "profilers": ["network"],
    "networkProfiler": {
      "skip_domains": [],
      "track_on_fail": false
    }
  }
}
```

**Behavior**

Profilers define which types of runtime data are captured. The network profiler records HTTP requests and responses.

Specific domains can be excluded to reduce noise. Tracking can be limited to failed tests to reduce data volume.

***

**Multi-Project Reporting**

Some test suites need to send results to multiple Qase projects.

This requires a different mode and explicit configuration for each target project.

**Minimal configuration**

```json
{
  "qase": {
    "mode": "testops_multi",
    "testops_multi": {
      "default_project": "DEMO",
      "projects": [
        {
          "project": "DEMO",
          "api": {
            "token": "token1"
          },
          "run": {
            "title": "Run A",
            "complete": true
          },
          "environment": "staging"
        }
      ]
    }
  }
}
```

**Behavior**

Each project entry defines its own connection and run behavior. The default project is used when no explicit mapping is provided.

Runs can have different titles, completion behavior, and environments per project.

***

**Framework-Specific Behavior**

Different test frameworks expose unique concepts that require explicit mapping.

This configuration adapts those concepts to Qase.

**Minimal configuration**

```json
{
  "qase": {
    "framework": {
      "playwright": {
        "browser": {
          "addAsParameter": true,
          "parameterName": "browser"
        },
        "markAsFlaky": true
      },
      "pytest": {
        "xfailStatus": {
          "xfail": "skipped",
          "xpass": "failed"
        }
      },
      "executionPlan": {
        "path": "./build/qase-execution-plan.json"
      }
    }
  }
}
```

**Behavior**

Playwright can include browser information as a test parameter and mark retried tests as flaky. Pytest allows mapping expected failures and unexpected passes to specific statuses.

Execution plans define how tests are selected, although current implementations rely on API-based plans rather than local files.

***

<br />

# Frameworks

***

# Scenarios & Advanced

* Using reporters in CI/CD pipelines
* Parallel execution and sharding
* Combining automated and manual tests
* Linking tests to existing Qase test cases
* Using the Qase CLI (qasectl)

***

# Troubleshooting

* Empty runs and missing results
* Debugging reporter issues
* Error code reference
