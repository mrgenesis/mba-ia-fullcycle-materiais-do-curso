> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Container Modules (`ContainerModule` in `container/index.ts`)

## What to test

- **Module compilation** — the `ContainerModule` loads without errors
- **DI resolution** — all registered services resolve via `container.get()` without throwing
- **Correct binding scopes** — repositories are singletons, use cases are transient (per the container-service rule)
- **Cross-module resolution** — the global container successfully loads all module containers together

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Any ContainerModule | **Yes** — compilation/resolution test | No | No |

These are "unit" tests in the sense that they don't require HTTP or external systems, but they do load the **real** InversifyJS container and resolve real bindings. The goal is to catch:
- Missing bindings (`No matching bindings found for serviceIdentifier`)
- Circular dependencies
- Wrong provider factories (e.g., `toDynamicValue` that throws)
- Missing module imports in the global container

TypeScript compilation does NOT catch these errors — InversifyJS resolves at runtime.

## Setup pattern

```typescript
import { describe, it, expect, beforeAll } from 'vitest';
import { Container } from 'inversify';
import { userModule } from '../src/user/container/index.js';
import { USER_TYPES } from '../src/user/container/types.js';

describe('User ContainerModule', () => {
  let container: Container;

  beforeAll(async () => {
    container = new Container();
    // Load the module — this is the compilation test
    await container.load(userModule);
  });

  it('resolves UserRepository', () => {
    expect(() => container.get(USER_TYPES.UserRepository)).not.toThrow();
  });

  it('resolves CreateUserUseCase', () => {
    expect(() => container.get(USER_TYPES.CreateUserUseCase)).not.toThrow();
  });

  it('resolves LoginUseCase', () => {
    expect(() => container.get(USER_TYPES.LoginUseCase)).not.toThrow();
  });

  // Test all registered bindings...
});

describe('Global container', () => {
  let container: Container;

  beforeAll(async () => {
    // Import the real global container setup
    const { container: globalContainer } = await import('../src/shared/container.js');
    container = globalContainer;
  });

  it('resolves all user module services', () => {
    expect(() => container.get(USER_TYPES.UserRepository)).not.toThrow();
    expect(() => container.get(USER_TYPES.CreateUserUseCase)).not.toThrow();
    expect(() => container.get(USER_TYPES.LoginUseCase)).not.toThrow();
  });

  // As modules are added, test cross-module resolution here
});
```

**Note:** If a container binding depends on a database connection (e.g., repository bindings that use `toDynamicValue` with `DataSource`), the compilation test may need a real DataSource or a lightweight stub. Prefer starting a real DataSource for these tests — it's fast in Docker and catches real wiring issues.

## When to skip

- **Never skip** — module compilation tests are mandatory for every `ContainerModule`. A missing binding is a real runtime bug that TypeScript cannot catch.

## Examples from project

- **userModule** (planned) — Binds UserRepository (singleton), CreateUserUseCase (transient), LoginUseCase (transient), BlockUserUseCase, UpdateRolesUseCase, etc. **Compilation test for every binding.**
- **Global container** (planned at `src/shared/container.ts`) — Loads all module containers. **Test that cross-module resolution works.**
