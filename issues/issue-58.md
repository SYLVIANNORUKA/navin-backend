## Domain: Documentation

---

### Issue [API-QA] Add missing Swagger documentation for Auth endpoints

**Tier:** 🟢 Easy

**Description:**
- **Problem:** `docs/swagger.yaml` currently documents only `POST /api/auth/signup`, `POST /api/auth/login`, and `POST /api/auth/api-keys`. Missing routes include any refresh-token, logout, or password-reset flows that may exist in `src/modules/auth/auth.routes.ts`.
- **Implementation:** Audit `src/modules/auth/auth.routes.ts`, identify all registered paths, and add their OpenAPI definitions to `docs/swagger.yaml`.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] Every route in `auth.routes.ts` has a corresponding Swagger path entry.
- [ ] Auth endpoints that are public (no `bearerAuth`) explicitly omit the `security` block.
- [ ] Response schemas reflect the actual controller return shapes.
- [ ] Refresh/logout endpoints document cookie or header requirements if applicable.

**Testing Requirements:**
- [ ] Swagger UI renders updated auth paths without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.
- [ ] Route audit list attached to PR.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-58-auth-swagger`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

