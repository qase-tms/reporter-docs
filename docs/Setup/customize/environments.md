---
title: environments
deprecated: false
hidden: false
metadata:
  robots: index
---
You run the same test suite against staging on every PR and against production after each deploy. Both runs pass. A week later, someone asks: "When did production tests last fail?" You scroll through a list of identically-named runs and can't tell which targeted what.

Environments solve this. They let you tag a test run with _where_ it ran, so you can filter, compare, and track results per deployment target.

<br />

### A new concept: Qase-side references

Up to this point, everything you've configured lives in your repo — config files, env vars, annotations in code. Environments are different. You create them in Qase first, then reference them from your reporter config by their **slug**.

This pattern — "create it in Qase, reference it from config" — will come up again with test plans and configurations. The idea is the same each time: Qase holds the definition, your reporter just points to it.

<br />

### Setting up an environment

1. In your Qase project, go to **Environments** (in the left sidebar).
2. Create an environment — give it a title (e.g., "Staging") and a slug (e.g., `staging`). The slug is what your reporter will use.
3. Reference the slug in your config:

```json
{
  "environment": "staging"
}
```

Or as an env var:

```bash
export QASE_ENVIRONMENT=staging
```

That's it. Every test run created by the reporter will now be tagged with the "Staging" environment in Qase.

<br />

### How it works under the hood

When the reporter starts a run, it takes your slug, calls the Qase API to find a matching environment, and attaches its ID to the run. If the slug doesn't match any environment in your project, the run is created without one — no error, no failure, just no environment tag. This is by design: the reporter won't block your tests over a missing environment. But it does mean a typo in the slug will silently produce untagged runs.

> **Tip:** If your runs are showing up without an environment and you're sure you set one, enable `debug` mode and check the logs. The reporter logs whether it found the environment or not.

<br />

### The CI pattern

In practice, you probably don't want to hardcode the environment in your config file — it changes depending on where the pipeline is running. Set `environment` via an env var in your CI workflow:

```yaml
# GitHub Actions example
jobs:
  test-staging:
    env:
      QASE_MODE: testops
      QASE_TESTOPS_API_TOKEN: ${{ secrets.QASE_API_TOKEN }}
      QASE_ENVIRONMENT: staging

  test-production:
    env:
      QASE_MODE: testops
      QASE_TESTOPS_API_TOKEN: ${{ secrets.QASE_API_TOKEN }}
      QASE_ENVIRONMENT: production
```

Your `qase.config.json` stays environment-agnostic. CI decides where the tests ran.

<br />

### When to use environments

* You deploy to multiple targets (dev, staging, production, regional instances) and want to track test health per target.
* You want to filter the test runs dashboard by environment to answer "how's staging doing?"
* You want to compare pass rates across environments over time.

If you only have one environment or don't care about the distinction, skip this. You can always add it later — it's just a config value, not a code change.
