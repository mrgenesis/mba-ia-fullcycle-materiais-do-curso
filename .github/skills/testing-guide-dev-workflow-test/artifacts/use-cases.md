> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Use Cases (`*.usecase.ts` in `services/`)

## What to test

- **Branch logic** — all conditional paths (e.g., LoginUseCase: user not found, wrong password, account blocked, valid login)
- **Business rule enforcement** — e.g., CreateUser validates password policy, checks email uniqueness
- **Error throwing** — correct custom error types for each failure scenario
- **Side effects orchestration** — e.g., BlockUserUseCase updates status AND invalidates all sessions
- **Configured library contracts** — JWT token generation with correct claims/expiration, argon2 hash verification
- **DB query contracts** — correct data persisted, correct data retrieved, mappings are accurate

## Layer assignment

Use cases are the most varied artifact type — the test layer depends on their characteristics:

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Branching logic only (no DB, no external system) | **Yes** — mock repo ports | No | No |
| DB access only (no branching) | No | **Yes** — real PostgreSQL | No |
| Branching + DB access | **Yes** — mock repo ports for branches | **Yes** — real DB for query contract | No |
| Uses configured lib (JWT, argon2) | **Yes** — real lib with test config | No | No |
| Branching + DB + configured lib | **Yes** — real libs, mock repo ports | **Yes** — real DB | No |

**Critical:** When a use case needs BOTH unit and integration tests, each layer tests something different. The unit test proves the branching logic is correct (with mocked repos). The integration test proves the DB contract is correct (with real PostgreSQL). Neither substitutes the other.

## Setup pattern — Unit test (branching + configured lib)

```typescript
import { describe, it, expect, beforeEach } from 'vitest';
import { LoginUseCase } from '../src/user/services/login.usecase.js';
import jwt from 'jsonwebtoken';

// Mock the repository port — NOT the concrete TypeORM implementation
const mockUserRepo = {
  findByEmail: vi.fn(),
};
const mockSessionRepo = {
  create: vi.fn(),
};

// Use REAL jwt with test config — configured lib, never mock
const TEST_JWT_SECRET = 'test-secret-key-for-testing';
const TEST_JWT_EXPIRATION = '15m';

let useCase: LoginUseCase;

beforeEach(() => {
  vi.clearAllMocks();
  useCase = new LoginUseCase(
    mockUserRepo,
    mockSessionRepo,
    { secret: TEST_JWT_SECRET, expiration: TEST_JWT_EXPIRATION },
  );
});

describe('LoginUseCase', () => {
  it('throws UnauthorizedError when user not found', async () => {
    mockUserRepo.findByEmail.mockResolvedValue(null);
    await expect(useCase.execute({ email: 'x@y.com', password: 'any' }))
      .rejects.toThrow(/invalid credentials/i);
  });

  it('throws UnauthorizedError when password is wrong', async () => {
    mockUserRepo.findByEmail.mockResolvedValue({
      uuid: '123', passwordHash: 'wrong-hash', status: 'active', roles: ['aluno'],
    });
    // argon2.verify will fail against the wrong hash — use real argon2
    await expect(useCase.execute({ email: 'x@y.com', password: 'wrong' }))
      .rejects.toThrow(/invalid credentials/i);
  });

  it('throws ForbiddenError when account is blocked', async () => {
    mockUserRepo.findByEmail.mockResolvedValue({
      uuid: '123', passwordHash: validHash, status: 'blocked', roles: ['aluno'],
    });
    await expect(useCase.execute({ email: 'x@y.com', password: 'correct' }))
      .rejects.toThrow(/blocked/i);
  });

  it('returns valid JWT tokens on successful login', async () => {
    mockUserRepo.findByEmail.mockResolvedValue(validUser);
    mockSessionRepo.create.mockResolvedValue({ id: 'session-1' });

    const result = await useCase.execute({ email: 'x@y.com', password: 'correct' });

    // Verify the token is a real JWT with correct claims
    const decoded = jwt.verify(result.accessToken, TEST_JWT_SECRET) as jwt.JwtPayload;
    expect(decoded.sub).toBe(validUser.uuid);
    expect(decoded.roles).toEqual(validUser.roles);
  });
});
```

## Setup pattern — Integration test (DB contract)

```typescript
import { describe, it, expect, beforeAll, afterAll, afterEach } from 'vitest';
import { DataSource } from 'typeorm';
import { CreateUserUseCase } from '../src/user/services/create-user.usecase.js';
import { TypeOrmUserRepository } from '../src/user/repositories/typeorm-user.repository.js';

let dataSource: DataSource;
let useCase: CreateUserUseCase;

beforeAll(async () => {
  dataSource = new DataSource({
    type: 'postgres',
    host: 'localhost', port: 5432,
    username: 'lms', password: 'lms', database: 'lms_test',
    entities: [/* all relevant models */],
    synchronize: true,
    dropSchema: true,
  });
  await dataSource.initialize();

  const userRepo = new TypeOrmUserRepository(dataSource);
  useCase = new CreateUserUseCase(userRepo);
});

afterEach(async () => {
  await dataSource.query('TRUNCATE TABLE users RESTART IDENTITY CASCADE');
});

afterAll(async () => {
  await dataSource.destroy();
});

describe('CreateUserUseCase — integration', () => {
  it('persists user with correct data and returns without password', async () => {
    const result = await useCase.execute({
      name: 'Maria', email: 'maria@test.com',
      password: 'Str0ng!Pass', roles: ['aluno'],
    });

    expect(result.uuid).toBeDefined();
    expect(result.email).toBe('maria@test.com');
    expect(result).not.toHaveProperty('passwordHash');
  });

  it('throws ConflictError on duplicate email', async () => {
    await useCase.execute({ name: 'A', email: 'dup@t.com', password: 'Str0ng!Pass', roles: ['aluno'] });
    await expect(
      useCase.execute({ name: 'B', email: 'dup@t.com', password: 'Str0ng!Pass', roles: ['aluno'] }),
    ).rejects.toThrow(/already in use|conflict/i);
  });
});
```

## When to skip

- Use case is pure delegation with no branching (e.g., a "get user by ID" that just calls `repo.findById()` and returns) — the repository integration test + E2E test already cover this
- Use case logic is identical to another already-tested use case (avoid variant repetition)

## Examples from project

- **CreateUserUseCase** — Branching (validates password, checks email uniqueness, hashes password) + DB access. **Unit: test validation branches with mocked repo. Integration: test persistence with real DB.**
- **LoginUseCase** — Branching (user not found, wrong password, blocked, valid) + configured lib (JWT, argon2). **Unit: test all branches with real JWT/argon2, mocked repos.**
- **BlockUserUseCase** — Branching (user not found, already blocked) + DB access (update status + invalidate sessions). **Unit: test branches. Integration: test cascading invalidation in real DB.**
- **RefreshTokenUseCase** — Configured lib (JWT verify) + DB (session lookup). **Unit: real JWT + mocked session repo. Integration: session lookup in real DB.**
- **LogoutUseCase** — DB only (invalidate session, no branching). **Integration only.**
- **UpdateRolesUseCase** — Branching (at least 1 role must remain) + DB. **Unit: test role invariant. Integration: test persistence.**
