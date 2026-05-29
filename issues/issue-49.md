## Domain: Shipments

---

### Issue [Security] Add `requireAuth` middleware to `GET /shipments` route

**Tier:** 🟡 Medium

**Description:**
- **Problem:** The `GET /api/shipments` route in `src/modules/shipments/shipments.routes.ts` line 30 only applies `validateRequest` middleware but omits `requireAuth` and `requireRole`. This means shipment data — including tracking numbers, origins, destinations, enterprise/logistics IDs, and Stellar transaction hashes — is publicly accessible to any unauthenticated request. Per AGENTS.md: "Every new route MUST use `requireAuth` and `requireRole` unless there is an explicit requirement for public access."
- **Implementation:** Add `requireAuth` middleware to the `GET /shipments` route. Consider adding `requireRole` to restrict listing to appropriate roles (e.g., `ADMIN`, `MANAGER`, `VIEWER`). Additionally, scope the query to the authenticated user's `organizationId` to prevent cross-organization data leakage.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `GET /shipments` route includes `requireAuth` middleware.
- [ ] Unauthenticated requests receive 401.
- [ ] Shipment listing is scoped to the user's organization.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: unauthenticated `GET /shipments` returns 401.
- [ ] Add test: authenticated user only sees shipments from their organization.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `security/issue-49-shipments-auth-guard`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
