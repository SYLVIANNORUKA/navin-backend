## Domain: Shipments

---

### Issue [Bug] Fix broken `shipments.validation.ts` with duplicate and conflicting schema declarations

**Tier:** 🔴 Hard

**Description:**
- **Problem:** `src/modules/shipments/shipments.validation.ts` is in a non-compilable state due to multiple duplicate declarations:
  1. `ShipmentStatus` is imported from two different sources (lines 2-3).
  2. `CreateShipmentBodySchema` is declared **twice** (lines 15-20 and 21-28) with conflicting definitions — one makes `trackingNumber` optional, the other required.
  3. `ShipmentStatusBodySchema` is declared **three times** (lines 40-43, 45-52, 63-73) with different shapes.
  4. `ShipmentsQuerySchema` alias is declared twice (lines 60-61).
  This blocks the entire shipments module from compiling.
- **Implementation:** Consolidate each schema to a single canonical declaration. Per the frontend compatibility guide, `trackingNumber` should be optional (auto-generated on the backend). The `ShipmentStatusBodySchema` should accept `{ status: ShipmentStatus }`. Remove all duplicate declarations and ensure a single `ShipmentStatus` import source. Run `npm run build` to verify.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] Each Zod schema is declared exactly once with no duplicates.
- [ ] `CreateShipmentBodySchema` makes `trackingNumber` optional.
- [ ] `ShipmentStatusBodySchema` is a single schema accepting `{ status }`.
- [ ] `npm run build` compiles without errors.
- [ ] All shipment-related tests pass.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Run shipment test suite (`shipments.test.ts`, `shipments.service.test.ts`, `shipments.pagination.test.ts`) to confirm no regressions.
- [ ] Add validation unit tests for each consolidated schema ensuring correct accept/reject behavior.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `fix/issue-42-shipments-validation-duplicates`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
