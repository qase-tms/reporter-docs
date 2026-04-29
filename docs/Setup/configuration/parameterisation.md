---
title: parameterisation
deprecated: false
hidden: false
metadata:
  robots: index
---
You run the same login test with five different users. They all show up in Qase as "Login test — passed." One of them failed last Tuesday. Which user? Which environment? You're scrolling through logs trying to reconstruct what happened.

Parameters solve this. They tag each test result with the specific inputs that produced it — the username, the browser, the dataset row, the API endpoint — so when something fails, you see exactly which combination broke.

<br />

### which run of _this_ test failed?

If you're doing any form of data-driven testing — parameterized tests, data providers, scenario outlines, matrix builds — you already have parameters. The question is whether they make it into your reporting or get lost in a sea of identical test names.

Without parameters in Qase, five runs of the same test look like five copies of the same result. With parameters, each one is distinct: you can filter, compare, and track them independently. When your checkout test passes for credit cards but fails for PayPal, that distinction is visible to everyone on the team — not just the person who read the CI log.

<br />

### How parameters get reported

Most frameworks capture parameters automatically — you don't need to do anything extra.

* **Data-driven frameworks** (TestNG `@DataProvider`, pytest `@pytest.mark.parametrize`, MSTest `[DataRow]`, PHPUnit data providers) — the reporter picks up parameter names and values from the framework itself. If your test receives `username` and `password` from a data provider, those appear as parameters in Qase without any annotation.

* **BDD/Gherkin** — Scenario Outline examples are captured automatically. Each row in your Examples table becomes a parameterized result.

* **Playwright and other JS frameworks** — parameters aren't auto-captured because the framework doesn't have a built-in data provider concept. You declare them explicitly:

```typescript
test('Checkout with payment method', async ({ page }) => {
  const method = 'paypal';
  qase.parameters({ payment_method: method });
  // test implementation
});
```

The pattern is consistent: if your framework knows about the parameters, the reporter captures them. If it doesn't, you add one line.

<br />

### Group parameters

Sometimes you want Qase to treat a set of parameters as a single unit. Say you're testing across a matrix of browser + OS combinations. You don't want Qase to create separate history threads for every possible pairing — you want it to group `{browser: "Chrome", os: "Windows"}` as one configuration and `{browser: "Firefox", os: "macOS"}` as another.

That's what group parameters do. They tell Qase: "these values travel together."

```typescript
test('Cross-browser checkout', async ({ page }) => {
  qase.groupParameters({ browser: 'Chrome', os: 'Windows' });
  // test implementation
});
```

Most teams don't need group parameters on day one. Start with regular parameters. If you later find your test history is fragmenting across too many combinations, group parameters let you consolidate.

<br />

### Excluding noisy parameters

Some frameworks inject parameters you don't care about — internal IDs, fixture names, timestamps. These clutter your results without adding signal. In Python, you can exclude specific parameter names:

```python
@qase.ignore_parameters("request", "fixture_name")
@pytest.mark.parametrize("browser", ["chrome", "firefox"])
def test_checkout(browser, request):
    pass
```

Only `browser` gets reported. The rest is filtered out before it reaches Qase.

<br />

### When to care about parameters

* **You're running parameterized tests** — parameters are probably already being captured. Check your first test run in Qase and confirm the values appear. If they don't, add explicit `qase.parameters()` calls.
* **You're running the same test in a CI matrix** (multiple browsers, multiple environments) — use group parameters so Qase tracks each matrix cell as a coherent unit.
* **You're seeing duplicate-looking results** — if the same test appears multiple times with no way to tell them apart, you're missing parameters. Add them.

For the exact syntax in your framework, check the demo repository.
