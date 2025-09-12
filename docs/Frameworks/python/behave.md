---
title: Behave
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
**Behave** is a Python-based behavior-driven development (BDD) testing framework. It allows writing tests in a natural language style using the Gherkin syntax, making collaboration between technical and non-technical team members easier. Behave is commonly used for end-to-end, integration, and functional testing, following the Given-When-Then format.

With `qase-behave`, you can seamlessly publish the results of your automated BDD test runs to your Qase project using the public API

<br />

## Installation

To integrate Qase into your Behave project, follow these steps:

<br />

### Activate the Behave App

To activate the app, go to the ‘[Apps](https://app.qase.io/apps?app=behave-reporter)’ section in your workspace, and click on ‘Activate’

Switch to the ‘Access tokens’ tab, and create a new API token from here. Save the API token as we’ll need it for the next steps.

<br />

### Add qase-behave to your project

To install the reporter, run the following command in a virtual environment:

`pip install qase-behave`

<br />

### Add a configuration file for the reporter

At the very least, the reporter will need two variables defined - your Behave App’s Token, and the Qase Project you want to publish the results to.

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

We’ll use a simple test to check if the results are being published to your Qase project. Create the directories **features/ **and **features/steps** in the root of your project and add the following:

`features/simple.feature`

```Text feature
Feature: Simple tests

  Scenario: Test without annotations success
    Given I have a simple test
    When I run it
    Then it should pass
```

`features/steps/steps.py`

```python Python
from behave import *

@given('I have a simple test')  
def step_impl(context):  
    pass

@when('I run it')  
def step_impl(context):  
    pass

@then('it should pass')  
def step_impl(context):  
    pass
```

Now, let’s run the test by executing the following command:

`QASE_MODE=testops behave --format=qase.behave.formatter:QaseFormatter`

In the above command, we’re setting the reporter’s mode to `testops` using the Environment variable `QASE_MODE`