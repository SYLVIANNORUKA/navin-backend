## Domain: Identity

---

### Issue [Security] Add authentication guard to `POST /users` endpoint

**Tier:** 🟡 Medium

**Description:**
- **Problem:** The `POST /users` route in `src/modules/users/users.routes.ts` lines 27-31 calls `createUserController` without any `requireAuth` or `requireRole` middleware. This means any unauthenticated user can create new user accounts in the system. Per AGENTS.md: "Every new route MUST use `requireAuth` and `requireRole` unless there is an explicit requirement for public access." User creation should be restricted to administrators.
- **Implementation:** Add `requireAuth` and `requireRole([UserRole.SUPER_ADMIN, UserRole.ADMIN])` middleware to the `POST /users` route. Ensure the controller reads `organizationId` from the authenticated user's token payload rather than relying on request body.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `POST /users` route includes `requireAuth` middleware.
- [ ] `POST /users` route includes `requireRole` restricting to `SUPER_ADMIN` and `ADMIN`.
- [ ] Unauthenticated requests receive 401.
- [ ] Non-admin authenticated requests receive 403.
- [ ] Admin-authenticated requests create users successfully.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: unauthenticated `POST /users` returns 401.
- [ ] Add test: authenticated non-admin `POST /users` returns 403.
- [ ] Add test: authenticated admin `POST /users` creates user successfully.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `security/issue-47-users-auth-guard`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
