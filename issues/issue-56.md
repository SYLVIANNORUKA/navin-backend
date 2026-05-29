## Domain: Documentation

---

### Issue [API-QA] Add missing Swagger documentation for Shipments endpoints

**Tier:** 🟢 Easy

**Description:**
- **Problem:** The `docs/swagger.yaml` spec does not document the Shipments module endpoints (`POST /api/shipments`, `GET /api/shipments`, `GET /api/shipments/:id`, `POST /api/shipments/:id/proof`). Frontend developers and API consumers have no authoritative contract for these critical routes.
- **Implementation:** Add full OpenAPI path definitions under `/api/shipments` in `docs/swagger.yaml` including request bodies, query params, response schemas (200, 401, 403, 404), and auth guards (`bearerAuth`). Reference the existing `Shipment` component schema.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `POST /api/shipments` is fully documented with request body and 201 response.
- [ ] `GET /api/shipments` is documented with query params, pagination `meta`, and 200 response.
- [ ] `GET /api/shipments/:id` is documented with path param and 404 case.
- [ ] `POST /api/shipments/:id/proof` is documented with multipart file upload and 200 response.
- [ ] All shipment endpoints correctly declare `security: [bearerAuth: []]` where applicable.

**Testing Requirements:**
- [ ] Swagger UI renders all new shipment paths without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.
- [ ] Spot-check that documented request/response shapes match the actual controller behavior.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-56-shipments-swagger`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

