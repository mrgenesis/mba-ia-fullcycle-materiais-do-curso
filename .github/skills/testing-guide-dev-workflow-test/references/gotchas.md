> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Stack-Specific Gotchas & Pitfalls

## TypeORM + PostgreSQL

### `repository.delete({})` throws on empty criteria
TypeORM throws an error when you call `delete({})` or `remove()` with empty criteria. For test cleanup, always use raw SQL:
```typescript
await dataSource.query('TRUNCATE TABLE users RESTART IDENTITY CASCADE');
```

### `synchronize: true` without `dropSchema: true` causes stale schema
If you change entity decorators between test runs, `synchronize: true` alone may not drop columns or constraints that were removed. Always use `dropSchema: true` in test DataSource config for a clean slate.

### `select: false` columns not included in find results
When testing TypeORM models with `@Column({ select: false })` (e.g., password hash), a normal `findOne` won't return that column. To verify the column was persisted, use:
```typescript
const withPassword = await repo.findOne({
  where: { email: 'test@t.com' },
  select: ['id', 'email', 'passwordHash'], // explicitly include
});
```

### Soft delete and `findOne` behavior
After `softRemove()`, the entity's `deletedAt` is set. Normal queries (`find`, `findOne`, `findOneBy`) exclude soft-deleted records by default. Use `withDeleted: true` to include them in assertions:
```typescript
const found = await repo.findOne({
  where: { id: softDeletedId },
  withDeleted: true,
});
expect(found?.deletedAt).toBeDefined();
```

### Foreign key constraint ordering in TRUNCATE
When truncating tables with foreign key relationships, either:
1. Truncate in reverse dependency order (children before parents)
2. Use CASCADE: `TRUNCATE TABLE users RESTART IDENTITY CASCADE`

CASCADE is simpler but truncates related tables too — be aware if tests share tables.

---

## Vitest + ESM + TypeScript

### `.js` extensions required in test imports
Since the project uses ESM (`"type": "module"`), all imports in test files must use `.js` extensions, just like in source files:
```typescript
import { User } from '../../domain/entities/user.entity.js'; // correct
// import { User } from '../../domain/entities/user.entity';  // ERROR
```

### `vi.mock()` hoisting with ESM
In ESM mode, `vi.mock()` is hoisted to the top of the file. If you need dynamic mocking (different behavior per test), use `vi.fn()` on port interface objects instead of `vi.mock()` on module paths. This is more explicit and avoids hoisting surprises.

### Module resolution with `vitest`
Vitest supports TypeScript ESM natively. If you encounter module resolution errors, ensure:
1. `tsconfig.json` has `"module": "NodeNext"` and `"moduleResolution": "NodeNext"`
2. Test files are included in tsconfig: `"include": ["src/**/*.ts", "tests/**/*.ts"]`
3. If using path aliases, install `vite-tsconfig-paths` and add it to vitest config

### Test isolation and shared state
Vitest runs test files in parallel by default. Each file gets its own worker, but tests within a file run sequentially. If integration tests share a database, ensure:
- Each test file creates its own DataSource (or uses a shared one with proper cleanup)
- `afterEach` truncates all relevant tables
- `afterAll` destroys the DataSource connection

For E2E tests that share a single app instance, use `--pool=forks --poolOptions.forks.singleFork` or run E2E files sequentially to avoid port conflicts.

---

## InversifyJS

### Container resolution errors are runtime-only
TypeScript will happily compile `container.get<UserRepository>(TYPES.UserRepository)` even if no binding exists. The error only appears at runtime: `No matching bindings found for serviceIdentifier: Symbol(User.UserRepository)`. This is why compilation tests are mandatory.

### `toDynamicValue` factory errors
If a `toDynamicValue` factory throws (e.g., because it references a DataSource that isn't initialized), the error message may be cryptic. In compilation tests, wrap resolution in try/catch and log the actual error:
```typescript
it('resolves UserRepository', () => {
  expect(() => container.get(USER_TYPES.UserRepository)).not.toThrow();
});
```

### Singleton scope and test pollution
Repositories bound as singletons persist across `.get()` calls within the same container. If a test modifies a singleton's internal state, subsequent tests see the modified state. For integration tests, either:
1. Create a fresh container per test file (recommended)
2. Reset repository state in `afterEach`

---

## Express.js 5 + supertest

### `app` vs `server` for supertest
Supertest works with the Express `app` instance, NOT the listening server. Import `app` (from `app.ts`), not the result of `app.listen()`:
```typescript
import app from '../src/app.js';
import request from 'supertest';
const response = await request(app).get('/v1/users');
```
If you import `server.ts`, the server starts listening on port 3000 and may conflict with the dev server.

### Express 5 async error handling
Express 5 natively catches errors from `async` route handlers — no need for `express-async-errors`. However, the error handler middleware must still have the 4-parameter signature `(error, req, res, next)` to be recognized as an error handler by Express.

### Response body on 204 No Content
Supertest returns an empty body for 204 responses. Don't assert on `response.body` for 204 — it will be `{}` or undefined:
```typescript
expect(response.status).toBe(204);
// Don't do: expect(response.body).toEqual(...)
```

---

## JWT + argon2

### JWT `expiresIn` as string vs number
`jsonwebtoken`'s `sign()` accepts `expiresIn` as a string (e.g., `'15m'`) or a number (seconds). In tests, use `'0s'` or `0` to create immediately-expired tokens for testing expiration logic:
```typescript
const expired = jwt.sign({ sub: 'user-1' }, secret, { expiresIn: '0s' });
// Wait a tick, then verify it's expired
await new Promise(r => setTimeout(r, 1000));
expect(() => jwt.verify(expired, secret)).toThrow(/expired/);
```

### argon2 test performance
argon2 with default parameters is intentionally slow (for security). In tests, use minimum cost parameters to avoid slow test suites:
```typescript
const TEST_ARGON2_OPTIONS = { timeCost: 1, memoryCost: 1024, parallelism: 1 };
```
Configure the use case to accept these options via constructor injection or environment config, so tests can override them without changing production settings.
