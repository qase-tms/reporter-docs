---
title: TestCafe
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
TestCafe is a modern, open-source framework designed for end-to-end testing of web applications. It simplifies the testing process by eliminating the need for browser plugins or additional configuration, making it accessible and easy to use for developers of all skill levels.

TestCafe supports cross-browser testing, parallel test execution, and provides robust tools for interacting with UI elements. Its straightforward syntax and built-in features make it a popular choice for creating reliable and maintainable test suites.

With our `testcafe-qase-reporter`, you can seamlessly integrate TestCafe with Qase Test Management. This allows you to automatically upload the results of your automated test runs to your Qase project, leveraging Qase’s public API to streamline your testing workflow and improve collaboration.

<br />

## Installation

To integrate Qase Reporter in your TestCafe setup, follow these steps:

<br />

### Activate the TestCafe App

To integrate Qase Reporter in your Playwright setup, follow these steps:

To activate the app, go to the [Apps](https://app.qase.io/apps?app=testcafe-reporter) section in your workspace, and click on ‘Activate’.

Switch to the ‘Access tokens’ tab, and create a new API token from here. Save the API token as we’ll need it for the next steps.

<br />

### Add `testcafe-reporter-qase` to your project

To install and add the reporter as a development dependency, run the following command in your node project 1:

```Text npm
npm install -D testcafe-reporter-qase
```
```Text yarn
yarn add testcafe-reporter-qase -D
```

<br />

### Configuration

At the very least, the reporter will need three variables defined — the mode, your testcafe app’s token, and the Qase project you want to publish the results to.

Qase Testcafe reporter can be configured in multiple ways:

using a separate config file `qase.config.json`,\
using environment variables (they override the values from the configuration files).

You can configure the reporter options in a separate file `qase.config.json`, placed in the root directory of your project.

```json
{  
  "testops": {  
    "api": {  
      "token": "<token>"  
    },  
    "project": "\<project_code>"  
  }  
}
```

Please refer to [this article](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code) for guidance on how to find your Project code in Qase.

<br />

### Let’s verify the integration

We’ll use a simple test to check if the results are being published to your Qase project. Create a `tests/` directory in the root of your project and add the following test:

```javascript
import { test } from 'testcafe';  
import { qase } from 'testcafe-reporter-qase/qase';

fixture`Simple tests`  
.page`http://devexpress.github.io/testcafe/example/`;

test('Simple Test', async (t) => {  
    await t.expect(true).ok();  
  });
```

<br />

**Now, let’s run the test** by executing the following command:

```text npm
QASE_MODE=testops npx testcafe chrome ./tests/exampleTest.js -r spec,qase
```
```text yarn
QASE_MODE=testops yarn testcafe chrome ./tests/exampleTest.js -r spec,qase
```

In the above command, we’re setting the reporter’s mode to ‘testops’ using the environment variable `QASE_MODE.`
