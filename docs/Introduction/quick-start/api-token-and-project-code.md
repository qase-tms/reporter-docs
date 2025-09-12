---
title: Token and Project code
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
## Personal Token vs App Token

You can generate API tokens from two places in your Qase workspace:

1. **Personal Token** – found in your profile settings.\
   This token carries your user permissions. So if your role allows creating test runs or submitting results, this token will too. It’s tied to your user and is useful for quick setups or when testing things manually.
2. **App Token** – found under the Apps section in the workspace settings.\
   This token is designed specifically for integrations like reporters. The benefits:
   * Actions taken using this token are labeled with the app’s name, not your user.
   * The token is scoped with only the minimum permissions needed by the reporter — good for security.

We recommend using App Tokens for setting up reporters in most cases.

<br />

## Project code

Every Qase project has a unique project code, which is used to associate results with that specific project.

* It can be up to 10 characters long
* Alphanumeric (e.g. WEBAPP, MOBILE123)
* You can set it when creating the project, or change it later, from the project's settings.

<br />

> [How do I find my project code?](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code)

<br />

> 📘 For reporting to work, make sure you’ve got these three things:
>
> 1. `mode` is set to `testops`
> 2. The correct `project code` is used
> 3. A valid `token` is provided
