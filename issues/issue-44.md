## Domain: Shipments

---

### Issue [Refactor] Remove manual try/catch from `uploadShipmentProof` controller

**Tier:** 🟢 Easy

**Description:**
- **Problem:** `src/modules/shipments/shipments.controller.ts` lines 76-98 wrap the `uploadShipmentProof` handler in a manual `try/catch` block that catches all errors and returns a generic 500 response. Per AGENTS.md convention, controllers must NOT use manual `try/catch` — they should be wrapped in `asyncHandler` and throw `AppError` instances, letting the global error middleware handle formatting and status codes. The current approach swallows meaningful error messages and returns unhelpful "Failed to upload proof" responses for all failure modes.
- **Implementation:** Remove the `try/catch` block. Wrap the route handler with `asyncHandler` in the route definition (if not already). Let the service layer throw typed `AppError` instances that the global error middleware will format consistently.

**Dependencies:**
- Depends on issue-43.md (duplicate sendResponse must be fixed first)

**Acceptance Criteria:**
- [ ] `uploadShipmentProof` controller has no manual `try/catch`.
- [ ] Route uses `asyncHandler(uploadShipmentProof)` wrapper.
- [ ] Service-layer errors propagate as `AppError` with correct status codes.
- [ ] Global error middleware handles all error formatting.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Run `deliveryProof.test.ts` to confirm proper error responses.
- [ ] Add test case for error propagation (e.g., missing file) to verify correct status code.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `refactor/issue-44-remove-trycatch-proof-upload`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
