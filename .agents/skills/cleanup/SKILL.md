---
name: cleanup
description: Post-edit sweep that detects and fixes code hygiene violations before a PR is opened.
---

# Skill: Cleanup

## Purpose
Run this skill **after completing any code change** and **before opening a PR**. It performs a deterministic sweep of every file you touched (and their direct dependents) to catch hygiene issues that slip past linting.

---

## Step 1 — Duplicate Detection

Scan every file in the changeset for:

| Pattern | Action |
|---------|--------|
| Same `import` statement appearing more than once | Delete the duplicate line. |
| Same variable / schema / interface declared more than once in one file | Keep the **first** declaration. If the two declarations conflict, flag for human review — do NOT silently pick one. |
| Identical consecutive statements (e.g., two `sendResponse()` calls) | Delete the duplicate. A controller must call `sendResponse` exactly **once**. |

### How to check
```bash
# Find duplicate imports in touched files
grep -n "^import " <file> | sort -t: -k2 | uniq -d -f1
```

---

## Step 2 — Convention Conformance

For every `.ts` file in the changeset, verify:

1. **No `any`** — Search for `: any`, `as any`, `<any>`. Replace with `unknown` or a precise type. The only exception is `// eslint-disable-line @typescript-eslint/no-explicit-any` on BullMQ `connection` casts (document why).
2. **No `console.*`** — Replace with the structured `logger` from `src/shared/logger/logger.js`. Use `logger.info(contextObj, message)`, `logger.warn(...)`, `logger.error({ err, ...context }, message)`.
3. **No manual `try/catch` in controllers** — Controllers must be wrapped in `asyncHandler`. Errors propagate to the global `errorMiddleware`. If you find a `try/catch` in a controller, remove it and ensure the route uses `asyncHandler(controllerFn)`.
4. **`sendResponse` everywhere** — Every successful controller response must use `sendResponse()` from `src/shared/http/sendResponse.js`. Never use raw `res.json()` or `res.send()`.
5. **`AppError` everywhere** — Services must throw `AppError` (not `new Error(...)`). Include status code, human message, and error code from `ErrorCodes`.
6. **Auth guards on every route** — Every route must have `requireAuth` middleware unless explicitly documented as public. If a route is intentionally public, add a comment: `// PUBLIC: <reason>`.

---

## Step 3 — Import Hygiene

1. All imports must be at the **top of the file**, never inline.
2. Use `import type { X }` for type-only imports (TypeScript `verbatimModuleSyntax`).
3. Group imports: Node built-ins → third-party → project absolute paths. Separate groups with a blank line.
4. All import paths must end with `.js` extension (ESM resolution).

---

## Step 4 — Schema & Model Consistency

For any file in `src/modules/*/`:

1. **Zod schemas** — Each schema must be declared exactly once. Export the inferred type: `export type X = z.infer<typeof XSchema>;`.
2. **Mongoose models** — Must include `isoDatePlugin`, soft-delete pre-hooks (`find`, `findOne`, `findOneAndUpdate`, `countDocuments`, `aggregate`), and appropriate indexes.
3. **Schema ↔ Validation alignment** — If a Mongoose field is `required: true`, the corresponding Zod schema must NOT mark it `.optional()` unless the service layer provides a default before DB insertion.

---

## Step 5 — Security Sweep

1. **Sensitive fields** — Confirm `passwordHash`, `apiKeyHash`, secret keys are excluded from `toJSON` transforms and never appear in `sendResponse` data payloads.
2. **Role assignment** — Only admin-protected endpoints may accept `role` in the request body. Signup must never allow `SUPER_ADMIN` or `ADMIN`.
3. **Query injection** — No `...rest` or `...filters` spread from `req.query` into MongoDB queries. All query params must be explicitly destructured and validated through Zod.
4. **Webhook auth** — External webhooks must verify a signature or API key. No unauthenticated webhook endpoints.

---

## Step 6 — Final Verification

Run these commands and confirm zero errors:

```bash
npm run lint
npm run build
npm test
```

If any command fails, fix the issue before proceeding. Do NOT skip failing tests.

---

## Output

After completing the sweep, produce a brief checklist summary:

```
CLEANUP REPORT
- Duplicates removed: <count>
- Convention fixes: <count>
- Security issues: <count>
- Build: PASS/FAIL
- Tests: PASS/FAIL
```
