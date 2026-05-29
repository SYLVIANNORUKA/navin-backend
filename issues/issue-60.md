## Domain: Documentation

---

### Issue [API-QA] Add missing Swagger documentation for Analytics and Payments endpoints

**Tier:** 🟢 Easy

**Description:**
- **Problem:** `GET /api/analytics` and `GET /api/payments` are registered in `src/app.ts` but have no representation in `docs/swagger.yaml`. These are critical BI and settlement interfaces.
- **Implementation:** Add OpenAPI path definitions for both routes. Analytics should cover aggregation params (groupBy, dateRange). Payments should cover status filters and pagination.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `GET /api/analytics` documented with query params and aggregation response schema.
- [ ] `GET /api/payments` documented with query params (`status`, `page`, `limit`) and response schema.
- [ ] Both endpoints declare `security: [bearerAuth: []]`.
- [ ] Response shapes match actual controller return values.

**Testing Requirements:**
- [ ] Swagger UI renders new paths without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.
- [ ] Controller audit list attached to PR.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-60-analytics-payments-swagger`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

