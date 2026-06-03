---
name: review-frontend-code
description: >
  Review changed React/frontend files for bugs, hooks mistakes, state management issues,
  data-fetching problems, accessibility failures, and performance regressions. Returns structured
  findings per file. Use when reviewing a PR, diff, or React code change for correctness issues.
---

# Frontend Review Skill

Review the provided files and return a JSON array of issues:

```json
[
  {
    "file": "app/src/pages/projects/ProjectList.tsx",
    "line": 42,
    "severity": "critical",
    "category": "performance",
    "rule": "missing-memo",
    "message": "Expensive derived data is recomputed on every render with no memoization.",
    "suggestion": "Wrap with useMemo() and list the correct dependency array."
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
- **critical** — Broken functionality, data loss risk, or incorrect business logic.
- **warning** — Bad practice likely to cause bugs, stale data, or accessibility failures.
- **suggestion** — Minor refactor or optional optimization.

## Categories
`state-management` · `performance` · `hooks` · `data-fetching` · `accessibility` · `error-handling` · `architecture` · `type-safety` · `code-style`

## Scope
- Focus on the diff. Escalate to full-file only for critical issues rooted outside the diff.
- One issue per root cause.
- Skip issues already caught by ESLint or the TypeScript compiler.
- Use PR context (title, description, base branch) to assess intent and regressions.

## Skip
Lock files · auto-generated files · vendored code · `*.stories.tsx` / `*.test.tsx` (unless the test itself contains the bug)
