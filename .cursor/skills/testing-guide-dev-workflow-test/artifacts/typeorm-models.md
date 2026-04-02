> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# TypeORM Models (`@Entity` classes in `models/`)

## What to test

- **Unique constraints** — e.g., email uniqueness on User model
- **Column defaults** — e.g., `status` defaults to `'active'`, `createdAt` auto-set
- **Soft delete behavior** — `@DeleteDateColumn()` marks `deletedAt` instead of removing rows; soft-deleted records are excluded from normal queries
- **`select: false` columns** — e.g., password hash not returned by default queries
- **Column length constraints** — e.g., `name` max 100 chars enforced at DB level
- **Relation cascades and foreign keys** — e.g., user-roles relationship integrity

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Any TypeORM model | **No** — decorators need a real DB | **Yes** — real PostgreSQL | No |

TypeORM models are persistence representations with decorators. Their behavior (constraints, defaults, cascades) only manifests when executed against a real database. Mocking the DB here proves nothing.

## Setup pattern

```typescript
import { describe, it, expect, beforeAll, afterAll, afterEach } from 'vitest';
import { DataSource, Repository } from 'typeorm';
import { UserModel } from '../src/user/models/user.model.js';

let dataSource: DataSource;
let userRepo: Repository<UserModel>;

beforeAll(async () => {
  dataSource = new DataSource({
    type: 'postgres',
    host: 'localhost',
    port: 5432,
    username: 'lms',
    password: 'lms',
    database: 'lms_test',
    entities: [UserModel],
    synchronize: true,
    dropSchema: true, // clean slate each test run
  });
  await dataSource.initialize();
  userRepo = dataSource.getRepository(UserModel);
});

afterEach(async () => {
  // Truncate to avoid test interference
  await dataSource.query('TRUNCATE TABLE users RESTART IDENTITY CASCADE');
});

afterAll(async () => {
  await dataSource.destroy();
});

describe('UserModel', () => {
  it('enforces unique email constraint', async () => {
    const user1 = userRepo.create({ name: 'A', email: 'dup@test.com', passwordHash: 'hash', roles: ['aluno'] });
    await userRepo.save(user1);

    const user2 = userRepo.create({ name: 'B', email: 'dup@test.com', passwordHash: 'hash', roles: ['aluno'] });
    await expect(userRepo.save(user2)).rejects.toThrow(/duplicate|unique/i);
  });

  it('does not return password hash by default (select: false)', async () => {
    const user = userRepo.create({ name: 'A', email: 'a@t.com', passwordHash: 'secret', roles: ['aluno'] });
    await userRepo.save(user);

    const found = await userRepo.findOneBy({ email: 'a@t.com' });
    expect(found?.passwordHash).toBeUndefined();
  });

  it('soft deletes via deletedAt', async () => {
    const user = userRepo.create({ name: 'A', email: 'a@t.com', passwordHash: 'hash', roles: ['aluno'] });
    await userRepo.save(user);

    await userRepo.softRemove(user);

    // Normal find excludes soft-deleted
    const found = await userRepo.findOneBy({ email: 'a@t.com' });
    expect(found).toBeNull();

    // withDeleted includes it
    const withDeleted = await userRepo.findOne({ where: { email: 'a@t.com' }, withDeleted: true });
    expect(withDeleted?.deletedAt).toBeDefined();
  });

  it('auto-generates uuid', async () => {
    const user = userRepo.create({ name: 'A', email: 'a@t.com', passwordHash: 'hash', roles: ['aluno'] });
    const saved = await userRepo.save(user);
    expect(saved.uuid).toMatch(/^[0-9a-f]{8}-/);
  });
});
```

## When to skip

- The model has no constraints, defaults, or `select: false` columns beyond what TypeORM provides automatically (rare — most models have at least a unique constraint or default)
- The constraint is already tested as part of a repository integration test that covers the same scenario

## Examples from project

- **UserModel** (planned) — Has unique email, `select: false` on passwordHash, `@DeleteDateColumn` for soft delete, auto-generated uuid, auto-timestamps. **All require integration tests with real PostgreSQL.**
- **SessionModel** (planned) — Has foreign key to User, active/inactive status. Integration tests for FK constraint and active-session queries.
