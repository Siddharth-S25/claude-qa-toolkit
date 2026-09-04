---
name: security-checker
description: Runs a defensive, first-pass security review of a codebase or project — dependency vulnerability scanning, hardcoded secret detection, and static checks for common insecure patterns (injection risk, weak crypto, insecure config, unsafe eval/deserialization) — and delivers a severity-ranked findings report. Use this skill whenever the user asks to "check security", "run a security audit/scan", "find vulnerabilities in my project", or wants to know if their code/dependencies are safe before a release. This is a triage/awareness tool for the project's own codebase, not a penetration test, and does not test or attack systems the user doesn't own.
license: MIT
metadata:
  author: Sid
  version: "1.0.0"
  category: security
  repository: https://github.com/Siddharth-S25/claude-qa-toolkit
---

# Security Checker

A first-pass, defensive security review for a project's own codebase — dependency risk, leaked secrets, and common insecure code patterns — delivered as a severity-ranked report. This complements, but does not replace, a real penetration test or a dedicated security team's review.

## Scope and boundaries

- Only ever run against code the user owns or has explicit rights to scan (their own repo/project directory) — never against a third-party site or system.
- This is static/dependency analysis, not exploitation. Never attempt to actually trigger, weaponize, or demonstrate a found vulnerability against a live system.
- Findings are a starting point for the user to verify and fix, not a certification that the project is secure.

## Workflow

### 1. Identify the project type

Detect the language/ecosystem (Node/npm, Python/pip, Java/Maven-Gradle, etc.) from manifest files (`package.json`, `requirements.txt`/`pyproject.toml`, `pom.xml`/`build.gradle`) to pick the right tooling below.

### 2. Dependency vulnerability scan

Run the ecosystem-appropriate audit tool and capture the output:

| Ecosystem | Command |
|---|---|
| Node | `npm audit --json` |
| Python | `pip-audit` (install if missing: `pip install pip-audit --break-system-packages`) |
| Java (Maven) | check for `dependency-check-maven` plugin if present; otherwise note that a full CVE scan needs the OWASP Dependency-Check plugin and list dependencies with obviously outdated major versions as a lighter-weight signal |

Summarize by severity (Critical/High/Medium/Low) rather than dumping raw tool output.

### 3. Secret scanning

Search the codebase for likely hardcoded credentials and keys — API keys, AWS/GCP/Azure credential patterns, private key headers (`-----BEGIN PRIVATE KEY-----`), database connection strings with embedded passwords, JWT secrets, generic `password =` / `api_key =` / `secret =` assignments with literal string values (not env var references). Use `grep -rn` with targeted patterns across the project, excluding `node_modules`, `.git`, `vendor`, and build output directories. Report the file and line number, never the secret's actual value in full — mask it (e.g. `AKIA****************`).

### 4. Static pattern checks

Grep/review for common insecure patterns relevant to the language in use:

- **Injection risk**: string concatenation/formatting building SQL, shell commands, or `eval`/`exec`-style calls from unsanitized input
- **Weak/broken crypto**: MD5/SHA1 used for password hashing, hardcoded encryption keys/IVs, use of `Math.random()`/`random` (non-cryptographic) for tokens or session IDs
- **Insecure deserialization**: `pickle.loads` on untrusted input (Python), Java native deserialization of untrusted streams
- **Disabled security controls**: TLS/certificate verification disabled (`verify=False`, `rejectUnauthorized: false`), CORS wildcarded (`Access-Control-Allow-Origin: *`) alongside credentialed requests, debug mode flags left on (`DEBUG = True`, `app.run(debug=True)`)
- **Insecure config in source**: `.env` files committed to the repo, default/example credentials left in config files

### 5. Score and report

Aggregate findings into a report with:

- **Summary**: total findings by severity, top 3 issues to fix first
- **Findings table**: Category, Severity, File/Location, Description, Recommendation — one row per finding
- **Dependency scan results**: vulnerable packages, current vs. patched version, severity
- Deliver as a Markdown report by default (`SECURITY-REVIEW.md`); if the user's other work in this project trends toward Excel deliverables, offer `.xlsx` instead (read `/mnt/skills/public/xlsx/SKILL.md` if so).

### 6. Be precise, not alarmist

Every finding must point to an actual file/line or actual `npm audit`/`pip-audit` output — never invent a vulnerability to pad the report. If a scan comes back clean, say so plainly rather than manufacturing low-severity findings to seem thorough. Always close with a one-line reminder that this is a triage pass, and anything Critical/High should be verified and, for production systems, reviewed by someone with security expertise before shipping.
