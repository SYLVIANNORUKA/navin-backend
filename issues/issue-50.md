## Domain: Telemetry

---

### Issue [Security] Add `requireAuth` middleware to `GET /telemetry` route

**Tier:** 🟡 Medium

**Description:**
- **Problem:** The `GET /api/telemetry` route in `src/modules/telemetry/telemetry.routes.ts` lines 10-14 only applies `validateRequest` middleware but omits `requireAuth`. Telemetry data — including GPS coordinates, temperature readings, humidity, battery levels, and Stellar anchor hashes — is publicly accessible. This exposes sensitive shipment tracking and environmental monitoring data to unauthenticated users.
- **Implementation:** Add `requireAuth` middleware to the `GET /telemetry` route. Scope telemetry queries to shipments belonging to the authenticated user's organization to prevent cross-organization data access.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `GET /telemetry` route includes `requireAuth` middleware.
- [ ] Unauthenticated requests receive 401.
- [ ] Telemetry listing is scoped to shipments within the user's organization.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: unauthenticated `GET /telemetry` returns 401.
- [ ] Add test: authenticated user only sees telemetry for their organization's shipments.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `security/issue-50-telemetry-auth-guard`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
