---
name: test-case-generator
description: Generates a structured QA test case suite (positive, negative, and edge cases) from a pasted software requirement, user story, or acceptance criteria, and delivers it as a downloadable Excel file. Applies test design techniques such as Boundary Value Analysis (BVA), Equivalence Class Partitioning (ECP), Decision Table Testing, State Transition Testing, Error Guessing, and Pairwise/Combinatorial testing, choosing whichever techniques actually fit the requirement. Use this skill whenever the user pastes or describes a requirement and asks for "test cases", "test scenarios", "QA test design", "boundary value analysis", "equivalence partitioning", or wants a requirement turned into a test suite — even if they don't explicitly ask for an Excel file or name a specific technique.
license: MIT
metadata:
  author: Sid
  version: "1.0.0"
  category: qa
  repository: https://github.com/Siddharth-S25/claude-qa-toolkit/tree/main/test-case-generator
---

# Test Case Generator

Turns a plain-text software requirement into a professional, technique-driven test case suite delivered as an `.xlsx` file.

## Workflow

### 1. Read the requirement

The requirement will normally be pasted as plain text in the chat message itself. Read it carefully and extract the testable elements:

- **Inputs/fields**: name, type (numeric, string, date, enum...), constraints (min/max, length, format, mandatory/optional)
- **Business rules / conditional logic**: "if X and Y then Z"
- **Workflow / states**: e.g. order status moving from Draft → Submitted → Approved
- **Independent parameters that combine**: multiple filters/settings used together

### 2. Check for blocking gaps — but don't over-ask

Only pause and ask the user a clarifying question if something is genuinely **blocking** — i.e. you cannot generate meaningful test cases without it. Examples of blocking gaps:
- A field is clearly numeric/date but no range, min/max, or format is given at all, and BVA is expected to be a core part of the ask
- The requirement references a rule or screen that isn't described anywhere

Do NOT stop for minor ambiguities — make a reasonable, clearly-stated assumption and proceed (e.g. note "Assuming email field max length is 255 chars, industry default" as a comment/preconditions note rather than blocking).

### 3. Decide which techniques actually apply

Don't force every technique onto every requirement. Match technique to requirement shape:

| Requirement shape | Technique(s) to apply |
|---|---|
| Numeric/date/length field with a defined range | Boundary Value Analysis (min-1, min, min+1, max-1, max, max+1) |
| Field with distinct valid/invalid categories (e.g. valid email formats vs invalid) | Equivalence Class Partitioning |
| Multiple conditions combining to decide an outcome ("if premium user AND cart > $50 then free shipping") | Decision Table Testing |
| Entity moves through defined states/statuses | State Transition Testing |
| Several independent input parameters used together (e.g. filters, dropdowns) | Pairwise / Combinatorial Testing |
| General robustness / "what could go wrong" | Error Guessing |

Always include plain **positive** (happy path) and **negative** (invalid input, error handling) cases regardless of technique. Technique-based cases are additional depth, not a replacement for basic positive/negative coverage.

### 4. Generate the test case set

Each row uses this template:

| Column | Notes |
|---|---|
| ID | e.g. TC_001, sequential |
| Title | Short, action-oriented |
| Preconditions | State the system must be in before the test |
| Steps | Numbered, concrete, reproducible |
| Test Data | Actual values used (esp. important for BVA/ECP rows) |
| Expected Result | Specific, verifiable outcome |
| Priority | High / Medium / Low |
| Technique Used | Positive / Negative / BVA / ECP / Decision Table / State Transition / Pairwise / Error Guessing |

Group rows logically: Positive cases first, then Negative, then technique-specific sections (BVA, ECP, etc.) as applicable — use a "Technique Used" column value rather than separate sheets unless the suite is very large (30+ cases), in which case separate sheets per category are fine.

### 5. Build the Excel file

Before writing the file, read `/mnt/skills/public/xlsx/SKILL.md` and follow its guidance for creating the spreadsheet (formatting, headers, column widths, freeze panes, etc.). Use a clean header row (bold, filled), autofit/reasonable column widths, and freeze the header row so it's usable as a working QA artifact, not just a data dump.

Save to `/mnt/user-data/outputs/` and use `present_files` to share it.

### 6. Summarize, don't dump

In the chat reply, don't repeat all the test cases as text — the Excel file is the deliverable. Give a short summary: total test case count, techniques applied and why, and call out any assumptions you made for gaps that weren't blocking.
