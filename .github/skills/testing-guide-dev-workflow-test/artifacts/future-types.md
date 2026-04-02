> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Future Types (not yet present in the project)

This guide provides proactive testing guidance for artifact types common in Express.js hexagonal architectures that are not yet present in the CodeLearn project but are likely to appear as modules are built.

---

## Validation Schemas / DTOs

**Anticipated pattern:** Separate validation schemas (e.g., using Zod, Joi, or class-validator) for request body validation. May live as `*.schema.ts` or `*.dto.ts` files.

**Test layer:** E2E only — one validation wiring test per endpoint.

**What to test:**
- E2E: send invalid payloads and verify 422 status codes
- Do NOT unit test individual schema rules — one E2E test per endpoint proving the validator is wired is enough (per the fundamentals: "validation passthrough")
- **Exception:** security-critical validation (e.g., XSS sanitization, SQL injection prevention) warrants dedicated E2E tests for each attack vector

**When they arrive:** Add E2E tests to the controller test file for each endpoint that uses validation. No separate test file needed.

---

## Domain Value Objects

**Anticipated pattern:** Immutable objects representing domain concepts (e.g., `Email`, `Password`, `Role`). Would live in `domain/entities/` or `domain/value-objects/`.

**Test layer:** Unit (if they contain validation or transformation logic).

**What to test:**
- Creation validation (e.g., `Email.create('invalid')` throws)
- Equality semantics (two value objects with same value are equal)
- Immutability (properties cannot be reassigned)

**When they arrive:** Test alongside domain entities. If the value object is used in an entity, the entity tests may already cover it — avoid duplicate coverage.

---

## Database Migrations

**Anticipated pattern:** TypeORM migration files in `src/shared/migrations/`.

**Test layer:** Integration (real PostgreSQL).

**What to test:**
- Migration applies without errors (up)
- Migration rolls back without errors (down)
- Data integrity is preserved after migration (for data migrations, not just schema changes)

**When they arrive:** Consider a CI step that runs migrations against a clean DB and validates schema matches the expected state. Individual migration tests are valuable only for complex data migrations.

---

## Event Emitters / Domain Events

**Anticipated pattern:** If the project introduces internal domain events (e.g., `UserBlocked` event triggers session invalidation), these may appear as event classes + handlers.

**Test layer:** Unit for handlers with branching; Integration if they trigger DB operations.

**When they arrive:** Test the handler, not the event emission. The event class itself is a data structure — skip it unless it contains logic.

---

## Scheduled Jobs / Cron Tasks

**Anticipated pattern:** The FDD mentions a "periodic job to clean expired sessions." If implemented, these would be standalone functions triggered on a schedule.

**Test layer:** Integration (since they typically interact with the DB).

**When they arrive:** Test the job function directly (not the scheduler). Verify it cleans up the correct data and doesn't affect active records.

---

## HTTP Client Services (External API calls)

**Anticipated pattern:** If the project integrates with external APIs (e.g., payment gateways, notification services), these would be adapter classes implementing driving ports.

**Test layer:** Integration with a fake HTTP server (e.g., MSW or nock).

**When they arrive:** Never call real external APIs in tests. Use a fake HTTP server that returns predefined responses. Test: success path, timeout, error responses, unexpected response shapes.

---

## File Storage Adapters (S3)

**Anticipated pattern:** Per ADR-006, a storage adapter for AWS S3 (production) and local filesystem (development).

**Test layer:** Integration with fake (in-memory or local fs).

**When they arrive:** Mock S3 calls using a local fake (e.g., `s3rver`, in-memory mock, or a simple local fs adapter). Test: upload returns a URL, download retrieves the file, delete removes it, handles errors (file not found, quota exceeded).
