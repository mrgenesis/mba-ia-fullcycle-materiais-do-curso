> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# File Conventions

## Naming conventions by layer

| Layer | File suffix | Example |
|---|---|---|
| Unit tests | `*.test.ts` | `login.usecase.test.ts` |
| Integration tests | `*.integration.test.ts` | `typeorm-user.repository.integration.test.ts` |
| E2E tests | `*.e2e.test.ts` | `user-routes.e2e.test.ts` |

The suffix distinguishes layers so Vitest can be configured to run them separately (different configs, different setup needs).

## Directory structure

Tests are **colocated** with the source code they test, inside a `__tests__` directory at the module level:

```
src/
  user/
    __tests__/
      unit/
        login.usecase.test.ts
        create-user.usecase.test.ts
        user.entity.test.ts
      integration/
        typeorm-user.repository.integration.test.ts
        user-model.integration.test.ts
        create-user.usecase.integration.test.ts
      e2e/
        user-routes.e2e.test.ts
        auth-routes.e2e.test.ts
    domain/
      entities/
      ports/
      services/
    controllers/
    models/
    repositories/
    services/
    container/
  shared/
    __tests__/
      unit/
        user-container.test.ts
      e2e/
        error-handler.e2e.test.ts
```

## Test helpers and shared setup

```
test/
  helpers/
    test-data-source.ts       # Shared DataSource factory for integration/e2e tests
    test-tokens.ts             # JWT token generation helpers for e2e tests
    test-fixtures.ts           # Reusable test data factories
  vitest.global-setup.ts       # Global setup (create test DB)
```

## Vitest configuration

### Single config with project workspaces (recommended)

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: false,          // explicit imports (import { describe, it } from 'vitest')
    environment: 'node',
    globalSetup: './test/vitest.global-setup.ts',
    include: ['src/**/*.test.ts'],
    coverage: {
      provider: 'v8',
      include: ['src/**/*.ts'],
      exclude: [
        'src/**/*.test.ts',
        'src/**/index.ts',     // barrel files
        'src/server.ts',       // entry point
        'src/**/types.ts',     // DI type constants
        'src/**/ports/**',     // interfaces — no runtime code
      ],
      thresholds: {
        statements: 80,
        branches: 80,
        functions: 80,
        lines: 80,
      },
    },
  },
});
```

### Separate configurations for layers (alternative)

If integration/e2e tests need different timeouts or setup:

```typescript
// vitest.config.unit.ts
export default defineConfig({
  test: {
    include: ['src/**/*.test.ts', '!src/**/*.integration.test.ts', '!src/**/*.e2e.test.ts'],
    testTimeout: 5000,
  },
});

// vitest.config.integration.ts
export default defineConfig({
  test: {
    include: ['src/**/*.integration.test.ts'],
    testTimeout: 30000,
    globalSetup: './test/vitest.global-setup.ts',
  },
});

// vitest.config.e2e.ts
export default defineConfig({
  test: {
    include: ['src/**/*.e2e.test.ts'],
    testTimeout: 30000,
    globalSetup: './test/vitest.global-setup.ts',
  },
});
```

With corresponding npm scripts:
```json
{
  "test:unit": "vitest run --config vitest.config.unit.ts",
  "test:integration": "vitest run --config vitest.config.integration.ts",
  "test:e2e": "vitest run --config vitest.config.e2e.ts"
}
```

## Coverage targets (thorough philosophy)

| Metric | Target |
|---|---|
| Statements | 80% |
| Branches | 80% |
| Functions | 80% |
| Lines | 80% |

These targets apply to source code only — test files, interfaces (ports), type constants, barrel files, and entry points are excluded from coverage measurement.

**Rationale for 80%:** Thorough coverage without forcing tests for trivial code (simple error classes, type re-exports). The remaining 20% accounts for genuinely untestable or not-worth-testing code per the Testing Criteria.

## Import extensions

TypeScript ESM requires `.js` extensions in imports. Test files follow the same convention:

```typescript
// In test files
import { LoginUseCase } from '../../services/login.usecase.js';
import { User } from '../../domain/entities/user.entity.js';
```
