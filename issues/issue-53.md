## Domain: Infrastructure

---

### Issue [Security] Fix rate limiter bypass for requests with invalid Bearer tokens

**Tier:** 🟡 Medium

**Description:**
- **Problem:** The rate limiter middleware in `src/shared/middleware/rateLimiter.ts` line 17 uses a `skip` function that checks for the presence of a `Bearer ` prefix in the `Authorization` header:
  ```typescript
  skip: hasAuthenticatedBearerToken,
  ```
  The `hasAuthenticatedBearerToken` function only validates the header format (starts with `Bearer `), not whether the token is actually valid. An attacker can bypass rate limiting on **all** endpoints by sending any request with `Authorization: Bearer fake-token-here`. This defeats the purpose of rate limiting for brute-force protection on login and other sensitive endpoints.

- **Implementation:**
  1. Remove the `skip` logic from the rate limiter entirely, OR
  2. Apply separate rate limiters: a strict one for unauthenticated routes (login, signup) and a relaxed one for authenticated routes. The authentication check should happen via `requireAuth` middleware *before* determining rate limit tiers, not via a header format check.
  3. If differentiated rate limits are needed, use the validated `req.user` property (set by `requireAuth`) rather than raw header inspection.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] Requests with invalid Bearer tokens are rate-limited normally.
- [ ] Only requests that pass full JWT verification can qualify for relaxed rate limits (if applicable).
- [ ] Login endpoint remains protected by rate limiting regardless of headers.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: request with `Authorization: Bearer invalid` is still rate-limited.
- [ ] Add test: rapid login attempts trigger rate limit (429).
- [ ] Run `login-rate-limit.test.ts` to confirm no regressions.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `security/issue-53-rate-limiter-bypass`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
