## Domain: Documentation

---

### Issue [API-QA] Document Socket.IO real-time events and payload schemas

**Tier:** 🟡 Medium

**Description:**
- **Problem:** The backend broadcasts real-time events (telemetry, status updates, anomalies) via Socket.IO, but there is no documentation for event names, payload shapes, or authentication flow. Frontend engineers must reverse-engineer the websocket contract from source code.
- **Implementation:** Create a new `docs/websockets.md` file documenting: (1) connection URL and auth handshake, (2) emitted event catalog with payload schemas, (3) client subscription patterns, (4) error handling. Reference the Socket.IO setup in `src/infra/socket/`.

**Dependencies:**
- Depends on None

**Acceptance Criteria:**
- [ ] `docs/websockets.md` created and committed.
- [ ] All emitted event names listed with descriptions.
- [ ] Payload schemas provided as TypeScript interfaces or JSON examples.
- [ ] Auth handshake (token verification) documented.
- [ ] Disconnect/cleanup behavior noted.

**Testing Requirements:**
- [ ] Markdown renders correctly in GitHub preview.
- [ ] Event list cross-referenced against `src/infra/socket/` event emitters.

**PR Checklist:**
- [ ] Branch is named conventionally (e.g., `docs/issue-64-websocket-docs`).
- [ ] `npm run lint` and `npm run build` pass with zero warnings.

