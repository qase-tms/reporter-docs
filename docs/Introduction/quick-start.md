---
title: See reporting in action
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

## Configuring a Reporter

To get started, you’ll need to configure a few key options:

* `mode`: When set to testops, this enables result reporting to Qase.
* `API Token`: Your Qase API token — used to authenticate requests.
* `Project code`: The code for the Qase project you want to report to.

We’ll walk through where to find your API token and project code in the next section.

There are other config options available too, some specific to the framework — we’ll cover those in the specific reporter docs.