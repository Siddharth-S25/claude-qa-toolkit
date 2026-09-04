---
name: test-plan-generator
description: Generates a complete QA Test Plan document (scope, strategy, entry/exit criteria, resources, schedule, risks, environments, deliverables) from a project description, requirement set, or release scope, and delivers it as a Word document. Use this skill whenever the user asks to "create a test plan", "write a test strategy", "plan testing for" a release/project/feature, or needs a formal document describing how testing will be approached, resourced, and scheduled — as distinct from individual test cases (use test-case-generator for those). Applicable to Agile, Waterfall, and hybrid delivery models; adapts sections to the stated methodology.
license: MIT
metadata:
  author: Sid
  version: "1.0.0"
  category: qa
  repository: https://github.com/Siddharth-S25/claude-qa-toolkit
---

# Test Plan Generator

Turns a project/feature/release description into a formal QA Test Plan document (`.docx`), structured close to IEEE 829 but trimmed for practical, Agile-friendly use.

## Workflow

### 1. Gather the inputs

Before drafting, make sure you have (ask only for what's genuinely missing and blocking a section — don't interrogate for everything):

- **Scope**: what feature/release/system is being tested, and what's explicitly out of scope
- **Methodology**: Agile/Scrum, Waterfall, or hybrid — changes section framing (sprints vs phases)
- **Timeline**: release date or sprint window, if known
- **Team**: who's doing the testing (roles, not necessarily names)
- **Environments**: what environments exist (dev/QA/staging/prod) and which are used for what
- **Risk context**: anything explicitly flagged as high-risk, new, or previously buggy

If timeline, team, or environment details aren't given, don't block — write the section with a clearly marked placeholder (e.g. "*[Team size/roles TBD]*") rather than inventing specifics.

### 2. Structure the document

Standard sections, adapt/omit based on what's relevant to the project's scale (a small feature test plan doesn't need every section a full release plan does):

1. **Introduction** — purpose of the document, project/feature summary
2. **Scope** — in-scope and explicitly out-of-scope items
3. **Test Objectives** — what the testing effort is meant to verify/prove
4. **Test Strategy** — levels (unit/integration/system/UAT), types (functional, regression, performance, security, accessibility — include only the types relevant to what's being tested), automation vs manual split
5. **Entry Criteria** — what must be true before testing starts (e.g. build deployed to QA, unit tests passing)
6. **Exit Criteria** — what must be true to consider testing complete (e.g. no open Critical/High defects, X% test case pass rate, coverage threshold met)
7. **Test Environment** — environments, data, and any environment-specific constraints
8. **Roles & Responsibilities** — who owns what part of testing
9. **Schedule / Milestones** — mapped to sprints or phases, whichever fits the methodology
10. **Risks & Mitigations** — testing-specific risks (e.g. environment instability, tight timeline, third-party dependency) with a mitigation for each — this section must have real, specific risks tied to what the user described, not generic filler
11. **Deliverables** — what artifacts testing will produce (test cases, defect reports, summary report, automation scripts, etc.)
12. **Defect Management** — severity/priority definitions and the triage/reporting process, if not already defined elsewhere in the org

### 3. Write it

Read `/mnt/skills/public/docx/SKILL.md` before creating the file and follow its formatting guidance (headings, styles, page numbers if the document is long enough to warrant them). Use a clean, professional layout: numbered sections, a short title page or header block (project name, version, date, author), and tables for entry/exit criteria and the risk register rather than prose paragraphs.

Save to `/mnt/user-data/outputs/` and use `present_files` to share it.

### 4. Be honest about gaps

In the chat reply (not buried in the document), call out any section you had to leave as a placeholder because the input didn't cover it, so the user knows exactly what to fill in before using the plan for real.
