## Domain: Documentation

---

### Issue [API-QA] Document Stellar proof-of-delivery webhook in Swagger

**Tier:** 🟢 Easy

**Description:**
- **Problem:** The Stellar webhook endpoint (`POST /api/webhooks/stellar`) receives blockchain proof-of-delivery callbacks but is not present in `docs/swagger.yaml`. Integrators and auditors have no documented contract for this critical settlement trigger.
- **Implementation:** Add an OpenAPI path definition for `POST /api/webhooks/stellar` in `docs/swagger.yaml`. Document the expected Stellar payload (tx hash, memo, event type), signature verification requirements, and response codes.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `POST /api/webhooks/stellar` present in `docs/swagger.yaml`.
- [ ] Request body documents Stellar-specific fields (tx hash, memo, event type).
- [ ] `security` block uses `apiKeyAuth` or notes HMAC signature verification if applicable.
- [ ] 200 and 400 response schemas documented.
- [ ] Cross-references `src/modules/webhooks/stellar.webhook.service.ts` for field accuracy.

**Testing Requirements:**
- [ ] Swagger UI renders the new path without YAML syntax errors.
- [ ] `npm run build` passes with zero warnings.
- [ ] Payload fields verified against actual service handler.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-65-stellar-webhook-swagger`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

