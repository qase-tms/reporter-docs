---
title: Start here
deprecated: false
hidden: false
metadata:
  robots: index
---
# Clarifying the terminology

Before diving in, it's worth getting clear on a few terms that often get used interchangeably, but mean very different things.

Test _frameworks_, _test runners_, and _reporters_ each describe a distinct layer in your testing pipeline, and each one has a specific job. 

A useful rule of thumb: you **pick your Qase reporter based on which test runner you're using**. So understanding the difference between these three first will make the rest of this guide click into place.

## Test Frameworks: helps you write tests

A test framework is the foundation of your test suite. It gives you the tools, structure, and conventions needed to write tests. Think of it as the language and grammar you use to describe what your application should do.

Frameworks provide:

* A way to define tests (e.g., test(), it(), def test_...)
* Assertions to verify expected behavior (e.g., expect(page).toHaveTitle(...))
* Hooks for setup and teardown (e.g., beforeEach, afterAll)
* Organizational structures like test files, suites, and groups

Examples: Playwright Test, pytest, Jest, Mocha, Robot Framework, JUnit

A test framework on its own doesn't run your tests or report results — it just defines them. That's where the next piece comes in.

## Test Runners: executes your tests

A test runner is what takes your written tests and executes them. It reads your test files, runs them in the right order, and collects the raw pass/fail outcomes. Many modern frameworks bundle a test runner inside them (Playwright Test is a good example), but they can also be separate tools.

The test runner is responsible for:

Discovering test files based on your configuration

Running tests (sequentially or in parallel)

Tracking which tests passed, failed, or were skipped

Exiting with an appropriate status code so your CI pipeline knows if tests succeeded

Examples: pytest (runner + framework), playwright test CLI, jest CLI, robot CLI

Once the runner finishes, you have results — but they're raw. They live in memory or a local output file. To do anything meaningful with them, like view a report or send them to a test management platform, you need a reporter.

## Reporters: help capture, create and share results

A reporter listens to the test runner as it executes tests and transforms the raw results into something consumable. That could be a formatted summary in your terminal, an HTML report you can open in a browser, or a structured file like JUnit XML that your CI platform can parse.

Reporters can:

Print results to the console in a readable format

Generate HTML, JSON, or XML output files

Integrate with third-party dashboards and notification tools

Provide detailed failure context (stack traces, screenshots, video)

Examples: Playwright's built-in html, dot, and json reporters; pytest's --tb options and plugins like pytest-html; Allure

By default, most reporters are self-contained — they capture and display results locally. But if you're using a test management platform like Qase, you need results to travel further than your local machine or CI logs. That's exactly what Qase's reporters are built for.

## Qase's Reporters: get your results into Qase

Qase reporters are a specific category of reporter designed to do one thing: send your test results to Qase. While a standard reporter might generate an HTML file on your machine, a Qase reporter takes those same results and pushes them to Qase via the API — creating test runs, recording results against test cases, and marking runs as complete.

Each Qase reporter is built for a specific test framework or runner:

qase-playwright for Playwright Test

qase-pytest for pytest

qase-jest for Jest

qase-robotframework for Robot Framework

A Qase reporter does three things in sequence:

Creates a new test run in your Qase project when the test suite starts

Sends test results to Qase as tests complete (in configurable batches)

Marks the run as complete once all results have been submitted

This is why Qase reporters have their own configuration options — like QASE_TESTOPS_API_TOKEN, QASE_TESTOPS_PROJECT, and QASE_TESTOPS_BATCH_SIZE — that go beyond what a standard reporter needs. They're not just formatting results; they're communicating with an external API on your behalf.

Note: A Qase reporter works alongside your existing reporters, not instead of them. You can run the Qase reporter and the Playwright HTML reporter at the same time — one sends results to Qase, the other builds a local HTML report
