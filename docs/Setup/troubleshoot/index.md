---
title: Troubleshoot
deprecated: false
hidden: false
metadata:
  robots: index
---
Something not working the way you expected? You're in the right place.

<br />

Most reporter issues fall into a few categories:

* **Results not appearing in Qase** — usually a config issue (wrong mode, bad token, mismatched project code)
* **Results appearing but looking wrong** — linked to the wrong test case, missing steps, missing attachments
* **Performance problems** — slow test runs, timeouts during upload, memory issues from large attachments
* **Status mismatches** — your framework reports one thing, Qase shows another

<br />

The pages in this section cover each of these areas. They're written to be useful in two ways:

1. **Self-service.** Most issues are solvable without filing a ticket. Enabling debug mode, reading the logs, and checking your config against what's documented here will resolve the majority of problems. The reporter logs tell you exactly what it's doing — which API it's calling, what it's sending, and where it's failing.

2. **Better support conversations.** When you do need help, the diagnostic data you collect here — debug logs, config files, reporter version, framework version — is exactly what the support team needs. A ticket that includes a debug log and your `qase.config.json` gets resolved significantly faster than "it's not working."

<br />

> If you need to reach support, contact us through the live chat from the app, or at **[support@qase.io](mailto:support@qase.io)**.
>
> **When you do, include**: your reporter version, framework version, `qase.config.json` (with the token redacted), and the debug log output. The pages in this section show you how to get all of that.
