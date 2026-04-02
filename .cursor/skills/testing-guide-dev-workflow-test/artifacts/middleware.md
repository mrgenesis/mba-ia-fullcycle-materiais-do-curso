> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Middleware (`*.ts` in `shared/middleware/`)

## What to test

- **Auth middleware** — rejects requests without token (401), rejects expired/invalid tokens (401), rejects tokens with invalidated sessions (401), passes valid requests through
- **RBAC middleware** — rejects requests from users without required role (403), allows users with the correct role
- **Future middleware** — rate limiting, request logging, CORS configuration

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Simple middleware (delegates to lib or checks a flag) | No | No | **Yes** — supertest |
| Complex middleware (multi-step auth: JWT verify + session DB lookup) | No | Optionally (see note) | **Yes** — supertest |

**Why E2E, not unit:** Middleware behavior is defined by how it interacts with the HTTP request/response cycle. Testing middleware in isolation requires mocking `req`, `res`, and `next` — which creates fragile tests coupled to Express internals. Supertest exercises the real HTTP stack.

**Exception for complex middleware:** The auth middleware in this project does JWT verification + a database lookup for active sessions. If you want to test the DB session lookup in isolation (e.g., expired session, invalidated session), an integration test can be valuable. But the E2E test is still mandatory.

## Setup pattern

```typescript
import { describe, it, expect, beforeAll, afterAll, afterEach } from 'vitest';
import request from 'supertest';
import app from '../src/app.js';
import jwt from 'jsonwebtoken';
// ... DB setup (see references/external-systems.md)

// Helper: generate a valid JWT for testing
function generateTestToken(payload: object, secret = TEST_JWT_SECRET): string {
  return jwt.sign(payload, secret, { expiresIn: '15m' });
}

describe('Auth middleware', () => {
  it('returns 401 without Authorization header', async () => {
    const response = await request(app).get('/v1/users');
    expect(response.status).toBe(401);
  });

  it('returns 401 with expired token', async () => {
    const expired = jwt.sign({ sub: 'user-1' }, TEST_JWT_SECRET, { expiresIn: '0s' });
    const response = await request(app)
      .get('/v1/users')
      .set('Authorization', `Bearer ${expired}`);
    expect(response.status).toBe(401);
  });

  it('returns 401 with invalidated session', async () => {
    // Create user and session, then invalidate the session
    // ... (setup: insert user + session, mark session inactive)
    const token = generateTestToken({ sub: userUuid, sessionId: invalidatedSessionId });
    const response = await request(app)
      .get('/v1/users')
      .set('Authorization', `Bearer ${token}`);
    expect(response.status).toBe(401);
  });
});

describe('RBAC middleware', () => {
  it('returns 403 when user lacks required role', async () => {
    const studentToken = generateTestToken({ sub: 'user-1', roles: ['aluno'] });
    // POST /v1/users requires admin role
    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${studentToken}`)
      .send({ name: 'A', email: 'a@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });
    expect(response.status).toBe(403);
  });

  it('allows requests from users with the required role', async () => {
    const adminToken = generateTestToken({ sub: 'admin-1', roles: ['admin'] });
    const response = await request(app)
      .post('/v1/users')
      .set('Authorization', `Bearer ${adminToken}`)
      .send({ name: 'A', email: 'a@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });
    expect(response.status).not.toBe(403);
  });
});
```

## When to skip

- Never skip E2E tests for auth/RBAC middleware — these are security boundaries
- For non-security middleware (e.g., request ID generation, logging), E2E tests are optional — verify in the response headers if relevant

## Examples from project

- **Auth middleware** (planned) — JWT verification + session lookup in PostgreSQL. **E2E: test all rejection paths (no token, expired, invalid, invalidated session).**
- **RBAC middleware** (planned) — Role check against required roles per route. **E2E: test 403 for insufficient role, 200 for correct role.**
