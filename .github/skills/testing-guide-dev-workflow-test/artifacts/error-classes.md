> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Error Classes (`*.ts` in `shared/errors/`)

## What to test

- **Branch logic inside error classes** — e.g., a ValidationError that aggregates multiple violation messages
- **Computed properties** — e.g., error code derived from error type

## Layer assignment

| Condition | Unit | Integration | E2E |
|---|---|---|---|
| Error class with branching (e.g., aggregates violations) | **Yes** — unit test | No | No |
| Simple error class (extends base, sets message/code) | **Skip** — no behavior to test | No | Covered by error handler E2E |

## Setup pattern

```typescript
import { describe, it, expect } from 'vitest';
import { ValidationError } from '../src/shared/errors/validation-error.js';

describe('ValidationError', () => {
  // Only test if it has branching logic
  it('aggregates multiple violations into a single message', () => {
    const error = new ValidationError([
      'minimum 8 characters',
      'uppercase letter required',
    ]);
    expect(error.message).toContain('minimum 8 characters');
    expect(error.message).toContain('uppercase letter required');
    expect(error.violations).toHaveLength(2);
  });
});
```

## When to skip

- The error class is a simple subclass: `class NotFoundError extends AppError { constructor(msg) { super(msg, 404); } }` — no behavior, no branching. The error handler E2E test already proves it maps to the correct status code.
- The error class only sets static properties (code, message, statusCode)

## Examples from project

- **NotFoundError, ConflictError, ForbiddenError, UnauthorizedError** (planned) — Likely simple subclasses. **Skip unit tests; covered by error handler E2E.**
- **ValidationError** (planned) — May aggregate violation messages from password policy, role validation, etc. **Unit test if it contains aggregation logic.**
