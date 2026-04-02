> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Domain Services (`*.service.ts` in `domain/services/`)

## What to test

- **Stateless business logic** that doesn't fit in entities or use cases
- Branch conditions and edge cases
- Interactions between multiple domain entities (coordination logic)
- Domain calculations (e.g., progress percentage, grade computation in future modules)

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Domain service with branching logic | **Yes** — mock dependencies | No | No |
| Domain service that only delegates (no branching) | **Skip** — test the caller instead | No | No |

Domain services live inside the hexagonal domain layer — they are pure TypeScript with no framework dependencies. Always unit test.

## Setup pattern

```typescript
import { describe, it, expect } from 'vitest';
import { PasswordPolicyService } from '../src/user/domain/services/password-policy.service.js';

describe('PasswordPolicyService', () => {
  const service = new PasswordPolicyService();

  it('accepts a password meeting all criteria', () => {
    expect(service.validate('Str0ng!Pass')).toEqual({ valid: true, violations: [] });
  });

  it('rejects password shorter than 8 characters', () => {
    const result = service.validate('Sh0rt!');
    expect(result.valid).toBe(false);
    expect(result.violations).toContain('minimum 8 characters');
  });

  it('rejects password without uppercase letter', () => {
    const result = service.validate('str0ng!pass');
    expect(result.valid).toBe(false);
    expect(result.violations).toContain('uppercase letter');
  });

  // Test all branches...
});
```

## When to skip

- The domain service has no branching — it only coordinates calls between entities without conditionals
- The logic is trivial (e.g., a factory that just calls `new Entity(...)` with no validation)

## Examples from project

- **No domain services exist yet.** When they are created (e.g., PasswordPolicyService, ProgressCalculationService), apply the patterns above. The first FDD (User module) may introduce password validation as a domain service rather than embedding it in the entity — either location is valid, but test wherever the logic lives.
