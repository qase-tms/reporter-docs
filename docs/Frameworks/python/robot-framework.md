---
title: Robot Framework
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
**Robot Framework** is a generic test automation framework for acceptance testing and acceptance test-driven development. It is a keyword-driven testing framework that uses tabular test data syntax.

With `qase-robotframework` you can publish the results of your automated test runs to your Qase project using the public API endpoints.

<br />

## Installation

To integrate Qase into your project, follow these steps:

<br />

### Activate the Robot Framework App

To activate the app, go to the ‘[Apps](https://app.qase.io/apps?app=robotframework-reporter)’ section in your workspace, and click on ‘**Activate**’.  
Switch to the ‘Access tokens’ tab, and create a new API token from here. Save the API token, as we’ll need it in the following steps.

<br />

### Add qase-robotframework to your project

To install the reporter, run the following command in a virtual environment 🔗:

```
pip install qase-robotframework
```

<br />

### Add a configuration file for the reporter

At the very least, the reporter will need two variables defined – your Robot Framework App’s Token, and the Qase Project you want to publish the results to:

Ensure that this file is placed in the root of your project.

```json JSON
{
  "debug": false,
  "testops": {
    "api": {
      "token": "<app-token>"
    },
    "project": "<project-code>"
  }
}
```

Please refer to this [article](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code) for guidance on how to find your Project code in Qase.

<br />

### Verify the integration

Let’s verify the integration, we’ll use a simple test to check if the results are being published to your Qase project. Create a `tests/ directory` in the root of your project and add the following test:

```python Python
Simple Addition Test  
    ${result}=    Evaluate    1 + 1  
    Should Be Equal    ${result}    2
```

<br />

Now, let’s run the test by executing the following command:

`QASE_MODE=testops robot --listener qase.robotframework.Listener ./tests/test_addition.robot`

In the above command, we’re setting the reporter’s mode to `testops` using the Environment variable `QASE_MODE`. You can learn more about other reporter modes here.

Click on the link printed by `qase-robotframework` to go to the test run in Qase.