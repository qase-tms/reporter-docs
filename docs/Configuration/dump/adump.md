---
title: '[a]Dump'
deprecated: false
hidden: true
metadata:
  robots: index
---
# Quick Start

## Choosing a Qase Reporter

All Qase reporters are open source and maintained on our [GitHub](https://github.com/qase-tms) page.

Start there and select the reporter that matches your setup. The right choice depends on your project’s programming language and the test runner you’re using.

1. Identify the language repository — Find the repository that corresponds to your project’s stack.
2. Locate the framework package — Within that repository, navigate to the package for your specific test runner.
3. Follow the documentation — Each package includes installation and configuration instructions tailored to that framework.

***

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

## Jump to your framework

**JavaScript**  
[Playwright](#playwright) · [Cypress](#cypress) · [Jest](#jest) · [Mocha](#mocha) · [WebdriverIO](#webdriverio) · [Newman](#newman) · [CucumberJS](#cucumberjs)

**Python**  
[Pytest](#pytest) · [Robot Framework](#robot-framework) · [Behave](#behave) · [Tavern](#tavern)

**Java**  
[JUnit 5](#junit-5) · [JUnit 4](#junit-4) · [TestNG](#testng) · [Cucumber](#cucumber-java)

***

## JavaScript

### Playwright

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

### Cypress

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

### Jest

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

### Mocha

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

### WebdriverIO

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

### Newman

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

### CucumberJS

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

## Python

### Pytest

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

### Robot Framework

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

### Behave

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

### Tavern

⚑ FLAG: install and run command not confirmed.

**2. Set your environment variables**

(Same as above)

[↑ Back to top](#quick-start)

***

## Java

### JUnit 5

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

### JUnit 4

(Same structure)

[↑ Back to top](#quick-start)

***

### TestNG

(Same structure)

[↑ Back to top](#quick-start)

***

### Cucumber (Java)

(Same structure)

[↑ Back to top](#quick-start)

***

# Configuration

* Reporter modes: testops, report, and off
* Complete configuration reference
* Enriching results: metadata, fields & attachments

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

# Scenarios & Advanced

* Using reporters in CI/CD pipelines
* Parallel execution and sharding
* Combining automated and manual tests
* Linking tests to existing Qase test cases
* Using the Qase CLI (qasectl)
* Report mode and local reporting

# Troubleshooting

* Empty runs and missing results
* Debugging reporter issues
* Error code reference
