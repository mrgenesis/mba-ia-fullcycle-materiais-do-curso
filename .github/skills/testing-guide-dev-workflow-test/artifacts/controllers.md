> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Controllers (`Router` in `controllers/`)

## What to test

Controllers in this project are **procedural Express Router functions** — not classes. Per the controller rule, they follow the pattern: extract data from request -> call use case -> return response. They contain NO business logic.

What to validate via E2E:
- **HTTP status codes** — correct codes for success and each error scenario (200, 201, 204, 401, 403, 404, 409, 422)
- **Response body shape** — JSON structure matches the API contract
- **Request data extraction** — correct fields from `req.body`, `req.params`, `req.query` reach the use case
- **Error delegation** — errors propagated via `next(error)` are handled by the error handler middleware

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Any controller (Router handler) | **No** — never unit test | No | **Yes** — supertest |

**Why no unit tests:** Controllers are thin delegation. A unit test would mock the use case and assert `res.status(201).json(result)` was called — that's a mirror test (copying the implementation). The E2E test validates the same thing by actually making an HTTP request and checking the response.

## Setup pattern

Controller tests are written as E2E tests using supertest. See `artifacts/middleware.md` for the full E2E setup pattern — controller tests share the same infrastructure.

```typescript
import { describe, it, expect, beforeAll, afterAll, afterEach } from 'vitest';
import request from 'supertest';
import app from '../src/app.js';
// ... DB setup (see references/external-systems.md)

describe('POST /v1/users', () => {
  it('returns 201 with user data (without password)', async () => {
    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'Maria', email: 'maria@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });

    expect(response.status).toBe(201);
    expect(response.body).toHaveProperty('id'); // uuid
    expect(response.body).toHaveProperty('email', 'maria@t.com');
    expect(response.body).not.toHaveProperty('password');
    expect(response.body).not.toHaveProperty('passwordHash');
  });

  it('returns 409 when email already exists', async () => {
    // Create first user
    await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'A', email: 'dup@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });

    // Attempt duplicate
    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'B', email: 'dup@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });

    expect(response.status).toBe(409);
    expect(response.body.error.code).toBeDefined();
  });

  it('returns 422 when password violates policy', async () => {
    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'A', email: 'a@t.com', password: 'weak', roles: ['aluno'] });

    expect(response.status).toBe(422);
  });

  it('returns 403 when caller is not admin', async () => {
    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${studentToken}`)
      .send({ name: 'A', email: 'a@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });

    expect(response.status).toBe(403);
  });

  it('returns 401 without authentication', async () => {
    const response = await request(app)
      .post('/v1/users')
      .send({ name: 'A', email: 'a@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });

    expect(response.status).toBe(401);
  });
});
```

## When to skip

- Never skip controller E2E tests — they are the ONLY test layer for controllers
- However, you do NOT need a separate E2E test for every possible input combination if the use case unit tests already cover the branch. One E2E test per error status code is sufficient to prove the wiring works.

## Examples from project

- **Auth routes** (POST /v1/auth/login, POST /v1/auth/refresh, POST /v1/auth/logout) — E2E tests for each status code documented in the FDD
- **User routes** (POST /v1/users, PATCH /v1/users/:id/roles, PATCH /v1/users/:id/status, GET /v1/users/:id, GET /v1/users) — E2E tests for success and error scenarios per endpoint
