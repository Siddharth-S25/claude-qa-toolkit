# Test Case Generator (Claude Skill)

A Claude skill that turns a plain-text software requirement into a structured QA test case suite, delivered as an Excel file. Built for the requirement-to-test-case workflow QA engineers do manually — write positive/negative cases, then layer on boundary and equivalence analysis for anything with defined ranges or rules.

## What it does

Give Claude a requirement (a field spec, user story, or acceptance criteria), and this skill:

1. Extracts the testable elements — inputs, constraints, business rules, states, workflows.
2. Decides which test design techniques actually apply, rather than forcing all of them onto every requirement:
   - Boundary Value Analysis (BVA) for numeric/date/length fields with a defined range
   - Equivalence Class Partitioning (ECP) for fields with distinct valid/invalid categories
   - Decision Table Testing for combined conditional logic
   - State Transition Testing for entities that move through defined statuses
   - Pairwise/Combinatorial Testing for multiple independent parameters
   - Error Guessing for general robustness checks
3. Generates a full test case suite with ID, Title, Preconditions, Steps, Test Data, Expected Result, Priority, and Technique Used columns.
4. Flags genuinely blocking gaps in the requirement (e.g. no range given at all for a field BVA depends on) instead of silently guessing, but doesn't stop for minor ambiguities.
5. Outputs a formatted `.xlsx` file — bold header row, sensible column widths, frozen header — not a plain text dump.

It does not run or execute the tests, and it does not replace requirement review — it's a first-pass draft meant to be checked and edited before use.

## Installation

1. Download `test-case-generator.skill` from this repo (or clone it and point Claude at the `SKILL.md`).
2. In Claude, use the skill card's **Save skill** option, or add the folder to your skills directory.

## Usage example

**Input** (pasted requirement):

> Password field: 8-16 characters, must contain at least one uppercase letter, one lowercase letter, one number, and one special character. Mandatory field. On successful registration, account status moves from "Unregistered" to "Pending Verification" to "Active" after email confirmation.

**Output**: an `.xlsx` file with 18 test cases, including:

| ID | Title | Technique Used |
|---|---|---|
| TC_001 | Register with valid password meeting all rules | Positive |
| TC_004 | Password length at lower boundary - 1 (invalid, 7 chars) | BVA |
| TC_008 | Password length at upper boundary (valid, 16 chars) | BVA |
| TC_011 | Password from invalid class - only lowercase letters | ECP |
| TC_015 | Account status transitions Pending Verification -> Active on email confirmation | State Transition |
| TC_018 | SQL-injection style string as password is handled safely | Error Guessing |

Each row includes the actual test data used, not placeholder values — e.g. the BVA rows spell out `Pas1!wo` (7 chars, invalid) through `Pass1!word12345678` (17 chars, invalid).

## Known limitations

- Technique selection is only as good as the requirement text — vague requirements produce fewer technique-tagged cases, by design (it won't fabricate ranges or rules that aren't stated).
- No integration with test management tools (Jira/Xray/TestRail) yet — output is a standalone spreadsheet.
- Decision Table and Pairwise generation haven't been tested against requirements with more than ~4 combining conditions; coverage may be incomplete beyond that.

## License

MIT — see [LICENSE.txt](../LICENSE.txt).
