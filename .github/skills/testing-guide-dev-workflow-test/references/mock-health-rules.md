> Part of the `testing-guide-dev-workflow-test` skill (see `../SKILL.md`).

# Mock Health Rules & Boundary Principle

## The boundary rule for this project

Mock across **architecturally significant boundaries**, not within. In this hexagonal architecture, the significant boundaries are:

1. **Port interfaces** (driving ports) — e.g., `UserRepository` interface. Mock these in unit tests because the concrete implementation has its own integration tests.
2. **Side-effect dependencies** — e.g., email sender, S3 uploader. Mock these to avoid irreversible external effects.
3. **External HTTP APIs** — e.g., payment gateways. Use fake HTTP servers (MSW, nock) instead of mocking the client directly.

### What to mock in unit tests

| Dependency type | Mock? | Why |
|---|---|---|
| **Owned services** (your repo ports, other use cases) | **Yes** — they have their own tests | Isolates the unit under test |
| **Configured libs** (jsonwebtoken, argon2) | **No** — use real with test config | Mocking hides configuration bugs |
| **Side-effect deps** (email, SMS, S3) | **Yes** — irreversible external effects | Prevents side effects in tests |
| **Slow pure functions** (argon2 hash) | **No** — use lower cost parameters | Still exercises real behavior |

### Concrete examples

**Good: mock the repository port**
```typescript
const mockUserRepo = {
  findByEmail: vi.fn(),
  save: vi.fn(),
};
const useCase = new CreateUserUseCase(mockUserRepo);
```
Why: `TypeOrmUserRepository` has its own integration tests. Mocking the port interface isolates the use case logic.

**Bad: mock jsonwebtoken**
```typescript
// DON'T DO THIS
vi.mock('jsonwebtoken', () => ({
  sign: vi.fn().mockReturnValue('fake-token'),
  verify: vi.fn().mockReturnValue({ sub: 'user-1' }),
}));
```
Why: This never catches wrong JWT secrets, bad expiration, malformed payloads, or algorithm mismatches.

**Good: use real jsonwebtoken with test config**
```typescript
import jwt from 'jsonwebtoken';
const TEST_SECRET = 'test-secret-for-testing';
// In the test, verify the token is real
const decoded = jwt.verify(result.token, TEST_SECRET);
expect(decoded.sub).toBe(expectedUserId);
```

**Bad: mock argon2**
```typescript
// DON'T DO THIS
vi.mock('argon2', () => ({
  hash: vi.fn().mockResolvedValue('mocked-hash'),
  verify: vi.fn().mockResolvedValue(true),
}));
```
Why: This never catches hashing configuration issues or actual verification failures.

**Good: use real argon2 with low-cost parameters**
```typescript
import argon2 from 'argon2';
// In test setup or test config
const TEST_ARGON2_OPTIONS = {
  timeCost: 1,       // minimum — fast for tests
  memoryCost: 1024,  // minimum — low memory for tests
  parallelism: 1,
};
const hash = await argon2.hash('password', TEST_ARGON2_OPTIONS);
const isValid = await argon2.verify(hash, 'password');
```

## Vitest mocking patterns

### `vi.fn()` for port interface methods
```typescript
const mockRepo = {
  findByEmail: vi.fn().mockResolvedValue(null),
  save: vi.fn().mockResolvedValue(savedUser),
};
```

### `vi.spyOn()` for observing real implementations
```typescript
const spy = vi.spyOn(sessionRepo, 'invalidateAll');
await blockUserUseCase.execute(userId);
expect(spy).toHaveBeenCalledWith(userId);
```
Use sparingly — prefer asserting on observable outcomes (DB state, return values) over spy interactions.

### Reset between tests
```typescript
beforeEach(() => {
  vi.clearAllMocks(); // clears call history and return values
});
```

## Litmus test

Can you describe what **observable behavior** this test validates without referencing mock interactions?

- "Verifies that `findByEmail` was called with 'test@mail.com'" — **Bad.** That's wiring, not behavior.
- "Verifies that LoginUseCase throws UnauthorizedError when user is not found" — **Good.** Observable behavior.
- "Verifies that `save` was called once" — **Bad.** Wiring test.
- "Verifies that the created user appears in the database with the correct email" — **Good.** Observable outcome.

## When too many mocks signal a design problem

If a unit test requires more than 3 mocks to set up, consider:
1. **Split the unit under test** — the use case is doing too much
2. **Write an integration test instead** — the behavior you're testing is about interactions, not logic
3. **Check if you're mocking within boundaries** — you shouldn't need to mock internal collaborators of the same module
