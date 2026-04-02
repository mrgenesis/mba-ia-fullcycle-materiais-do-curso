> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Domain Entities (`*.entity.ts` in `domain/entities/`)

## What to test

- Business rule validation (e.g., password policy: min 8 chars, 1 uppercase, 1 lowercase, 1 number, 1 symbol)
- Invariant enforcement (e.g., user must have at least 1 role — RN-023)
- State transitions (e.g., active -> blocked, role addition/removal)
- Computed properties or derived values
- Edge cases in domain logic (empty roles array, max-length name, boundary values)

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Entity has branching logic (validation, state transitions) | **Yes** — test all branches | No | No |
| Entity is a plain data holder (no methods, no logic) | **Skip** — no behavior to test | No | No |

Domain entities are **pure TypeScript** — no TypeORM decorators, no framework dependencies. They are always unit tested in isolation. Never start a database for domain entity tests.

## Setup pattern

```typescript
import { describe, it, expect } from 'vitest';
import { User } from '../src/user/domain/entities/user.entity.js';

describe('User entity', () => {
  // Test business rule validation
  it('rejects password without uppercase letter', () => {
    expect(() => User.validatePassword('weakpass1!')).toThrow(/uppercase/);
  });

  // Test invariant enforcement
  it('requires at least one role', () => {
    expect(() => new User({ name: 'Test', email: 'a@b.com', roles: [] }))
      .toThrow(/at least one role/);
  });

  // Test state transitions
  it('transitions from active to blocked', () => {
    const user = createValidUser({ status: 'active' });
    user.block();
    expect(user.status).toBe('blocked');
  });

  // Test edge cases
  it('accepts name at exactly 100 characters', () => {
    const name = 'a'.repeat(100);
    const user = createValidUser({ name });
    expect(user.name).toBe(name);
  });

  it('rejects name over 100 characters', () => {
    const name = 'a'.repeat(101);
    expect(() => createValidUser({ name })).toThrow(/100/);
  });
});

// Helper: builds a valid user with optional overrides
function createValidUser(overrides: Partial<UserProps> = {}): User {
  return new User({
    name: 'Valid Name',
    email: 'valid@example.com',
    password: 'Str0ng!Pass',
    roles: ['aluno'],
    ...overrides,
  });
}
```

## When to skip

- Entity has no methods, only property declarations — TypeScript enforces types at compile time
- Entity fields are only getters with no transformation or validation logic
- The entity delegates all validation to the use case (in that case, test the use case instead)

## Examples from project

- **User entity** (planned) — Has password policy validation, role invariants (min 1 role), status transitions (active/blocked), name length validation. **Dominant pattern: unit tests for all branches.**
