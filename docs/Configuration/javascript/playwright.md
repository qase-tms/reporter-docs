---
title: Playwright
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
**Playwright** is an open-source automation framework for testing web applications across multiple browsers (Chromium, Firefox, WebKit).

It enables end-to-end testing, browser automation, and web scraping with a single API, supporting actions like navigating pages and interacting with UI elements.

Our `playwright-qase-reporter` allows you to upload the results of your automated test runs to your Qase project via the Qase's public API.

<br />

## Installation

To integrate Qase Reporter in your Playwright setup, follow these steps:

<br />

### Activate the Playwright App

To activate the app, go to the [Apps](https://app.qase.io/apps?app=playwright-reporter) section in your workspace, and click on 'Activate'.

Switch to the 'Access tokens' tab, and create a new API token from here. Save the API token as we'll need it for the next steps.

<br />

### add playwright-qase-reporter to your project

To install and add the reporter as a development dependency, run the following command in your node project [^yarn]:

```shell npm
npm install -D playwright-qase-reporter
```
```Text yarn
yarn add playwright-qase-reporter -D 
```

<br />

### add the reporter to your playwright configuration file

At the very least, the reporter will need two variables defined — your playwright app's token, and the Qase project you want to publish the results to: <br />

```typescript
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [
    [
      'playwright-qase-reporter',
      {
       /* You can define the reporter options here, or in a separate file. */
        testops: {
          api: {
            token: `<app-token>`,
          },
          project: `<project-code>`,
        },
      },
    ],
  ],
});
```

Please refer to [*this article*](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code) for guidance on how to find your Project code in Qase.

You can configure the reporter options in a separate file `qase.config.json`, placed in the *root directory of your project*.

```json
{
  "testops": {
    "api": {
      "token": "<token>"
    },
    "project": "<project_code>"
  }
}
```

<br />

### Let's verify the integration

We'll use a simple test to check if the results are being published to your Qase project. Create a `tests/` directory in the root of your project and add the following test:

```javascript
const { test, expect } = require('@playwright/test');

test.describe('Example Test Suite', () => {
  test('A simple test to check the Playwright integration', () => {
    expect(true).toBe(true);
  });
});
```

<br />

**Now, let's run the test** by executing the following command:

```Text npm
QASE_MODE=testops npx playwright test
```
```Text yarn
QASE_MODE=testops yarn playwright test
```

In the above command, we're setting the reporter's mode to 'testops' using the environment variable `QASE_MODE`.