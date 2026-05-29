## Domain: Infrastructure

---

### Issue [Refactor] Replace all `console.log/warn/error` calls with structured `logger`

**Tier:** 🟢 Easy

**Description:**
- **Problem:** Several files use raw `console.log`, `console.warn`, or `console.error` instead of the project's structured `logger` from `src/shared/logger/logger.js`. This creates inconsistent log formatting, breaks structured log parsing in production (e.g., JSON log aggregation with Datadog, CloudWatch, etc.), and makes it difficult to correlate log entries with request context.
  
  Affected files:
  - `src/modules/shipments/shipments.service.ts` (lines 87, 173, 180)
  - `src/modules/webhooks/stellar.webhook.service.ts` (lines 10, 24, 35, 54, 72)
  - `src/services/stellar.service.ts` (line 139)

- **Implementation:** Replace every `console.log(...)`, `console.warn(...)`, and `console.error(...)` with the appropriate `logger.info(...)`, `logger.warn(...)`, or `logger.error(...)` call. Use structured log objects (first argument) with context fields like `shipmentId`, `paymentId`, `error`, etc., followed by a message string.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] No `console.log`, `console.warn`, or `console.error` calls remain in `src/` (except in test files).
- [ ] All replacements use `logger` with structured context objects.
- [ ] Log output in development still shows relevant information.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Run `grep -r "console\." src/` to verify zero occurrences outside tests.
- [ ] Existing tests continue to pass without log-related failures.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `refactor/issue-54-structured-logging`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
