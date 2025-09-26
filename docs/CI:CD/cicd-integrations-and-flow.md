---
title: CI/CD integrations general flow
deprecated: false
hidden: true
metadata:
  robots: index
---
Qase integrates with your CI/CD pipelines so you can trigger automated test runs from Qase and automatically send results back — regardless of which CI/CD platform you use. This guide explains the general flow and technical requirements. You can then adapt the examples to GitHub Actions, GitLab CI, Bitbucket Pipelines, Jenkins, or any other system.

## Prerequisites

* A Qase project with an API token.
* A CI/CD platform where you can add environment variables and edit pipeline scripts.
* A Qase reporter or CLI in your test framework.
* The Qase App installed for your chosen CI/CD platform (from the Qase Apps page: [https://app.qase.io/apps](https://app.qase.io/apps).

<Image align="center" border={false} src="https://files.readme.io/a83ad3de4281e8a65d77833a5b6cc6d34f53fcfde0c6e721904a3e1b7abfe4e8-82520.png" />

## The General Flow

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Step
      </th>

      <th>
        What Happens
      </th>

      <th>
        Why It Matters
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        1. **Install the CI/CD App in Qase**
      </td>

      <td>
        From the Qase dashboard, add the integration for your CI/CD platform.
      </td>

      <td>
        Allows Qase to trigger your pipeline and pass variables.
      </td>
    </tr>

    <tr>
      <td>
        2. **Trigger Pipeline from Qase**
      </td>

      <td>
        In Qase, start or open a test run and click **Trigger CI/CD pipeline**.
      </td>

      <td>
        Sends a request to your CI/CD platform with run context.
      </td>
    </tr>

    <tr>
      <td>
        3. **Environment Variables Passed**
      </td>

      <td>
        Qase passes `QASE_RUN_ID` and `QASE_PROJECT_CODE` to the pipeline.
      </td>

      <td>
        Identifies which run and project this pipeline belongs to.
      </td>
    </tr>

    <tr>
      <td>
        4. **Map to Reporter Variables**
      </td>

      <td>
        Your reporter expects `QASE_TESTOPS_RUN_ID` and `QASE_TESTOPS_PROJECT_CODE`.
      </td>

      <td>
        Ensures test results upload to the right run.
      </td>
    </tr>

    <tr>
      <td>
        5. **Execute Tests with a Reporter or CLI**
      </td>

      <td>
        Pipeline runs tests using Qase reporter (`qase-javascript`, `qase-pytest`, etc.) or the `qasectl` CLI.
      </td>

      <td>
        Collects and uploads results to Qase.
      </td>
    </tr>

    <tr>
      <td>
        6. **Results Return to Qase**
      </td>

      <td>
        Qase updates the test run automatically.
      </td>

      <td>
        View pass/fail, logs, screenshots, and attachments in Qase.
      </td>
    </tr>
  </tbody>
</Table>

## Mapping Environment Variables

Qase currently sends:

* QASE_RUN_ID
* QASE_PROJECT_CODE

But reporters expect:

* QASE_TESTOPS_RUN_ID
* QASE_TESTOPS_PROJECT_CODE

You must map them inside your pipeline. Syntax depends on your CI/CD tool, but conceptually it looks like this:

```yaml
# General pattern (adjust for your CI/CD platform)
export QASE_TESTOPS_RUN_ID=$QASE_RUN_ID
export QASE_TESTOPS_PROJECT_CODE=$QASE_PROJECT_CODE
```

#### Examples of where to put this:

* GitHub Actions: under `env:` block
* GitLab CI: in `variables:`
* Bitbucket Pipelines: in `script:`section
* Jenkins: in `environment` block

This mapping ensures that test results are uploaded to the correct run in Qase.

## Using the Qase Reporter / CLI

* Language SDKs: Install the Qase reporter matching your test framework (Java, JS, Python, etc.).
* CLI Option (qasectl): For teams without reporters, qasectl can upload JUnit/Allure/XCtest/Qase style reports back to Qase after a run.
* Make sure the reporter or CLI has access to your QASE_TESTOPS_API_TOKEN (keep it secret in your CI/CD platform).

<br />

### Tips & Best Practices

* **API Token Security:** Store your API token as a **secret variable** in your CI/CD tool — never in plaintext.

* **Parallel Jobs:** Each parallel job can use the same `QASE_TESTOPS_RUN_ID` if you’re splitting tests across workers.

* **Debugging:** If a run in Qase appears empty, check the following:
  * Variable names are mapped correctly.
  * The reporter is installed and configured properly.
  * Your tests actually executed and produced results.

* **Unique Run IDs:** Qase generates a new run ID each time you trigger a run. Do **not hardcode** run IDs.

<br />

Additional Resources: Qase SDKs & Reporters: <Anchor label="https://github.com/qase-tms" target="_blank" href="https://github.com/qase-tms">https\://github.com/qase-tms</Anchor>
