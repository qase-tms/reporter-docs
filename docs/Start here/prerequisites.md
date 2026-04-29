---
title: Prerequisites
deprecated: false
hidden: false
metadata:
  robots: index
---
You need two things from Qase before you set up a reporter: an **API token** and a **project code**.

## API token

An API token lets the reporter authenticate with your Qase account. Copy it somewhere safe — you'll use it as the value for `QASE_TESTOPS_API_TOKEN`.

Treat this token like a password. In CI, store it as a secret — never commit it to your repository.

<br />

### Personal Token vs App Token

You can generate API tokens from two places in your Qase workspace:

1. **Personal Token** – found in your profile settings at **[https://app.qase.io/user/api/token](https://app.qase.io/user/api/token)**.
   This token carries your user permissions. So if your role allows creating test runs or submitting results, this token will too. It’s tied to your user and is useful for quick setups or when testing things manually.
2. **App Token** – found under the Apps section in the workspace settings.  
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

> [How do I find my project code?](https://help.qase.io/en/articles/9787250-how-do-i-find-my-project-code)