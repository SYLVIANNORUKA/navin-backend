## Domain: Identity

---

### Issue [Bug] Fix inconsistent password hashing between signup and invitation acceptance flows

**Tier:** 🟡 Medium

**Description:**
- **Problem:** Password hashing is performed in two places, creating inconsistent behavior:
  1. **Signup flow** (`src/modules/auth/auth.service.ts` line 50): The service explicitly hashes the password with bcrypt before calling `UserModel.create({ passwordHash })`.
  2. **Mongoose pre-save hook** (`src/modules/users/users.model.ts` lines 26-33): The `UserSchema.pre('save')` hook also hashes `this.passwordHash` if it has been modified.
  
  During signup, the password is hashed **twice** — once in the service and once by the pre-save hook — resulting in a double-hashed value that can never be verified at login.
  
  During invitation acceptance (`src/modules/users/users.service.ts` line 158), the raw password is assigned to `passwordHash` and only hashed once by the pre-save hook, which is correct but inconsistent.
  
  Additionally, `createTeamMember` (`src/modules/users/users.service.ts` line 27) sets `passwordHash: ''`, which is stored as an empty string. Depending on bcrypt version, `bcrypt.compare(input, '')` may behave unexpectedly.

- **Implementation:** Choose one hashing strategy and apply it consistently:
  - **Option A (Recommended):** Remove the pre-save hook. Hash exclusively in the service layer before calling the model. This follows the AGENTS.md principle of keeping business logic in services.
  - **Option B:** Remove service-layer hashing. Let the pre-save hook handle all hashing. Rename the field to `password` at the model level to avoid confusion.
  
  For `createTeamMember`, use a random secure placeholder hash (or a `null`/`undefined` field with schema adjustment) instead of an empty string.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] Password is hashed exactly once in all flows (signup, invitation, team member creation).
- [ ] `bcrypt.compare` works correctly for login after signup.
- [ ] `bcrypt.compare` works correctly for login after invitation acceptance.
- [ ] Team members created without a password cannot authenticate until they set one.
- [ ] Proper HTTP status codes and our standard JSON response wrapper are used.
- [ ] Edge cases (e.g., missing data, unauthorized roles) are handled gracefully.

**Testing Requirements:**
- [ ] Add test: signup → login succeeds with correct password.
- [ ] Add test: invitation acceptance → login succeeds with correct password.
- [ ] Add test: team member without password → login fails gracefully.
- [ ] Unit tests written for the core logic (target 80%+ coverage).
- [ ] External API calls or database connections are mocked in unit tests.
- [ ] Postman collection or Swagger spec updated (if this adds/modifies an endpoint).

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `fix/issue-48-password-hashing-consistency`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.
- [ ] Screenshot of passing Jest terminal logs is attached to the PR.
- [ ] Database migrations/seed scripts updated (if applicable).
