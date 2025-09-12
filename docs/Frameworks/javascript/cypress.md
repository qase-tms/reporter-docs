---
title: Cypress
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
Cypress is a purely JavaScript-based front-end testing tool built for the modern web. It aims to address the pain points developers or QA engineers face while testing an application. Cypress is a more developer-friendly tool that uses a unique DOM manipulation technique and operates directly in the browser.

With the cypress-qase-reporter, you can publish the results of your automated test runs to your Qase project using the public API.

<br />

## Installation

To integrate Qase Reporter in your Cypress setup, follow these steps:

```text npm
npm install -D cypress-qase-reporter
```

<br />

### Activate the Cypress App

To activate the app, go to the [Apps](https://app.qase.io/apps?app=cypress-reporter) section in your workspace, and click on ‘Activate’.

Switch to the ‘Access tokens’ tab, and create a new API token from here. Save the API token as we’ll need it for the next steps.

<br />

### Add cypress-qase-reporter to your project

To install and add the reporter as a development dependency, run the following in your node project.

```Text npm
npm install -D cypress-qase-reporter
```
```Text yarn
yarn add cypress-qase-reporter -D
```

<br />

## Add the reporter to your Cypress configuration file

At the very least, the reporter will need two variables defined - your Cypress App’s Token, and the Qase Project you want to publish the results to.

```javascript
const { defineConfig } = require('cypress');

module.exports = defineConfig({  
    reporter: 'cypress-multi-reporters',  
    reporterOptions: {  
        reporterEnabled: 'cypress-qase-reporter',  
        cypressQaseReporterReporterOptions: {  
            mode: "testops",  
            debug: true,  
            testops: {  
                api: {  
                    token: '<app-token>',  // Replace with your Cypress app token  
                },  
                project: '<prj-code>',  // Replace with your Qase project code  
                uploadAttachments: true,  
                run: {  
                    complete: true,  
                },  
            },  
            framework: {  
                cypress: {  
                    screenshotsFolder: 'cypress/screenshots',  
                }  
            }  
        },  
    },  
    video: false,  
    e2e: {  
        setupNodeEvents(on, config) {  
            require('cypress-qase-reporter/plugin')(on, config);  
            require('cypress-qase-reporter/metadata')(on);  
        },  
    },  
});
```

The above example is using ES Module format. For a CommonJS version, please copy the code from here

If you already override before:run or after:run hooks in your cypress.config.js, configure your e2e section like this:\
Please refer to this article for guidance on how to find your Project code in Qase.

```javascript
e2e: {  
  setupNodeEvents(on, config) {  
    require('cypress-qase-reporter/metadata')(on);  
    on('before:run', async () => {  
      console.log('override before:run');  
      await beforeRunHook(config);  
    });  
    on('after:run', async () => {  
      console.log('override after:run');  
      await afterRunHook(config):  
    });  
  },  
}
```

If you do not want to configure the reporter’s options in your Cypress configuration file, you have the option to use a qase.config.json to have all your reporter options defined.

Create a new `qase.config.json` file at the root of your repository, and add the project code and App token.

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

### Let’s verify the integration

We’ll use a simple test to check if the results are being published to your Qase project. Create a cypress/e2e/tests/ directory in the root of your project and add the following test:

```javascript
describe('Simple Cypress Test', () => {

  it('Visits a webpage and checks the heading', () => {  
    cy.visit('<https://example.com')>;  // Visit this page.  
    cy.get('h1').should('have.text', 'Example Domain'); // Check title  
  });  
});
```

Now, let’s run the test by executing the following command:

```Text npm
QASE_MODE=testops npx cypress run
```
```Text yarn
QASE_MODE=testops yarn cypress run
```

In the above command, we’re setting the reporter’s mode to `testops` using the Environment variable `QASE_MODE`.
