---
title: CI/CD integrations and flow
deprecated: false
hidden: true
metadata:
  robots: index
---
Qase allow you to integrate the CI/CD pipeline that enables you to trigger your automated test run from Qase.

You can see all the supported CI/CD platform from here - [https://app.qase.io/apps](https://app.qase.io/apps) and refer to the documentation available on our <Anchor label="help centre" target="_blank" href="https://help.qase.io/en/?q=CI%2FCD">help centre</Anchor> for installation guide.

<Image align="center" border={false} src="https://files.readme.io/a83ad3de4281e8a65d77833a5b6cc6d34f53fcfde0c6e721904a3e1b7abfe4e8-82520.png" />

After you have installed the respective CI/CD app on Qase, you will need to configure pipeline on that CI/CD platform to enable it to receive these two variables from Qase: QASE_RUN_ID and QASE_PROJECT_CODE
These variables are sent from Qase to your CI platform when you trigger run from Qase.

But, since Qase reporter's expects the variable to be QASE_TESTOPS_RUN_ID and QASE_TESOPS_PROJECT_CODE, we need to make sure we are mapping these variables in the workflow file with the one CI/CD receives from Qase.

For Instance, for Bitbucket we map these variables like this -

<Image border={false} src="https://files.readme.io/5dabf94609016d8eabbb745e383a46f67c7cec109091648634419ca7cf303684-9534.png" />

You can similarly map it in the workflow for other CI/CD platforms.

Now, when you trigger the CI/CD pipeline from Qase, 
You will need to choose the appropriate details from the test run page here and you can trigger the pipeline on your CI/CD platform from Qase - 

<Image border={false} src="https://files.readme.io/7001073497e9d91bdb58335ca4a67c1f089d2e4b1d8a86d0fd285daefcc78f69-16558.png" />

<br />
