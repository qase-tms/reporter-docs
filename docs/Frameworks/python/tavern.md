---
title: Tavern
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
**Tavern** is a Python-based testing framework designed for testing RESTful APIs. It allows writing tests in a simple and human-readable YAML format, making it easy to define request/response flows and validate complex API interactions. Tavern is well-suited for integration and end-to-end API testing, and it supports features like parameterization, test reuse, and environment variable substitution.

With `qase-tavern`, you can seamlessly publish the results of your automated API test runs to your Qase project using the public API, enabling centralized visibility and reporting of your test execution outcomes.

<br />

## Installation

To integrate Qase into your tavern project, follow these steps:

<br />

### Activate the Tavern App

To activate the app, go to the ‘[Apps](https://app.qase.io/apps?app=tavern-reporter)’ section in your workspace, and click on ‘Activate’

Switch to the ‘Access tokens’ tab, and create a new API token from here. Save the API token as we’ll need it for the next steps.

<br />

### Add qase-tavern to your project

To install the reporter, run the following command in a virtual environment:

`pip install qase-tavern`

<br />

### Add a configuration file for the reporter

At the very least, the reporter will need two variables defined - your tavern App’s Token, and the Qase Project you want to publish the results to.

Please ensure that this file is placed in the root of your project.

`qase.config.json`:

```json JSON
{
  "testops": {
    "api": {
      "token": "<token>"
    },
    "project": "<project_code>"
  }
}
```

Please refer to this [article](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code) for guidance on how to find your Project code in Qase.

<br />

### Verify the integration

We’ll use a simple test to check if the results are being published to your Qase project. Create the directory `tests/` in the root of your project and add the following test:

`tests/test_simple.tavern.yaml`

```yaml YAML
test_name: "Simple Status Code Test"

stages:
  - name: "Check status code"
    request:
      url: "https://jsonplaceholder.typicode.com/posts/1"
      method: "GET"
    response:
      status_code: 200

```

Now, let’s run the test by executing the following command:

`QASE_MODE=testops tavern --format=qase.tavern.formatter:QaseFormatter`

In the above command, we’re setting the reporter’s mode to `testops` using the Environment variable `QASE_MODE`