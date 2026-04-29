---
title: First steps
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Recommended flow

Once you’ve successfully published a test result or two to Qase, it’s a good time to take a step back and understand how the integration works and what the best practices are.

There are two places where you can configure the integration:

* In your code, using reporter options
* In your Qase workspace, through project settings

Reporter options control what data is sent from your tests. Qase workspace settings define what happens once those results are received — things like whether to auto-create test cases or update existing ones.

We’ll go into more detail in the next section, but here’s a quick overview of how the flow typically works.

<br />

## First execution

When you’re starting out, your test cases only exist in code. Qase doesn’t yet know about them.

To avoid manually creating each test in the UI, Qase has a feature that can automatically create test cases based on your first execution. 

To enable this:

1. Go to Project Settings > Test Run tab.
2. Turn on Auto-create test cases

Once this is enabled, Qase will create test cases automatically the first time you run your tests, using the data from your test code. You can also choose which fields (like title, steps, expected result) will be populated.

This step helps establish your test repository in Qase without any extra manual work.

<br />

## Linking Auto Tests with Test Cases

After the first run, assuming auto-create is enabled, your test cases will now exist in Qase and reflect the structure from your code. At this point, you have two options for linking test results to these cases:

1. **Rely on automatically matching the results with existing test cases.**\
   If you do nothing, Qase will try to match test results using the *test title* and the *suite path*. This works as long as:
   * Titles for the test in code, and in Qase, stay the same
   * You don’t move tests to different suites\
     If you change titles or suite paths, Qase can no longer match the tests so it creates a new test case leading to duplication of tests.
2. **Use Qase IDs for linking (Recommended)**\
   A more reliable option is to link your tests in code to the test cases in Qase using the unique Qase ID.\
   Once this link is established:
   * Title and suite changes won’t affect matching
   * Results will always be assigned to the correct test case\
     This method eliminates the risk of duplicates and ensures consistency between your code and your Qase project.