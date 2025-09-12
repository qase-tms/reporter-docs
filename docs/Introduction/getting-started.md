---
title: API Reporters
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
## What are API Reporters?

Qase API reporters are packages we maintain to help you integrate your automated testing framework with Qase. Think of them as the bridge between your test code and your test runs in Qase — they collect your test results and push them into Qase automatically, so you can track everything in one place.

We’ve got reporters for a range of frameworks including, in JavaScript, Python, Java, PHP, and more. You’ll find all of them on our [GitHub](https://github.com/qase-tms). If your framework isn’t supported out of the box, don’t worry — you can use our SDKs and public API to build a custom integration.

We provide pre-generated API clients for Python, JavaScript, Java, and Go — these are created using [OpenAPI Generator](https://openapi-generator.tech/). If you need a client in a language we don’t support yet, you can generate your own using [our API spec](https://github.com/qase-tms/specs).

All reporters are built on top of the Qase API, which follows standard REST conventions. All Reporters these endpoints:

- [GET /project](https://developers.qase.io/reference/get-projects)
- [POST /run/{code}](https://developers.qase.io/reference/create-run)
- [POST /result/{code}/{id}/bulk](https://developers.qase.io/reference/create-result-bulk)
- [PATCH /run/{code}/{id}/complete](https://developers.qase.io/reference/complete-run)

You’ll find more endpoints in the full [API reference](https://developers.qase.io/reference/introduction-to-the-qase-api), along with everything you need to build your own client or integration.

<br />

## Where Should I Start?

We recommend starting with the introduction pages next — they’ll guide you through:

- Picking the right reporter for your framework
- Configuring it for your test setup
- Running your tests and reporting results
- Common troubleshooting tips

After this, you can dive into the specific reporter documentation for your language, with code examples and config options to help you get up and running quickly. 

<br />

## Error Codes

| Code | Meaning                                                                                   |
| :--- | :---------------------------------------------------------------------------------------- |
| 400  | Bad Request - Your request is invalid.                                                    |
| 401  | Unauthorized - Your API key is wrong.                                                     |
| 403  | Forbidden - Your role doesn't have enough permissions to perform this action.             |
| 404  | Not Found - The resource could not be found.                                              |
| 405  | Method Not Allowed - You tried to access a resource with an invalid method.               |
| 410  | Gone - The resource requested has been removed from our servers.                          |
| 413  | Payload Too Large - Your bulk request is too large. Please see the limitations.           |
| 422  | Unprocessable Entity - You have validation errors in some fields.                         |
| 429  | Too Many Requests - You're performing too many requests! Slow down!                       |
| 500  | Internal Server Error - We had a problem with our server. Try again later.                |
| 503  | Service Unavailable - We're temporarily offline for maintenance. Please try again later.  |
| 507  | Insufficient Storage - Storage is full. Please, remove something or upgrade your account. |