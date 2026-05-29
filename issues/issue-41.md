## Domain: Auth

---

### Issue [Bug] Fix duplicate imports and conflicting declarations in `auth.service.ts`

**Tier:** 🔴 Hard

**Description:**
- **Problem:** `src/modules/auth/auth.service.ts` has duplicate import statements that prevent compilation. `UserModel`, `OrganizationModel`, and `blockToken` are each imported twice from the same modules (lines 6 and 8, lines 9 and 11). Additionally, the `TokenPayload` interface declares `jti` twice — once as optional and once as required — which is a TypeScript error. The file cannot compile in its current state, blocking all authentication functionality.
- **Implementation:** Remove the duplicate import lines (keeping one of each). Resolve the `jti` field in `TokenPayload` to a single declaration (it should be required since it is used for token revocation). Verify the file compiles cleanly with `npm run build`.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] Each module is imported exactly once with no duplicate import lines.
- [ ] `TokenPayload.jti` is declared once as a required `string` field.
- [ ] `npm run build` compiles without errors.
- [ ] All existing auth tests continue to pass.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Run full auth test suite (`auth.test.ts`, `auth.controllers.test.ts`) to confirm no regressions.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `fix/issue-41-auth-service-duplicate-imports`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
