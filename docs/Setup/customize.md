---
title: Customize
deprecated: false
hidden: false
metadata:
  robots: index
---
Up to this point, you've installed a reporter, connected it to Qase, and learned how to enrich your test results with metadata, steps, attachments, and parameters. With the defaults, that's enough to get results flowing into Qase.

But the defaults assume a simple case: one project, one run per execution, auto-generated run titles, no environment tracking, no test plan. That works for getting started — it won't work for long once your team is running tests in CI across multiple environments, tracking regression cycles, or coordinating manual and automated testing in the same project.

This section covers everything you can customize about how the reporter interacts with Qase:

The Reporter Lifecycle — what actually happens when you run your tests, step by step. Read this first — it's the mental model for everything else in this section. 

* Test Runs — naming, describing, tagging, and controlling when runs complete. 
* Environments — telling Qase where your tests ran (staging, production, etc.). 
* Test Plans — running tests as part of a planned testing cycle. 
* Qase Project Settings — auto-creation of test cases, auto-updates, and other project-level behaviors that affect how results are processed. 
* Configurations — reporting browser, OS, or device matrices alongside your results. You don't need all of these. 

Read the lifecycle page, then feel free to jump to whatever's relevant to your setup.
