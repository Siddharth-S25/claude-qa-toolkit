---
name: selenium-to-playwright-converter
description: Converts Selenium WebDriver test code (Java, Python, or JavaScript/TypeScript — page objects, test classes, waits, assertions, config, and runner setup) into equivalent Playwright code, and produces a coverage checklist proving nothing was dropped in translation. Use this skill whenever the user pastes or uploads Selenium test code and asks to convert, migrate, or port it to Playwright, or mentions "Selenium to Playwright migration". This is a full-fidelity migration skill, not a quick syntax swap — it explicitly tracks locators, waits, assertions, hooks, parallelization, and reporting so nothing is silently lost.
license: MIT
metadata:
  author: Sid
  version: "1.0.0"
  category: qa
  repository: https://github.com/Siddharth-S25/claude-qa-toolkit
---

# Selenium → Playwright Converter

Converts Selenium code to Playwright while explicitly tracking every element that could be silently dropped in a naive conversion — the failure mode this skill exists to prevent.

## Workflow

### 1. Inventory the source code before converting anything

Read the full Selenium code first and build an inventory (mentally or as scratch notes) of every one of these, since each is a common place conversions silently lose behavior:

- **Locators**: every `By.xpath`, `By.cssSelector`, `By.id`, etc., and any custom locator strategies
- **Waits**: explicit waits (`WebDriverWait`, `ExpectedConditions`), implicit waits, `Thread.sleep`/`time.sleep` calls, fluent waits
- **Assertions**: every assert statement and what it actually checks
- **Hooks**: `@Before`/`@After` (JUnit/TestNG), `setUp`/`tearDown` (Python), `beforeEach`/`afterEach` — and what each one does (browser launch/teardown, login, data reset)
- **Page Object patterns**: class structure, constructors, `PageFactory` usage
- **Parallel execution / grid config**: TestNG `parallel` attributes, Selenium Grid capabilities, browser/OS matrix
- **Reporting/listeners**: Allure/Extent annotations, screenshot-on-failure listeners, custom logging
- **Test data / parameterization**: `@DataProvider`, CSV/Excel-driven tests, parameterized fixtures
- **Config**: browser capabilities, timeouts, base URLs, environment switching

### 2. Convert with direct equivalents — don't approximate

Use these standard mappings (language-appropriate syntax):

| Selenium | Playwright |
|---|---|
| `driver.findElement(By.xpath(...))` | `page.locator('xpath=...')` — but prefer rewriting to `page.getByRole/getByText/getByTestId` where the original intent is clear (e.g. finding a button by visible text) |
| `WebDriverWait` + `ExpectedConditions.elementToBeClickable` | Playwright's built-in auto-waiting — usually just `page.locator(...).click()`; only add explicit `waitFor` when the original wait was checking something auto-waiting doesn't cover |
| `Thread.sleep` / `time.sleep` | Remove and replace with an explicit condition wait (`expect(locator).toBeVisible()`, `page.waitForResponse`, etc.) — never carry over a raw sleep, but note in the checklist what condition it's now waiting on |
| JUnit/TestNG `@BeforeMethod`/`@Test` | Playwright Test `test.beforeEach`/`test()` |
| `Assert.assertEquals(...)` | `expect(...).toBe(...)` / `expect(locator).toHaveText(...)` |
| PageFactory `@FindBy` | Playwright Page Object class with locators initialized in the constructor via `page.locator(...)` |
| TestNG `@DataProvider` | Playwright Test parameterization (loop generating `test()` blocks, or `test.describe` with a data array) |
| Grid/capabilities config | `playwright.config.ts` `projects` array (one project per browser/device) |
| Allure/Extent annotations | Playwright's built-in `test.step()`, `test.info().attach()`, and its native HTML/trace reporting |

### 3. Preserve structure and naming

Keep the same file/class organization, test names, and page object structure unless the user asks for a restructure. This is a migration, not a rewrite — reviewers on the user's team should be able to diff old vs new test-by-test.

### 4. Generate the coverage checklist

This is the step that fulfills "without missing anything" — always produce it, even for small conversions. For each category from Step 1, list: item found in original → where it landed in the converted code → any behavior change and why. Format as a table:

| Category | Original | Converted To | Notes |
|---|---|---|---|
| Wait | `WebDriverWait(driver, 10).until(elementToBeClickable(loginBtn))` | Removed — Playwright auto-waits on `.click()` | Behavior equivalent; auto-wait covers this |
| Hook | `@BeforeMethod setUp()` — launches driver, navigates to base URL | `test.beforeEach` — same | Direct equivalent |
| Sleep | `Thread.sleep(3000)` before checking cart total | `expect(cartTotal).toHaveText('$45.00')` | Original sleep had no real condition — now waits on the actual value instead of a fixed delay |

Flag anything that has **no clean Playwright equivalent** (e.g. certain Selenium Grid-specific vendor capabilities, some legacy browser support) explicitly rather than silently dropping it — tell the user it needs a manual decision.

### 5. Deliver

- Converted code as file(s) matching the original language ecosystem where reasonable (Java → Playwright Java, Python → Playwright Python, JS/TS → Playwright Test) — ask if the user wants to switch language (e.g. Java Selenium → Playwright TypeScript) rather than assuming.
- The coverage checklist, either inline in chat for small conversions or as a companion `CONVERSION-NOTES.md` file alongside the code for larger ones.
- A short summary noting total items converted, any behavior changes, and anything flagged for manual review.
