---
name: generate-coding-standards
description: >
  Navigate the codebase from a given entry point, trace all imports recursively, identify
  inconsistent implementations of the same concern, ask the user to pick a standard for each
  one, then write a coding-standard.md that explains What, Where, and How for every rule.
  Covers Architecture, Approaches, File Structure, and Code Practices.
---

# generate-coding-standards

You are studying a codebase to produce a reusable coding-standard document. Follow these phases:

---

## Phase 1 — Explore

Starting from the entry file provided by the user (e.g. `src/index.tsx`):

1. Read the file and collect every import.
2. For each import, open that file, read it, and collect its imports in turn.
3. Continue recursively until you have visited the full dependency graph.
   Skip files you have already read, and skip third-party packages (only follow local/workspace paths).
4. While reading, take notes on every **implementation pattern** you encounter for the four learning areas below.

### Learning areas

| Area | What to look for |
|---|---|
| **Architecture** | Entry point, provider composition order, state management layers (Redux, React Query, Context), HTTP layer, routing strategy |
| **Approaches** | React import style, component definition style, data-fetching strategy, async patterns, config/endpoint access |
| **File structure** | Top-level layout, feature module conventions, naming of service/hook/type/util/context files |
| **Code practices** | TypeScript typing (`interface` vs `type`), export style (named vs default), styling approach, error handling, feature-flag access |

---

## Phase 2 — Identify Inconsistencies

After exploring, compile a list of concerns where **more than one pattern** was found (e.g. three React import styles, two data-fetching strategies).

For **each inconsistency**, count how many files use each variant so you can present the user with informed options.

---

## Phase 3 — Ask the User

Ask the user **one question at a time** (use `ask_user` with `choices`). For each question:
- Name the concern (e.g. "React Import Style")
- Show the variants found with their file counts
- Let the user pick the standard

Do not ask about things that are already consistent across the codebase.

---

## Phase 4 — Write `coding-standard.md`

Write the output file at the **root of the repository** as `coding-standard.md`.

### Document structure

```
# <Project Name> — Coding Standards

> Brief intro: entry point traced, date generated.

---

## 1. Architecture
   - How the app boots (provider/wrapper tree)
   - Key infrastructure files table (file → purpose)
   - State management strategy table

## 2. File Structure
   - Top-level src/ layout (annotated directory tree)
   - Feature module structure (directory tree + rule for each file)

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
- Include real file paths as citations where helpful (e.g. `src/features/huddles/huddles.service.ts`).
- Architecture section must include the full provider/wrapper nesting tree as a code block.
- File structure section must include annotated directory trees.
- Close the document with a Quick Reference table summarising every rule.

