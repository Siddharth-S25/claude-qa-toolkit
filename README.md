# Claude QA Toolkit

A collection of Claude skills for QA/SDET work — test design, test planning, framework migration, and security triage. Each skill lives in its own folder with a self-contained `SKILL.md`, so you can install just the ones you need.

## Skills

| Skill | What it does |
|---|---|
| [`test-case-generator`](./test-case-generator) | Turns a pasted requirement into a positive/negative/BVA/ECP test case suite, delivered as an Excel file. |
| [`test-plan-generator`](./test-plan-generator) | Turns a project/feature/release description into a formal QA Test Plan (scope, strategy, entry/exit criteria, schedule, risks), delivered as a Word document. |
| [`selenium-to-playwright-converter`](./selenium-to-playwright-converter) | Converts Selenium test code (Java/Python/JS) to Playwright, with a coverage checklist that tracks every locator, wait, assertion, and hook so nothing is silently dropped. |
| [`security-checker`](./security-checker) | Runs a first-pass security review of a project — dependency vulnerabilities, hardcoded secrets, common insecure code patterns — as a severity-ranked report. |

## Installation

Each skill folder can be zipped/packaged independently as a `.skill` file and installed via the **Save skill** option on the skill card, or pointed at directly if you're running Claude Code / Cowork against this repo.

## Usage example

See each skill's own README/SKILL.md for a worked example — e.g. `test-case-generator` includes a full sample run against a password-field requirement.

## Roadmap

Ideas for future skills in this toolkit, as QA/SDET work keeps shifting toward AI-assisted and shift-left practices:

- **Flaky test / self-healing locator analyzer** — reviews test failure history and suggests more resilient locators or wait strategies
- **API contract testing** — diffs an OpenAPI/Swagger spec against actual responses, flags breaking changes
- **Visual regression setup generator** — scaffolds screenshot-diff testing (Playwright/Percy-style) for a given UI
- **Accessibility (WCAG) audit** — static + rendered-page accessibility checks with a prioritized fix list
- **Synthetic test data generator** — realistic, privacy-safe test data sets matching a given schema
- **Performance/load test script generator** — turns a user flow description into a k6 or JMeter script
- **Test coverage gap analyzer** — cross-references requirements/acceptance criteria against existing test suites to find untested paths

Not committed to a build order yet — open to reprioritizing based on what's actually useful day to day.

## License

MIT — see [LICENSE.txt](./LICENSE.txt). Individual skills may note their own version/author metadata in their `SKILL.md` frontmatter.
