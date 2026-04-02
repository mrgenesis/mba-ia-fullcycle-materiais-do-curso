> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Repositories (`*.repository.ts` in `repositories/`)

## What to test

Repositories bridge domain entities and TypeORM models. They implement the driving port interfaces and are responsible for:

- **Query contracts** — e.g., `findByEmail` returns `null` when not found (not throwing), returns a domain entity (not a TypeORM model)
- **Domain-to-model mapping** — domain entity fields map correctly to TypeORM model columns and vice versa
- **Custom query logic** — e.g., finding active sessions by userId, pagination with filters
- **Soft delete awareness** — queries exclude soft-deleted records by default
- **Relation loading** — eager/lazy relations load correctly when needed

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Any repository | **No** — mocking the DB defeats the purpose | **Yes** — real PostgreSQL | No |

Repositories exist to cross the system boundary (DB). A unit test with a mocked DB proves nothing about the actual query. Integration tests with real PostgreSQL are mandatory.

## Setup pattern

```typescript
import { describe, it, expect, beforeAll, afterAll, afterEach } from 'vitest';
import { DataSource } from 'typeorm';
import { TypeOrmUserRepository } from '../src/user/repositories/typeorm-user.repository.js';
import { UserModel } from '../src/user/models/user.model.js';
import { User } from '../src/user/domain/entities/user.entity.js';

let dataSource: DataSource;
let repository: TypeOrmUserRepository;

beforeAll(async () => {
  dataSource = new DataSource({
    type: 'postgres',
    host: 'localhost', port: 5432,
    username: 'lms', password: 'lms', database: 'lms_test',
    entities: [UserModel],
    synchronize: true,
    dropSchema: true,
  });
  await dataSource.initialize();
  repository = new TypeOrmUserRepository(dataSource);
});

afterEach(async () => {
  await dataSource.query('TRUNCATE TABLE users RESTART IDENTITY CASCADE');
});

afterAll(async () => {
  await dataSource.destroy();
});

describe('TypeOrmUserRepository', () => {
  it('saves a domain entity and retrieves it as a domain entity', async () => {
    const user = new User({ name: 'Maria', email: 'maria@test.com', passwordHash: 'hash', roles: ['aluno'] });
    const saved = await repository.save(user);

    expect(saved).toBeInstanceOf(User); // returns domain entity, not TypeORM model
    expect(saved.uuid).toBeDefined();
    expect(saved.email).toBe('maria@test.com');
  });

  it('findByEmail returns null when user does not exist', async () => {
    const result = await repository.findByEmail('nonexistent@test.com');
    expect(result).toBeNull();
  });

  it('findByEmail returns domain entity with correct mapping', async () => {
    const user = new User({ name: 'Maria', email: 'maria@test.com', passwordHash: 'hash', roles: ['aluno'] });
    await repository.save(user);

    const found = await repository.findByEmail('maria@test.com');
    expect(found).toBeInstanceOf(User);
    expect(found?.name).toBe('Maria');
    expect(found?.roles).toEqual(['aluno']);
  });

  it('excludes soft-deleted users from findByEmail', async () => {
    const user = new User({ name: 'A', email: 'a@t.com', passwordHash: 'hash', roles: ['aluno'] });
    await repository.save(user);
    await repository.softDelete(user.uuid);

    const result = await repository.findByEmail('a@t.com');
    expect(result).toBeNull();
  });
});
```

## When to skip

- The repository method is a simple pass-through to a TypeORM built-in with no custom query or mapping logic (rare in this project — the hexagonal mapping between domain entity and TypeORM model always involves custom logic)

## Examples from project

- **TypeOrmUserRepository** (planned) — Methods: `save`, `findByEmail`, `findByUuid`, `updateStatus`, `updateRoles`, `softDelete`. All involve domain-model mapping. **Integration tests for every public method.**
- **TypeOrmSessionRepository** (planned) — Methods: `create`, `findByToken`, `invalidate`, `invalidateAllForUser`. **Integration tests — especially `invalidateAllForUser` which is critical for the block-user flow.**
