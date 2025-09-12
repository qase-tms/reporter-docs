---
title: What endpoints do they use?
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
### Phase 1: Before Test Execution (Initialization)

1. **Configuration Management** _(if configurations specified)_
   - **GET** `https://api.qase.io/v1/configuration/{code}` _(v1)_
   - **POST** `https://api.qase.io/v1/configuration/{code}` _(v1)_  
   - **POST** `https://api.qase.io/v1/configuration/{code}/group` _(v1)_
   - Retrieves existing configurations and creates new ones if `createIfNotExists` is enabled.
   <br />

2. **Environment Retrieval** _(if environment specified)_
   - **GET** `https://api.qase.io/v1/environment/{code}` _(v1)_
   - Fetches environment details to obtain environment ID for the test run
   - Executed after configuration management, before run creation
   <br />

3. **Run Creation**
   - **POST** `https://api.qase.io/v1/run/{code}` _(v1)_
   - Creates new test run with retrieved environment and configuration IDs
   - Stores run ID for subsequent operations
   - Called by framework reporter's `onBegin()` hook
   <br />

### Phase 2: During Test Execution (Real-time Processing)

4. **Attachment Upload** _(per test result with attachments)_
   - **POST** `https://api.qase.io/v1/attachment/{code}` _(v1)_
   - Uploads screenshots, logs, and other test artifacts
   - Returns attachment hashes for referencing in results
   - Executed during result transformation before results upload
   <br />

5. **Results Upload** _(batched)_
   - **POST** `https://api.qase.io/v2/{project_code}/run/{run_id}/results` _(v2)_
   - Uploads test results in configurable batches (default: 200 results)
   - Triggered immediately when batch threshold is reached
   - Provides real-time feedback and dashboard links for failed tests
   - Called by framework reporter's `onTestEnd()` hook via `addTestResult()`
   <br />

### Phase 3: After Test Execution (Finalization)

6. **Final Results Upload** _(remaining results)_
   - **POST** `https://api.qase.io/v2/{project_code}/run/{run_id}/results` _(v2)_
   - Uploads any remaining results that didn't reach batch threshold
   <br />

7. **Run Completion** _(if enabled)_
   - **POST** `https://api.qase.io/v1/run/{code}/{id}/complete` _(v1)_
   - Marks test run as completed and generates run URL
   - Called by framework reporter's `onEnd()` hook via `publish()`
   <br />

## Framework Integration Points

| Hook          | Action            | API Calls Triggered                          |
| ------------- | ----------------- | -------------------------------------------- |
| `onBegin()`   | `startTestRun()`  | Configuration → Environment → Run Creation   |
| `onTestEnd()` | `addTestResult()` | Attachment Upload → Results Upload (batched) |
| `onEnd()`     | `publish()`       | Final Results Upload → Run Completion        |