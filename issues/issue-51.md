## Domain: Shipments

---

### Issue [Security] Prevent NoSQL injection via unvalidated query filter spread in shipments controller

**Tier:** 🟡 Medium

**Description:**
- **Problem:** In `src/modules/shipments/shipments.controller.ts` line 23, the controller destructures known query params and spreads the rest into a `filters` variable:
  ```typescript
  const { status, page = 1, limit = 20, origin, destination, ...filters } = query;
  ```
  These `filters` are passed directly into the shipments service and ultimately into a MongoDB query. An attacker can inject arbitrary MongoDB operators (e.g., `$gt`, `$ne`, `$regex`, `$where`) via query string parameters like `?deletedAt[$ne]=null` to bypass soft-delete filters or `?role[$gt]=` to exfiltrate data. This is a classic NoSQL injection vector.
- **Implementation:**
  1. Remove the `...filters` rest spread entirely.
  2. Explicitly whitelist only known, validated query parameters (`status`, `page`, `limit`, `origin`, `destination`, `search`, `cursor`).
  3. Ensure the Zod `ShipmentsQuerySchema` defines every accepted query parameter — any unlisted params should be stripped by Zod's `.strip()` or rejected by `.strict()`.
  4. Pass only validated, typed fields from the Zod output into the service layer.

**Dependencies:**
- Depends on issue-42.md (shipments validation must be fixed first)

**Acceptance Criteria:**
- [ ] No arbitrary query parameters are forwarded to MongoDB queries.
- [ ] Only explicitly defined and validated fields are used in database filters.
- [ ] `ShipmentsQuerySchema` uses `.strict()` or `.strip()` to reject/remove unknown keys.
- [ ] Attempting to pass MongoDB operators in query strings has no effect.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: query with `?deletedAt[$ne]=null` does not bypass soft-delete.
- [ ] Add test: query with `?$where=1` returns 400 or is silently ignored.
- [ ] Add test: valid query parameters continue to work correctly.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `security/issue-51-nosql-injection-prevention`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
