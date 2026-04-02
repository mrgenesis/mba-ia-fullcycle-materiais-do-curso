> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# External System Strategies

## PostgreSQL — Real (test DB in Docker)

**Why real:** PostgreSQL is fast to spin up in Docker (already in compose.yaml), fully controllable, no rate limits, no external costs. Mocking the database hides real query failures, constraint violations, and mapping bugs. The project's hexagonal architecture with domain-model mapping makes real DB testing essential.

### Test database setup

Use the existing Docker Compose PostgreSQL service with a dedicated test database. Two approaches:

**Option A: Separate test database (recommended)**

Create a `lms_test` database alongside the existing `lms` database. In your test setup:

```typescript
// vitest.global-setup.ts
import { DataSource } from 'typeorm';

export async function setup() {
  // Connect to default DB to create test DB
  const adminDs = new DataSource({
    type: 'postgres',
    host: 'localhost', port: 5432,
    username: 'lms', password: 'lms', database: 'lms',
  });
  await adminDs.initialize();
  await adminDs.query('CREATE DATABASE lms_test').catch(() => {
    // Already exists — fine
  });
  await adminDs.destroy();
}

export async function teardown() {
  // Optional: drop test DB after all tests
}
```

**Option B: Use the same `lms` database with `dropSchema: true`**

Simpler but destructive — only use if the dev database has no important seed data.

### Test DataSource configuration

```typescript
// test/helpers/test-data-source.ts
import { DataSource } from 'typeorm';
// Import ALL entity models that tests need
import { UserModel } from '../../src/user/models/user.model.js';
// ... other models

export function createTestDataSource(): DataSource {
  return new DataSource({
    type: 'postgres',
    host: process.env.DB_HOST ?? 'localhost',
    port: Number(process.env.DB_PORT ?? 5432),
    username: process.env.DB_USER ?? 'lms',
    password: process.env.DB_PASS ?? 'lms',
    database: process.env.DB_NAME ?? 'lms_test',
    entities: [UserModel /* , ...other models */],
    synchronize: true,   // auto-create tables from entities
    dropSchema: true,    // clean slate each test run
    logging: false,      // reduce noise; set true for debugging
  });
}
```

### Database cleanup between tests

Use TRUNCATE (not DELETE) for speed, and CASCADE to handle foreign keys:

```typescript
afterEach(async () => {
  const tables = ['sessions', 'users']; // order matters for FK constraints, or use CASCADE
  for (const table of tables) {
    await dataSource.query(`TRUNCATE TABLE ${table} RESTART IDENTITY CASCADE`);
  }
});
```

**Important:** Do NOT use `repository.delete({})` — TypeORM throws on empty criteria. Always use raw TRUNCATE queries for test cleanup.

### Connection lifecycle

```typescript
beforeAll(async () => {
  dataSource = createTestDataSource();
  await dataSource.initialize();
});

afterAll(async () => {
  await dataSource.destroy(); // always close — prevents hanging connections
});
```

---

## AWS S3 — Fake (in-memory or local filesystem)

**Why fake:** S3 requires network access, has costs, and introduces flakiness from network issues. In tests, replace the S3 adapter with a local implementation.

### Strategy

Since the project uses hexagonal architecture, the storage adapter implements a driving port interface (e.g., `FileStoragePort`). In tests, use a fake implementation:

```typescript
// test/fakes/in-memory-file-storage.ts
import type { FileStoragePort } from '../../src/media/domain/ports/driving/file-storage.port.js';

export class InMemoryFileStorage implements FileStoragePort {
  private files = new Map<string, Buffer>();

  async upload(key: string, data: Buffer): Promise<string> {
    this.files.set(key, data);
    return `https://fake-s3.local/${key}`;
  }

  async download(key: string): Promise<Buffer | null> {
    return this.files.get(key) ?? null;
  }

  async delete(key: string): Promise<void> {
    this.files.delete(key);
  }

  // Test helper
  clear(): void {
    this.files.clear();
  }
}
```

In integration tests for the Media module, inject `InMemoryFileStorage` instead of the real S3 adapter. In the InversifyJS container for tests, rebind:

```typescript
container.rebind(MEDIA_TYPES.FileStorage).toConstantValue(new InMemoryFileStorage());
```

### When S3 arrives

The S3 adapter itself should have a small integration test using a local S3-compatible server (e.g., MinIO or s3rver in Docker) if the team wants to validate the AWS SDK configuration. But this is optional — the fake approach above is sufficient for most scenarios.
