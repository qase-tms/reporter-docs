---
title: Introduction to the Qase API
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: noindex
next:
  description: ''
---
Welcome to the [Qase.io](https://qase.io) API! You can use our API to access Qase.io API endpoints, which allows to retrieve information about entities stored in database and perform actions with them. The API is organized around [REST](https://en.wikipedia.org/wiki/Representational_State_Transfer).

You can try using the API on the page [API Reference](https://developers.qase.io/reference) or by getting to know the public page in [Postman](https://www.postman.com/qaseio?tab=collections).

<br />

## Authentication

Qase.io uses API tokens to authenticate requests. You can view and manage your API keys in [API tokens pages](https://app.qase.io/user/api/token).

Your API keys has the same access rights as your role in the app, so be sure to keep them secure! Do not share your secret API keys in publicly accessible areas such as GitHub, client-side code, and so forth.

Qase API expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Token: API_TOKEN`

All API requests must be made over [HTTPS](http://en.wikipedia.org/wiki/HTTP_Secure). Calls made over plain HTTP will fail. API requests without authentication will also fail.

<br />

## API Rate limits

Rate limits apply per workspace. Every API token in a workspace shares the same limit, whether the requests are made using a personal access token or an app token, so issuing another token does not give you more throughput.

* Enterprise plan: 1,000 requests per minute (or, Custom Limits)
* Teams / Business (legacy): 600 requests per minute
* Startup: 300 requests per minute
* Free: 150 requests per minute

Two policies apply at the same time. The `rpm` policy caps the sustained rate over 60 seconds, and is the plan limit listed above. The `burst` policy caps a short spike over 10 seconds. A request is served only when both policies allow it, so a workspace that stays under its per-minute limit can still be throttled if it sends those requests in a narrow burst.

Every response tells you where you stand:

* `RateLimit-Policy` — the policies in force, where `q` is the quota and `w` is the window in seconds.
* `RateLimit` — how much of each policy is left, where `r` is the requests remaining and `t` is the seconds until that policy frees a slot.
* `X-RateLimit-Limit`, `X-RateLimit-Remaining` and `X-RateLimit-Reset` — a single-number mirror of the above, for older clients. They report whichever of the two policies is closer to its limit, and `X-RateLimit-Reset` is the Unix timestamp at which that window resets.

For example, on a workspace whose plan limit is 600 requests per minute:

```text
RateLimit-Policy: "rpm";q=600;w=60, "burst";q=100;w=10
RateLimit: "rpm";r=412;t=37, "burst";r=88;t=2
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 88
X-RateLimit-Reset: 1756742400
```

Here the `X-RateLimit-*` headers describe the `burst` policy rather than the plan limit, because a spike is in progress and `burst` is the policy closer to being exhausted.

Once either policy is exceeded, clients receive an HTTP `429` with a `Retry-After` header telling them how many seconds to wait before sending requests again:

Retry-After: 12

{
  "status": false,
  "errorMessage": "API rate limit exceeded."
}

In addition, there is a separate limit of 3,000 API requests per minute per IP address. It sits well above the per-workspace limits and acts as a fallback if the rate limiting service is unavailable, so under normal conditions you will reach your workspace limit first.

<br />

## Access rights

Qase.io is using [Role-based Access Control](https://help.qase.io/hc/en-us/articles/360014177877-Workspace-Management-Roles) system to restrict some features usage in Web interface and the same rules are applied to API methods. In description for each method you will find a rule name, that is required to perform an action through API. If you don't have enough access rights, you will receive an error with 403 status code.

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
