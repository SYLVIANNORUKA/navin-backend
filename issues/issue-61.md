## Domain: Documentation

---

### Issue [API-QA] Add missing Swagger documentation for Health endpoint

**Tier:** 🟢 Easy

**Description:**
- **Problem:** `GET /api/health` is a public route wired in `src/app.ts` but is not listed in `docs/swagger.yaml`. It is the first endpoint any DevOps engineer or load balancer will hit, yet it has no documented contract.
- **Implementation:** Add an OpenAPI path definition for `GET /api/health` under `paths:`. Document the 200 response shape (uptime, timestamp, status fields) and note that it is public (no `security` block).

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `GET /api/health` present in `docs/swagger.yaml`.
- [ ] No `security` block attached (public endpoint).
- [ ] 200 response schema documents all fields returned by the health controller.

**Testing Requirements:**
- [ ] Swagger UI renders the health path without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.
- [ ] Response fields match `src/modules/health/health.controller.ts`.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-61-health-swagger`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

