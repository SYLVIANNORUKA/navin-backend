## Domain: Documentation

---

### Issue [API-QA] Standardize error response schemas across all Swagger paths

**Tier:** 🟡 Medium

**Description:**
- **Problem:** Error responses in `docs/swagger.yaml` are inconsistent. Some endpoints document `400` with no schema, others with a bare object, and none explicitly reference the standardized `{ success: false, message: string, data: null }` envelope used by the `errorMiddleware`.
- **Implementation:** Define a reusable `ErrorResponse` component schema under `components/schemas`. Update every path in `docs/swagger.yaml` to reference it for `400`, `401`, `403`, `404`, and `422` responses.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `components/schemas/ErrorResponse` defined with `success`, `message`, `data` fields.
- [ ] All `400` responses reference `ErrorResponse`.
- [ ] All `401` responses reference `ErrorResponse`.
- [ ] All `403` responses reference `ErrorResponse`.
- [ ] All `404` and `422` responses reference `ErrorResponse`.

**Testing Requirements:**
- [ ] Swagger UI validates all updated paths without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-62-standardize-error-schemas`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

