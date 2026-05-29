## Domain: Infrastructure

---

### Issue [Bug] Fix unhandled async rejections in `setImmediate` callbacks

**Tier:** 🟡 Medium

**Description:**
- **Problem:** In `src/modules/telemetry/telemetry.service.ts` (line 152) and `src/modules/webhooks/iot.service.ts` (line 111), `setImmediate(async () => { ... })` is used to defer anomaly detection processing. However, `setImmediate` does not handle promise rejections — if the async callback throws, the rejection is unhandled and will trigger Node.js `unhandledRejection` event, which crashes the process in Node 15+ with `--unhandled-rejections=throw` (the default).
  
  The anomaly detection logic inside these callbacks performs database operations (`detectAnomaly`, `emitAnomalyDetected`, `pushAlertJob`) that can all throw, making this a realistic crash vector under database connection issues or Redis failures.

- **Implementation:** Wrap the async callback body in a try/catch with proper error logging:
  ```typescript
  setImmediate(async () => {
    try {
      // existing anomaly detection logic
    } catch (err) {
      logger.error({ err, shipmentId }, 'Background anomaly detection failed');
    }
  });
  ```
  Alternatively, consider moving this work to a BullMQ job for reliability and retry semantics, which aligns with the existing worker architecture.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] All `setImmediate(async ...)` callbacks have error handling that prevents unhandled rejections.
- [ ] Errors in background anomaly detection are logged with context (shipmentId, error details).
- [ ] The main request/response flow is not affected by background processing failures.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: simulate anomaly detection failure and verify no unhandled rejection.
- [ ] Add test: verify error is logged when background processing fails.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `fix/issue-52-unhandled-async-rejections`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
