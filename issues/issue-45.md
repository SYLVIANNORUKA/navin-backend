## Domain: Identity

---

### Issue [Security] Prevent privilege escalation via arbitrary role assignment in signup

**Tier:** 🔴 Hard

**Description:**
- **Problem:** `src/modules/auth/auth.validation.ts` line 8 allows the `role` field in the signup request body to be any value from the enum `['SUPER_ADMIN', 'ADMIN', 'MANAGER', 'VIEWER', 'CUSTOMER']`. Since the signup endpoint is unauthenticated, any anonymous user can self-assign `SUPER_ADMIN` or `ADMIN` roles during registration, granting themselves full system access. This is a critical privilege escalation vulnerability.
- **Implementation:** Remove the `role` field from `SignupBodySchema` entirely, or restrict it to only `CUSTOMER` / `VIEWER`. The signup service should assign a safe default role (e.g., `VIEWER` or `CUSTOMER`). Admin and manager roles should only be assignable by authenticated admins via the user management or invitation endpoints. If the business requires org-creator roles, implement a separate "create organization" flow with controlled role assignment.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `SignupBodySchema` does not accept `SUPER_ADMIN`, `ADMIN`, or `MANAGER` as role values.
- [ ] Signup service assigns a safe default role when no role is provided.
- [ ] Existing admin-only user creation endpoints (`POST /users`, invitations) retain full role assignment capability.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: signup with `role: 'SUPER_ADMIN'` returns 400 validation error.
- [ ] Add test: signup without `role` field assigns default role.
- [ ] Verify admin user creation endpoint still allows all roles.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `security/issue-45-prevent-role-escalation`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
