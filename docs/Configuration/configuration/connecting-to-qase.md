---
title: Connecting to Qase
deprecated: false
hidden: false
metadata:
  robots: index
---
The reporter is off by default — it won't send anything until you tell it where to send results. You need three values to make that connection:

* **Mode** — set to `testops` to turn reporting on.
* **API token** — authenticates the reporter with your Qase account.
* **Project code** — tells the reporter which project to send results to.

<br />

### The fastest way: environment variables

If you just want to see it work, export three variables and run your tests:

```bash
export QASE_MODE=testops
export QASE_TESTOPS_API_TOKEN=your-token-here
export QASE_TESTOPS_PROJECT=DEMO
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
    "project": "DEMO"
  }
}
```

```shell
export QASE_TESTOPS_API_TOKEN=your-token-here
```

The token comes from the environment, everything else lives in the file. This way, your config is version-controlled and your secret stays out of the repo.

> **Note:** The mode defaults to `off`, so if you forget to set it, nothing breaks — the reporter just stays silent. This is intentional. You probably don't want every local test run pushing results to Qase.

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
