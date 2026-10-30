---
title: token and project code
deprecated: false
hidden: false
metadata:
  robots: index
---
The reporter is off by default — it won't send anything until you tell it where to send results. You need three values to make that connection, plus a fourth if your workspace is on the EU cluster:

* **Mode** — set to `testops` to turn reporting on.
* **API token** — authenticates the reporter with your Qase account.
* **Project code** — tells the reporter which project to send results to.
* **API host** — tells the reporter which Qase cluster to use. The default, `qase.io`, is the US cluster.

<br />

### The fastest way: environment variables

If you just want to see it work, export these variables and run your tests:

```bash
export QASE_MODE=testops
export QASE_TESTOPS_API_TOKEN=your-token-here
export QASE_TESTOPS_PROJECT=DEMO
export QASE_TESTOPS_API_HOST=qase.io  # eu.qase.io for the EU cluster
```

That's enough. Run your tests, and results will appear in Qase.

<br />

### The durable way: config file + env vars

For anything beyond a quick test, put the non-secret values in `qase.config.json` and keep the token in an environment variable:

```json
// qase.config.json
{
  "mode": "testops",
  "testops": {
    "project": "DEMO",
    "api": {
      "host": "qase.io"
    }
  }
}
```

```shell
export QASE_TESTOPS_API_TOKEN=your-token-here
```

The token comes from the environment, everything else lives in the file. This way, your config is version-controlled and your secret stays out of the repo.

> **Note:** The mode defaults to `off`, so if you forget to set it, nothing breaks — the reporter just stays silent. This is intentional. You probably don't want every local test run pushing results to Qase.

<br />

### The API host: US or EU cluster

Qase runs two clusters. The default host, `qase.io`, is the US cluster. If your workspace is on the EU cluster (you open Qase at `app-eu.qase.io`), set the host to `eu.qase.io`, or the reporter sends results to the US cluster and fails:

```json
// qase.config.json
{
  "mode": "testops",
  "testops": {
    "api": {
      "host": "eu.qase.io"
    },
    "project": "DEMO"
  }
}
```

Or as an environment variable:

```bash
export QASE_TESTOPS_API_HOST=eu.qase.io
```

The EU cluster does not need the `enterprise` flag. The Go reporter cannot reach the EU cluster yet because of a known bug that is being fixed.

<br />

### For Enterprise users:

If your organization uses a dedicated Qase instance (e.g., `yourcompany.qase.io` instead of `app.qase.io`), you'll need two additional settings:

```json
// qase.config.json
{
  "mode": "testops",
  "testops": {
    "api": {
      "host": "yourcompany.qase.io",
      "enterprise": true
    },
    "project": "DEMO"
  }
}
```

Or as environment variables:

```bash
export QASE_TESTOPS_API_HOST=yourcompany.qase.io
export QASE_TESTOPS_API_ENTERPRISE=true
```

When `host` is set to a custom domain, the reporter routes API calls to `https://api-yourcompany.qase.io` instead of the default `https://api.qase.io`. The `enterprise` flag tells the reporter to use enterprise-specific behavior.

> **Note:** If you're on the standard Qase cloud (`app.qase.io`), skip this — the defaults work out of the box.