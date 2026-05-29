## Domain: Identity

---

### Issue [Bug] Use `sendResponse` wrapper in `deleteUserController` for consistent API envelope

**Tier:** 🟢 Easy

**Description:**
- **Problem:** The `deleteUserController` in `src/modules/users/users.controller.ts` line 22 uses raw `res.json()` to send the response instead of the standardized `sendResponse()` utility. This breaks the API contract — all other endpoints return the `{ success, message, data, meta }` envelope, but user deletion returns a bare JSON object. The frontend's Axios interceptor expects the standard envelope and will fail to extract the response correctly.
- **Implementation:** Replace `res.json(...)` with `sendResponse(res, 200, true, 'User deleted successfully', result)` (or appropriate message). Ensure the import for `sendResponse` is present at the top of the file.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `deleteUserController` uses `sendResponse()` instead of `res.json()`.
- [ ] Response follows the `{ success, message, data }` envelope format.
- [ ] Frontend can parse the delete response using the standard interceptor.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: `DELETE /users/:id` response matches `{ success: true, message: string, data: ... }` shape.
- [ ] Run existing user tests to confirm no regressions.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `fix/issue-55-delete-user-response-envelope`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
