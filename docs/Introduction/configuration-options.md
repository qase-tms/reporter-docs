---
title: Configuration options
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
Qase reporters support primarily _two_ configuration methods: (_although some reporters allow CLI options_)

- A configuration File (`qase.config.json` or `.qaserc`)
- Environment Variables

The configuration system uses a priority hierarchy where environment variables override configuration file values, and both can be further overridden by direct options passed to the reporter.

_direct CLI options in the run command_ **> **_environment variables_** >** _options in config file_

> The reporters look for the config file in your project's root directory. 

<br />

**Common options**

| Description                                    | Config file   | Environment variable | Default value | Required | Possible values            |
| ---------------------------------------------- | ------------- | -------------------- | ------------- | -------- | -------------------------- |
| Mode of reporter                               | `mode`        | `QASE_MODE`          | `off`         | No       | `testops`, `report`, `off` |
| Fallback mode of reporter                      | `fallback`    | `QASE_FALLBACK`      | `off`         | No       | `testops`, `report`, `off` |
| Environment                                    | `environment` | `QASE_ENVIRONMENT`   | undefined     | No       | Any string                 |
| Root suite                                     | `rootSuite`   | `QASE_ROOT_SUITE`    | undefined     | No       | Any string                 |
| Enable debug logs                              | `debug`       | `QASE_DEBUG`         | `False`       | No       | `True`, `False`            |
| Enable capture logs from `stdout` and `stderr` | `captureLogs` | `QASE_CAPTURE_LOGS`  | `False`       | No       | `True`, `False`            |

<br />

**TestOps configuration**

| Description                                                                                                            | Config file               | Environment variable           | Default value                           | Required | Possible values |
| ---------------------------------------------------------------------------------------------------------------------- | ------------------------- | ------------------------------ | --------------------------------------- | -------- | --------------- |
| Token for [API access](https://developers.qase.io/#authentication)                                                     | `testops.api.token`       | `QASE_TESTOPS_API_TOKEN`       | undefined                               | Yes      | Any string      |
| Qase API host. For enterprise users, specify full address: `api-example.qase.io`                                       | `testops.api.host`        | `QASE_TESTOPS_API_HOST`        | `qase.io`                               | No       | Any string      |
| Qase enterprise environment                                                                                            | `testops.api.enterprise`  | `QASE_TESTOPS_API_ENTERPRISE`  | `False`                                 | No       | `True`, `False` |
| Code of your project, which you can take from the URL: `https://app.qase.io/project/DEMO` - `DEMO` is the project code | `testops.project`         | `QASE_TESTOPS_PROJECT`         | undefined                               | Yes      | Any string      |
| Qase test run ID                                                                                                       | `testops.run.id`          | `QASE_TESTOPS_RUN_ID`          | undefined                               | No       | Any integer     |
| Qase test run title                                                                                                    | `testops.run.title`       | `QASE_TESTOPS_RUN_TITLE`       | `Automated run <Current date and time>` | No       | Any string      |
| Qase test run description                                                                                              | `testops.run.description` | `QASE_TESTOPS_RUN_DESCRIPTION` | `<Framework name> automated run`        | No       | Any string      |
| Qase test run complete                                                                                                 | `testops.run.complete`    | `QASE_TESTOPS_RUN_COMPLETE`    | `True`                                  |          | `True`, `False` |
| Qase test plan ID                                                                                                      | `testops.plan.id`         | `QASE_TESTOPS_PLAN_ID`         | undefined                               | No       | Any integer     |
| Size of batch for sending test results                                                                                 | `testops.batch.size`      | `QASE_TESTOPS_BATCH_SIZE`      | `200`                                   | No       | Any integer     |
| Enable defects for failed test cases                                                                                   | `testops.defect`          | `QASE_TESTOPS_DEFECT`          | `False`                                 | No       | `True`, `False` |

<br />

**Report configuration**

| Description                 | Config file                | Environment variable            | Default value         | Required | Possible values |
| --------------------------- | -------------------------- | ------------------------------- | --------------------- | -------- | --------------- |
| Driver used for report mode | `report.driver`            | `QASE_REPORT_DRIVER`            | `local`               | No       | `local`         |
| Path to save the report     | `report.connection.path`   | `QASE_REPORT_CONNECTION_PATH`   | `./build/qase-report` |          |                 |
| Local report format         | `report.connection.format` | `QASE_REPORT_CONNECTION_FORMAT` | `json`                |          | `json`, `jsonp` |

<br />

## Example `qase.config.json` config:

```json
{  
  "mode": "testops",  
  "fallback": "report",   
  "debug": false,  
  "environment": "production",  
  "testops": {  
    "api": {  
      "token": "your-api-token",  
      "host": "qase.io"  
    },  
    "project": "your-project-code",  
    "run": {  
      "title": "Automated Test Run",  
      "description": "Regression test suite",  
      "complete": true,  
      "tags": ["regression", "automated"]  
    },  
    "uploadAttachments": true,  
    "plan": {  
      "id": 123  
    },  
    "batch": {  
      "size": 200  
    },  
    "defect": true,  
    "configurations": {  
      "values": [  
        {  
          "name": "browser",  
          "value": "chrome"  
        }  
      ],  
      "createIfNotExists": true  
    }  
  },  
  "report": {  
    "driver": "local",  
    "connections": {  
      "local": {  
        "path": "./test-results",  
        "format": "json"  
      }  
    }  
  }  
}
```