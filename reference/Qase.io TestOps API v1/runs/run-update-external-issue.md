---
title: Update external issues for runs
excerpt: >
  This method allows you to update links between test runs and external issues
  (such as Jira tickets).


  You can use this endpoint to:

  - Link test runs to external issues by providing the external issue identifier
  (e.g., "PROJ-1234")

  - Update existing links by providing a new external issue identifier

  - Remove existing links by setting the external_issue field to null


  **Important**: Each test run can have only one link with an external issue. If
  a test run already has an external issue link, providing a new external_issue
  value will replace the existing link.


  The endpoint supports both Jira Cloud and Jira Server integrations. Each
  request can update multiple test run links in a single operation.
api:
  file: qaseio-testops-api-v1.json
  operationId: run-update-external-issue
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---