---
title: Configuration
deprecated: false
hidden: false
metadata:
  robots: index
---
All Qase reporters read settings from the same layered system. If the same option is set in more than one place, the higher layer wins.

* **Config file** — your base. Lives in the repo, visible to your team, holds your defaults.
* **Environment variables** — override the file. This is how you inject secrets in CI or flip reporting on without editing anything.
* **Runtime overrides** — the top layer. CLI flags in Python, system properties in Java, framework config in JavaScript. Use these for one-off overrides. C#, Go, and PHP stop at env vars — no third layer.

<Image align="center" border={true} width="500px" src="https://files.readme.io/9df8ef778824abac1f52b55bbc2bf07cb5622ae3eda2c0bdf87acbc953b499bc-config_layer_priority_diagram.svg" className="border" />

### As an example:

Here's the same option — setting the mode to `testops` — expressed at each layer:

```json
// qase.config.json
{
  "mode": "testops"
}
```

```shell
# Environment variable
export QASE_MODE=testops
```

```shell
# CLI flag (Python/pytest)
pytest --qase-mode=testops
```

If all three are set, the CLI flag wins. If only the file and env var are set, the env var wins.

<br />

### When you'd use each layer

* **Locally**, set `"mode": "off"` in the config file. You're iterating on tests — no need to push results to Qase on every run.
* **In CI**, set `QASE_MODE=testops` as an environment variable. It overrides the file, reporting turns on, and you never touch the config.
* **Debugging a flaky test**, pass `--qase-debug=true` on the command line. It overrides everything for that single run, and you don't have to remember to undo it.

The practical pattern: keep `qase.config.json` as your source of truth, use env vars for anything that changes between environments, and forget the top (cli options) layer exists until you need a one-off.

<br />

> Note: Not all reporters offer CLI options for configuration. Eg: Javascript reporters.

<br />
