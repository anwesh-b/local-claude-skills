---
name: generate-coding-standards
description: >
  Analyze the codebase to generate a coding-standard.md. Navigate from a given entry point (or
  discover one automatically), trace imports recursively, identify inconsistent patterns, ask the
  user one-at-a-time to pick the standard for each inconsistency, then write a coding-standard.md
  explaining What, Where, and How for every rule. Use when user asks to "generate coding standards",
  "document conventions", "create a style guide", or "standardize this codebase".
---

# generate-coding-standards

You are studying a codebase to produce a reusable coding-standard document. Follow these phases:

---

## Phase 0 — Determine Entry Point

If the user has not specified an entry file:
1. Look for common entrypoints: `src/index.*`, `src/main.*`, `src/app.*`, `app/main.*`, `main.*`, `index.*`, `cmd/main.go`, `server.py`, etc.
2. If multiple candidates exist, list them and ask the user: "Which file should I start from?" — wait for their answer before proceeding.
3. If a clear single entrypoint is found, confirm it: "I'll start from `<path>`. Does that look right?"

---

## Phase 1 — Explore

Starting from the entry file:

1. Read the file and collect every **local** import (skip third-party packages).
2. For each local import, open that file, read it, and collect its imports in turn.
3. Continue recursively. Skip files already read. Stop when the full reachable local dependency graph is visited.
   - For alias imports (e.g. `@/`, `~/`, `#`), resolve them using `tsconfig.json`, `jsconfig.json`, `vite.config.*`, or similar config files.
   - For barrel re-exports (`index.*`), follow them but note they are barrels, not implementation files.
   - For dynamic imports (`import(...)`, `require(...)`), note them but do not follow them.
4. While reading, take notes on every **implementation pattern** for the four learning areas below.

### Learning areas

Adapt what you look for based on the actual language and framework in use:

| Area | What to look for |
|---|---|
| **Architecture** | How the app is bootstrapped, layers of the codebase (HTTP, service, data), dependency injection style, routing strategy, state management approach |
| **Approaches** | Module/import style, function vs class definitions, async patterns (async/await vs callbacks vs promises), data-fetching strategy, config/env access |
| **File structure** | Top-level directory layout, feature/module conventions, naming patterns for services, handlers, models, utilities, types, tests |
| **Code practices** | Typing style (where applicable), export style (named vs default), error handling approach, logging approach, test conventions |

---

## Phase 2 — Identify Inconsistencies

After exploring, compile two lists:

**A. Consistent patterns** — concerns where all files use the same approach. These still need to be documented as the standard.

**B. Inconsistent patterns** — concerns where more than one approach was found. For each, count how many files use each variant so you can present the user with informed options.

---

## Phase 3 — Ask the User

For each **inconsistent** concern, ask the user one question at a time:
- State the concern name (e.g. "Async Pattern")
- Show the variants found with their file counts (e.g. "async/await: 14 files, callbacks: 3 files")
- Ask them to pick the standard they want enforced

Ask one question at a time and wait for the answer before asking the next.

Do **not** ask about consistent patterns — infer those as the current standard automatically.

---

## Phase 4 — Write `coding-standard.md`

Write the output file at the **root of the repository** as `coding-standard.md`.

### Document structure

```
# <Project Name> — Coding Standards

> Brief intro: entry point traced, language/framework, date generated.

---

## 1. Architecture
   - How the app bootstraps (entry point → layers)
   - Key infrastructure files table (file → purpose)
   - State/data management strategy

## 2. File Structure
   - Top-level layout (annotated directory tree)
   - Module/feature structure (directory tree + rule for each file type)

## 3. Approaches
   One section per approach concern. Each section must contain:
   ### <Concern Name>
   **What:** one-line description of the rule
   **Where:** which files / folders this applies to
   **How:** ✅ correct code example(s) + ❌ avoid example(s)

## 4. Code Practices
   Same What / Where / How format for each practice rule.

---

## Quick Reference
   A summary table with columns: Concern | What | Where | How
```

### Rules for the document content

- Every standard must answer **What** (the rule), **Where** (scope of files it applies to), and **How** (code examples).
- ✅ correct examples must be complete and copy-pasteable.
- ❌ avoid examples must show the actual anti-pattern found in the codebase, not a generic bad example.
- Include real file paths as citations where helpful (e.g. `src/features/users/users.service.ts`).
- Architecture section must show the app boot flow as a code block or annotated diagram.
- File structure section must include annotated directory trees.
- Close the document with a Quick Reference table summarising every rule.

