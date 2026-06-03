---
name: review-security
description: >
  Analyze code for security vulnerabilities and return structured findings. Read-only and
  stateless — no pass/fail decisions, no GitHub calls. Use when reviewing a PR, diff, or
  any code change for injection, auth, authorization, data exposure, or cryptography issues.
---

# Security Review Skill

Review the provided files and return a JSON array of issues:

```json
[
  {
    "file": "api/controllers/auth_controller.rb",
    "line": 22,
    "severity": "critical",
    "category": "injection",
    "rule": "sql-injection",
    "message": "User input interpolated directly into a raw SQL query. An attacker can manipulate the query to dump or delete data.",
    "suggestion": "Use parameterized queries or a query builder."
  }
]
```

Return `[]` if there are no issues.

| Field | Type | Notes |
|---|---|---|
| `file` | string | Exact file path as given |
| `line` | number \| null | Line in the full file, null if not line-specific |
| `severity` | `"critical"` \| `"warning"` \| `"suggestion"` | |
| `category` | string | One of the categories below |
| `rule` | string | Short kebab-case id |
| `message` | string | The vulnerability and what an attacker could do with it |
| `suggestion` | string | Specific, actionable fix |

## Severity
- **critical** — Directly exploitable vulnerability, data exposure, or authentication bypass.
- **warning** — Weakness requiring specific conditions to exploit, or a bad practice that increases attack surface.
- **suggestion** — Hardening improvement or defense-in-depth measure.

## Categories
`injection` · `authentication` · `authorization` · `data-exposure` · `cryptography` · `input-validation` · `dependency` · `configuration` · `mobile-specific`

**Secret redaction:** When a hardcoded secret is found, report the file and line only — never reproduce the secret value in the finding.

## Scope
- Review all files in the diff.
- Always explain exploitability — vague warnings are not useful.
- Do not flag theoretical issues with no realistic attack path.
- One issue per vulnerability.

## Skip
Lock files · auto-generated files · documentation · test fixtures (unless they contain real credentials)

