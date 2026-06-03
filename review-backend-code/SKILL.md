---
name: review-backend-code
description: >
  Review changed backend files for correctness, API design, database usage, performance,
  error handling, validation, and architecture issues. Returns structured findings per file.
  Use when reviewing a PR, diff, or backend code change for bugs, regressions, or bad practices.
---

# Backend Review Skill

Review the provided files and return a JSON array of issues:

```json
[
  {
    "file": "api/controllers/users_controller.rb",
    "line": 34,
    "severity": "critical",
    "category": "database",
    "rule": "n+1-query",
    "message": "Fetching related records inside a loop causes N+1 queries.",
    "suggestion": "Use eager loading at the query level."
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
| `message` | string | What is wrong and why it matters |
| `suggestion` | string | Specific, actionable fix |

## Severity
- **critical** — Data loss, broken functionality, or incorrect business logic.
- **warning** — Bad practice likely to cause bugs or scaling problems.
- **suggestion** — Minor refactor or optional optimization.

## Categories
`database` · `api-design` · `performance` · `error-handling` · `architecture` · `validation` · `testing` · `code-style`

## Scope
- Focus on the diff. Escalate to full-file only for critical issues rooted outside the diff.
- One issue per root cause.
- Skip issues already caught by standard linters.
- Use PR context (title, description, base branch) to assess breaking changes.

## Skip
Lock files · auto-generated migrations · vendored code
