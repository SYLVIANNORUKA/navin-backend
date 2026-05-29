## Domain: Webhooks

---

### Issue [Security] Add authentication and signature verification to Stellar webhook endpoint

**Tier:** 🔴 Hard

**Description:**
- **Problem:** The Stellar webhook endpoint at `POST /api/webhooks/stellar` (defined in `src/modules/webhooks/iot.routes.ts` lines 21-25) has no authentication middleware — neither `requireAuth` nor `requireApiKey`. The `StellarWebhookHeadersSchema` in `src/modules/webhooks/stellar.webhook.validation.ts` defines `x-stellar-signature` as optional and the signature is never verified. This means any external actor can send forged webhook payloads to mark payments as `RELEASED`, `ESCROWED`, or `FAILED`, directly manipulating financial records without authorization.
- **Implementation:**
  1. Make `x-stellar-signature` required in `StellarWebhookHeadersSchema`.
  2. Add a `verifyStellarSignature` middleware that validates the HMAC/signature of the request body against a shared secret (stored in environment variables, e.g., `STELLAR_WEBHOOK_SECRET`).
  3. Add the `STELLAR_WEBHOOK_SECRET` to `src/env.ts` validation schema.
  4. Apply the verification middleware to the Stellar webhook route.
  5. Reject requests with missing or invalid signatures with 401.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `x-stellar-signature` header is required on all Stellar webhook requests.
- [ ] Request body signature is verified against `STELLAR_WEBHOOK_SECRET` using HMAC-SHA256.
- [ ] Requests with missing or invalid signatures receive 401 Unauthorized.
- [ ] Valid signed requests are processed normally.
- [ ] `STELLAR_WEBHOOK_SECRET` is added to `env.ts` Zod schema.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: request without `x-stellar-signature` returns 401.
- [ ] Add test: request with invalid signature returns 401.
- [ ] Add test: request with valid signature processes correctly.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `security/issue-46-stellar-webhook-auth`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
