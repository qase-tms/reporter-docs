---
title: Reporters
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
## Learn more about Qase's Reporters

Qase reporters are open-source packages maintained by Qase that help publish your test results to your Qase project.

They act as the bridge between your test code and your test runs in Qase, collecting results as tests execute and pushing them to Qase automatically, so everything is tracked in one place.

We’ve got reporters for a range of frameworks including, in JavaScript, Python, Java, PHP, and more. You’ll find all of them on our [GitHub](https://github.com/qase-tms). If your framework isn’t supported out of the box, don’t worry, you can use our SDKs and public API to build a custom integration.

We provide pre-generated API clients for Python, JavaScript, Java, and Go — these are created using [OpenAPI Generator](https://openapi-generator.tech/). If you need a client in a language we don’t support yet, you can generate your own using [our API spec](https://github.com/qase-tms/specs).

All reporters are built on top of the Qase API, which follows standard REST conventions. All Reporters these endpoints:

* [GET /project](https://developers.qase.io/reference/get-projects)
* [POST /run/\{code}](https://developers.qase.io/reference/create-run)
* [POST /result/\{code}/\{id}/bulk](https://developers.qase.io/reference/create-result-bulk)
* [PATCH /run/\{code}/\{id}/complete](https://developers.qase.io/reference/complete-run)

You’ll find more endpoints in the full [API reference](https://developers.qase.io/reference/introduction-to-the-qase-api), along with everything you need to build your own client or integration.

<br />

## Where Should I Start?

We recommend starting with the introduction pages next — they’ll guide you through:

* Picking the right reporter for your framework
* Configuring it for your test setup
* Running your tests and reporting results
* Common troubleshooting tips

After this, you can dive into the specific reporter documentation for your language, with code examples and config options to help you get up and running quickly.

