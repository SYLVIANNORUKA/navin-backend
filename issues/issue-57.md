## Domain: Documentation

---

### Issue [API-QA] Add missing Swagger documentation for Users endpoints

**Tier:** 🟢 Easy

**Description:**
- **Problem:** The Users module routes (`GET /api/users`, `POST /api/users`, `POST /api/users/team`, `POST /api/users/invitations`, `GET /api/users/invitations/verify`, `POST /api/users/invitations/accept`, `DELETE /api/users/:id`) are absent from `docs/swagger.yaml`.
- **Implementation:** Add complete OpenAPI path definitions for every route in `src/modules/users/users.routes.ts` to `docs/swagger.yaml`. Include Zod-validated request shapes, response envelopes, and RBAC role annotations.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] All 7 user routes are present in the Swagger spec.
- [ ] Request bodies reference the correct Zod schema shapes (e.g., `CreateUserBodySchema`).
- [ ] Response envelopes follow the standard `{ success, message, data, meta }` format.
- [ ] Role requirements (ADMIN, MANAGER, SUPER_ADMIN) are noted in endpoint `description` fields.

**Testing Requirements:**
- [ ] Swagger UI renders all new user paths without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.
- [ ] Cross-reference routes file to confirm no endpoint is missed.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-57-users-swagger`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

