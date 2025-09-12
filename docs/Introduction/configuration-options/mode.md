---
title: Testops and Report modes
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
Qase reporters let you choose **how** results are delivered and **what happens** if that delivery fails. Two options control this behaviour:

1. `mode` – the primary option for sending results to Qase Testops.
2. `fallback` – an automatic backup option that takes over if the primary option breaks.

Think of `testops` as a **pipeline to the cloud**, `report` as a **save-to-disk button**, and `off` as an easy **mute switch**.

<br />

| Mode      | What it does                                             | Typical use                                                                                      | What you need                          |
| --------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ | -------------------------------------- |
| `testops` | Sends results straight to the Qase TestOps cloud via API | you want to publish results to the Qase project, either for real time feedback, or to save them. | `QASE_API_TOKEN` + `QASE_PROJECT_CODE` |
| `report`  | Writes results to local JSON/JSONP files                 | Offline work, security-restricted networks, artefacts for later upload                           | Write access to the file syst          |

<br />

### `testops`: report to Qase.

Set it in the config file…

```json qase.config.json
{
  "mode": "testops",
  "testops": {
    "api": {
      "token": "<token>"
    },
    "project": "<project>",
  },
}
```

…or with an environment variable:

```text Linux Shell
export QASE_MODE=testops
```

The command for setting an Environment variable differs from shell to shell. 

- for ﻿Command Prompt - `set QASE_MODE=testops`
- for ﻿PowerShell - `$env:QASE_MODE="testops"`  ﻿

<br />

### `fallback`: your automatic plan B

`fallback` defines what the reporter should do **if the primary `mode` throws an error** (network outage, bad token, etc.). When that happens, the following happens so that no data is lost and your tests keep running:

1. Catches the error.  
2. Instantly swaps to the fallback mode.  
3. Moves every result it has already captured to the new reporter.  

Example configuration:

```bash
export QASE_MODE=testops
export QASE_FALLBACK=report
```

<br />

## Scenarios

| Scenario                                                 | `mode`    | `fallback` | Why use it                                                                                                   |
| -------------------------------------------------------- | --------- | ---------- | ------------------------------------------------------------------------------------------------------------ |
| Send results to Qase TestOps, with local file as backup. | `testops` | `report`   | Send to Qase first; if the API is unreachable, keep a JSON file locally.                                     |
| Fail-safe CI run                                         | `testops` | `off`      | Push to Qase; if anything breaks, silently disable reporting so the pipeline never fails on reporting alone. |