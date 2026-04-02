> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Error Handler (`errorHandler.ts` in `shared/middleware/`)

## What to test

The error handler is a single global Express error middleware. Per the project rule, it:
1. Maps custom error classes to HTTP status codes (NotFoundError -> 404, ConflictError -> 409, etc.)
2. Returns a consistent JSON response shape: `{ error: { code, message } }`
3. Returns 500 for unknown errors without exposing internal details
4. Logs the full error before responding

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Error handler middleware | **No** — requires Express error middleware pipeline | No | **Yes** — supertest |

The error handler's behavior depends on Express's error middleware mechanism (4-parameter signature). Testing it in isolation would require mocking `req`, `res`, `next`, and the error — which is fragile and tightly coupled to Express internals. E2E tests naturally exercise the error handler through the real middleware pipeline.

## Setup pattern

Error handler tests are a subset of E2E tests — they verify that different use case errors produce the correct HTTP responses.

```typescript
import { describe, it, expect } from 'vitest';
import request from 'supertest';
import app from '../src/app.js';
// ... DB and auth setup

describe('Error handler', () => {
  it('maps ConflictError to 409 with consistent shape', async () => {
    // Create a user, then try to create another with the same email
    await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'A', email: 'dup@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });

    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'B', email: 'dup@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });

    expect(response.status).toBe(409);
    expect(response.body).toEqual({
      error: {
        code: expect.any(String),
        message: expect.any(String),
      },
    });
  });

  it('maps NotFoundError to 404', async () => {
    const response = await request(app)
      .get('/v1/users/nonexistent-uuid')
      .set('Authorization', `Bearer ${adminToken}`);

    expect(response.status).toBe(404);
    expect(response.body.error).toBeDefined();
  });

  it('maps ValidationError to 422', async () => {
    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'A', email: 'a@t.com', password: 'weak', roles: ['aluno'] });

    expect(response.status).toBe(422);
    expect(response.body.error).toBeDefined();
  });

  it('returns 500 for unknown errors without internal details', async () => {
    // This is harder to trigger naturally — you may need a test-only route
    // that throws an unexpected error, or test it when a real unexpected
    // failure occurs (e.g., DB connection drops)
    // The key assertion: no stack trace, no internal message in response
  });
});
```

## When to skip

- Never skip — the error handler is a critical cross-cutting concern. However, you don't need to test every error class individually if the mapping pattern is consistent (e.g., if all custom errors extend a base class with a `statusCode` property, one test per distinct status code is enough).

## Examples from project

- **Error handler** (planned) — Maps: NotFoundError->404, ConflictError->409, ValidationError->422, UnauthorizedError->401, ForbiddenError->403, unknown->500. **E2E: one test per distinct status code mapping, plus one for unknown error -> 500.**
