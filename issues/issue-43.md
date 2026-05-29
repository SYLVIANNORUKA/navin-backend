## Domain: Shipments

---

### Issue [Bug] Remove duplicate `sendResponse` call in `uploadShipmentProof` controller

**Tier:** 🟢 Easy

**Description:**
- **Problem:** `src/modules/shipments/shipments.controller.ts` lines 94-95 contain two identical `sendResponse(res, 200, true, 'Proof uploaded', shipment)` calls inside the `uploadShipmentProof` handler. The second call will throw an `ERR_HTTP_HEADERS_SENT` error at runtime because headers have already been flushed by the first call, crashing the request.
- **Implementation:** Remove the duplicate `sendResponse` line, keeping only one. The controller should call `sendResponse` exactly once per handler.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `uploadShipmentProof` calls `sendResponse` exactly once.
- [ ] Proof upload endpoint returns 200 with the proof data without runtime errors.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Run `deliveryProof.test.ts` to confirm no regressions.
- [ ] Manually test proof upload via Postman to verify no "headers already sent" error.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `fix/issue-43-duplicate-send-response`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
