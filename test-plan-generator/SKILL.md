---
name: test-plan-generator
description: Generates a complete QA Test Plan document (objective, scope, test environments, defect reporting procedure, test strategy, schedule, phase-by-phase entry/exit criteria, tools, risks, approvals) from a project description, requirement set, or release scope, and delivers it as a Word document. Use this skill whenever the user asks to "create a test plan", "write a test strategy", "plan testing for" a release/project/feature, or needs a formal document describing how testing will be approached, resourced, and scheduled — as distinct from individual test cases (use test-case-generator for those). Applicable to Agile, Waterfall, and hybrid delivery models; adapts sections to the stated methodology and to web, API, or mobile testing as relevant.
license: MIT
metadata:
  author: Sid
  version: "2.0.0"
  category: qa
  repository: https://github.com/Siddharth-S25/claude-qa-toolkit/tree/main/test-plan-generator
---

# Test Plan Generator

Turns a project/feature/release description into a formal QA Test Plan document (`.docx`), structured to match how real client-facing test plans are actually organized — not just the IEEE 829 outline.

## Workflow

### 1. Gather the inputs

Before drafting, make sure you have (ask only for what's genuinely missing and blocking a section — don't interrogate for everything):

- **System under test**: what it is (web app, API, mobile app), and its base URL/environment if known
- **Scope**: what feature/release/system is being tested, and what's explicitly out of scope
- **Methodology**: Agile/Scrum, Waterfall, or hybrid — changes schedule framing (sprints vs phases)
- **Timeline**: release date or sprint window, if known
- **Team**: who's doing the testing (roles, not necessarily names), and area-specific points of contact if the user gives them (e.g. frontend/backend/devops owners for defect routing)
- **Environments**: what environments exist (dev/QA/staging/prod) and their URLs
- **Risk context**: anything explicitly flagged as high-risk, new, or previously buggy

If timeline, team, or environment details aren't given, don't block — write the section with a clearly marked placeholder (e.g. "*[Team size/roles TBD]*") rather than inventing specifics.

### 2. Structure the document

Use this section order — it's the order real test plans in the wild actually use, not the classroom IEEE 829 order:

1. **Objective** — one paragraph: what system is being tested and why this test effort exists
2. **Scope** — see "Scope discipline" below; then an **Inclusions** subsection breaking scope down by the system's actual functional areas (e.g. for an API: per-endpoint or per-operation CRUD breakdown; for a web app: per-page or per-flow breakdown) — this is where the real depth goes, not in the top-level scope list
3. **Test Environments** — a Name/URL table (e.g. QA, Pre-Prod, Staging) plus, when the system has a UI, the OS x Browser x Device coverage matrix (e.g. Windows - Chrome/Firefox/Edge, macOS - Safari, Android - Chrome, iOS - Safari). Skip the device matrix entirely for a pure backend/API system.
4. **Defect Reporting Procedure** — its own section, not folded into Deliverables:
   - Criteria for what counts as a defect
   - Reporting steps (template, repro steps, evidence attached)
   - Triage/prioritization process (severity + priority levels, who triages)
   - Tools used for tracking
   - Point-of-contact table by area if the user gave role/owner info (e.g. Frontend / Backend / DevOps -> owner)
   - Communication cadence (e.g. daily status email, standup mention)
5. **Test Strategy** — three parts, all three matter:
   - **Test design techniques** to be applied: Equivalence Class Partitioning, Boundary Value Analysis, Decision Table Testing, State Transition Testing, Use Case Testing, plus Error Guessing and Exploratory Testing where expertise-driven coverage adds value — list only the ones relevant to what's being tested, don't pad
   - **The actual test cycle process**: smoke/sanity test gate before deep testing (reject unstable builds, wait for a stable one), in-depth execution using the created test cases, parallel execution across supported environments, defect logging and status reporting cadence, repeat cycles until quality bar is met
   - **Testing philosophy/best practices** being followed: Context-Driven Testing, Shift-Left Testing, Exploratory Testing alongside scripted cases, End-to-End Flow Testing — include the ones that actually apply to this project, briefly explained, not just named
6. **Test Schedule** — a Task/Dates table (Test Plan creation, Test Case creation, Test Case execution, Summary report submission) mapped to sprints or calendar phases per the stated methodology
7. **Test Deliverables** — list the artifacts produced (test plan, test cases, execution reports, defect reports, summary report, automation scripts if applicable)
8. **Entry and Exit Criteria — broken out per phase**, not one generic pair:
   - **Requirement Analysis**: entry (requirements/details received) / exit (requirements understood, doubts cleared)
   - **Test Execution**: entry (test cases signed off, application ready) / exit (test case reports and defect reports ready)
   - **Test Closure**: entry (reports ready) / exit (test summary report delivered)
9. **Tools** — the concrete tool list (bug tracker, test management tool, screenshot/recording tool, document tools) — keep it to tools actually named or clearly implied by the project context
10. **Risks and Mitigations** — Risk / Mitigation pairs, each risk specific to what the user described (e.g. environment instability, tight timeline, resource availability, third-party dependency) — never generic filler with no connection to the actual project
11. **Approvals** — which documents go to the client/stakeholders for sign-off (test plan, test scenarios, test cases, reports) and a one-line statement that execution doesn't proceed until sign-off is received, if that's the user's process

### Scope discipline

Do not default to a boilerplate list of every possible testing type (functional, performance, security, load, internationalization, compatibility, usability, CI/CD, backup/recovery, rate limiting...) regardless of the project. That produces a padded, generic-looking plan. Only include a testing type in Scope if it's actually relevant to what's being tested — a small internal admin tool doesn't need internationalization or rate-limiting testing; an internal-only API doesn't need cross-browser compatibility testing. When the user's description is thin, ask which of the non-obvious types (performance, security, load) they actually want covered rather than including all of them by default.

### 3. Write it

Read `/mnt/skills/public/docx/SKILL.md` before creating the file and follow its formatting guidance (headings, styles, page numbers if the document is long enough to warrant them — this document type typically runs long enough for a table of contents). Use a clean, professional layout: numbered sections, a short title block (project name, version, date, author), and tables for the environment matrix, schedule, defect POC list, and risk register rather than prose paragraphs.

Save to `/mnt/user-data/outputs/` and use `present_files` to share it.

### 4. Be honest about gaps

In the chat reply (not buried in the document), call out any section you had to leave as a placeholder because the input didn't cover it, and flag any Scope item you excluded as "not applicable" so the user can add it back in if they actually need it.
