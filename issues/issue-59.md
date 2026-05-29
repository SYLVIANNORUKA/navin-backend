## Domain: Documentation

---

### Issue [API-QA] Add missing Swagger documentation for Telemetry and Anomalies endpoints

**Tier:** 🟢 Easy

**Description:**
- **Problem:** `GET /api/telemetry` and `GET /api/anomalies` are wired in `src/app.ts` but completely missing from `docs/swagger.yaml`. Consumers have no documented contract for querying telemetry time-series or anomaly alerts.
- **Implementation:** Add OpenAPI path definitions for both endpoints. Telemetry should document query filters (shipmentId, date range, pagination). Anomalies should document severity/type filters.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `GET /api/telemetry` documented with query params (`shipmentId`, `from`, `to`, `page`, `limit`).
- [ ] `GET /api/anomalies` documented with query params (`shipmentId`, `severity`, `type`, `resolved`).
- [ ] Both endpoints declare `security: [bearerAuth: []]`.
- [ ] Response schemas reference or extend the existing `Anomaly` component schema.

**Testing Requirements:**
- [ ] Swagger UI renders new paths without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.
- [ ] Parameters verified against actual controller query parsing logic.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-59-telemetry-anomalies-swagger`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

