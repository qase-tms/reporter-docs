---
title: '[a]Dump'
deprecated: false
hidden: true
metadata:
  robots: index
---
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

## Reporter-specific configuration

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

# Frameworks

## JavaScript

* Playwright
* Cypress
* CucumberJS
* Mocha, WebDriverIO, Newman, Jest, TestCafe

## Python

* Pytest, Robot Framework, Behave, Tavern

## Java

* Cucumber (3–7), JUnit (4–5), TestNG

***

# Scenarios & Advanced

* Using reporters in CI/CD pipelines
* Parallel execution and sharding
* Combining automated and manual tests
* Linking tests to existing Qase test cases
* Using the Qase CLI (qasectl)
* Report mode and local reporting

***

# Troubleshooting

* Empty runs and missing results
* Debugging reporter issues
* Error code reference
