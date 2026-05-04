---
title: root suite
deprecated: false
hidden: false
metadata:
  robots: index
---
## When your Qase project has more than one source of results

Most teams start with one test framework reporting to one Qase project. The suite hierarchy in Qase mirrors the file structure of the test code, and everything looks tidy.

Then the project grows. You add API tests alongside your Playwright e2e tests. Or a second service starts reporting to the same project. Or the mobile team shares the project with the web team.

Suddenly the suite tree in Qase looks like this:

```
├── tests
│   ├── auth
│   │   └── login
│   ├── checkout
│   └── profile
├── api
│   ├── auth
│   └── users
```

Two `auth` folders from two different codebases. Which is which? You can tell from context if you know the codebase, but anyone else looking at the dashboard — a QA lead, a product manager, a new team member — can't.

<br />

## What root suite does

`rootSuite` adds a top-level folder to every result the reporter sends. It's prepended to whatever suite hierarchy the test already has.

Without root suite:

```
tests > auth > login > "User can log in"
```

With `rootSuite: "Web E2E"`:

```
Web E2E > tests > auth > login > "User can log in"
```

Every result from that reporter instance gets the same prefix. The suite tree in Qase becomes:

```
├── Web E2E
│   ├── tests
│   │   ├── auth
│   │   └── checkout
├── API Tests
│   ├── auth
│   └── users
├── Mobile
│   ├── onboarding
│   └── payments
```

Each source of results has its own top-level namespace. No collisions, no ambiguity.

<br />

## How to set it

Config file:

```json
{
  "rootSuite": "Web E2E"
}
```

Environment variable:

```bash
QASE_ROOT_SUITE="Web E2E" npx playwright test
```

CLI flag (Python):

```bash
pytest --qase-root-suite="Web E2E"
```

All three methods are equivalent. Environment variables and CLI flags override the config file, as with all reporter options.

<br />

## When it's useful

**Multiple frameworks, one project.** You run Playwright for e2e and Jest for unit tests, both reporting to the same Qase project. Set `rootSuite: "E2E"` on the Playwright reporter and `rootSuite: "Unit"` on the Jest reporter. The suite tree stays clean.

**Multiple services, one project.** Your checkout service and your user service both have test suites reporting to the same project. Set `rootSuite: "Checkout Service"` and `rootSuite: "User Service"` respectively.

**Monorepo with multiple apps.** Your monorepo has `apps/web`, `apps/mobile`, `apps/admin`. Each has its own test suite and its own reporter config. Root suite gives each app its own namespace in Qase without needing separate projects.

**CI-driven labeling.** You can set root suite dynamically in CI to label results by branch, team, or pipeline:

```yaml
env:
  QASE_ROOT_SUITE: "PR-${{ github.event.pull_request.number }}"
```

This is a niche use case — environments and tags are usually better for this — but it works when you want visual separation in the suite tree.

<br />

## When you don't need it

If you have one test framework reporting to one Qase project, root suite adds an unnecessary level of nesting. Skip it.

If you're using `qase.suite()` in your test code to explicitly set suite paths, root suite still prepends on top of that. So `qase.suite('Auth\tLogin')` with `rootSuite: "E2E"` becomes `E2E > Auth > Login`. That's fine if you want it, but be aware of the extra level.

<br />

## How it interacts with auto-detected suites

Most reporters auto-detect suites from your test structure:

* **Playwright/Jest/Vitest**: file path + `test.describe()` blocks
* **Pytest**: file path + class name (dot-separated)
* **Java**: package + class name
* **Cucumber/Behave**: feature file path
* **Newman**: Postman collection folder structure

Root suite sits above all of this. It doesn't replace the auto-detected hierarchy — it wraps it. If your Playwright test lives at `tests/auth/login.spec.ts` inside a `test.describe('Positive Cases')` block, the full suite path becomes:

```
rootSuite > tests > auth > login > Positive Cases
```

If you also use `qase.suite('Auth\tLogin')` to override the auto-detected suite, root suite still prepends:

```
rootSuite > Auth > Login
```

<br />

## In case of pre-existing suites

The reporter checks for duplicates. If the first element of the suite hierarchy already matches the root suite value, it won't add it again. So you won't end up with `E2E > E2E > tests > auth` if something sets the suite twice.

***

| Setting    | Config file | Env variable      | CLI flag (Python)   | Default |
| ---------- | ----------- | ----------------- | ------------------- | ------- |
| Root suite | `rootSuite` | `QASE_ROOT_SUITE` | `--qase-root-suite` | Not set |

<br />
