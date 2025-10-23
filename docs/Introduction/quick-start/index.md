---
title: Before we jump in
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
## Source and Documentation

All of our reporters are **open source** and available on our [GitHub page](https://github.com/qase-tms).

Each language has its own repository (you’ll see them pinned at the top), typically set up as a monorepo. These include:

* The Qase API client for that language.
* A shared “**commons**” library used by all reporters in that language.

Feel free to bookmark the ones that are relevant to you — but don’t worry, we’ll link directly to the right places as we go through the setup and usage for each reporter.

<br />

## What’s a Reporter?

A reporter is a module or plugin that listens to events from your test runner (like Jest, Playwright, or JUnit) and manages how test results get processed, displayed, or passed along.

In our case, Qase reporters collect execution details like test status, duration, and error messages — and then send that data to your Qase project via the API. This lets you automatically sync test results with your Qase projects.

A quick clarification here: Reporters do not execute tests — that’s the job of the test runner. Instead, they observe and react to test events, meaning they can’t influence how tests are run or their outcomes, only how results are recorded or reported.

So if when you're configuring the setup (like which files to include or how to parallelize), you’ll still want to check your test runner’s official documentation.

<br />

## Configuring a Reporter

Most test frameworks come with their own built-in reporters — like `list`, `spec`, or `html` — so you can view test output locally. The Qase reporter is an additional one you’ll include in your setup to push results into your Qase project.

To get started, you’ll need to configure a few key options:

* `mode`: When set to testops, this enables result reporting to Qase.
* `API Token`: Your Qase API token — used to authenticate requests.
* `Project code`: The code for the Qase project you want to report to.

We’ll walk through where to find your API token and project code in the next section.

There are other config options available too, some specific to the framework — we’ll cover those in the specific reporter docs.
