---
name: testing-guide-dev-workflow-test
description: >
  Testing guide for dev-workflow-test (CodeLearn Platform). Reference this skill
  when planning features, implementing code, creating tests, or reviewing changes
  in dev-workflow-test. Covers what to test, at which layer, and how to set up
  each test — organized by artifact type.
  Triggers on: planning dev-workflow-test features, implementing dev-workflow-test
  features, writing tests for dev-workflow-test, reviewing dev-workflow-test code,
  reviewing dev-workflow-test tests, what should I test in dev-workflow-test,
  how to test dev-workflow-test, dev-workflow-test test guide, CodeLearn test guide.
---

## 0. Purpose

This guide helps you decide **what to test**, at **which layer**, and **how to set up tests** for each type of artifact in `dev-workflow-test` (CodeLearn Platform). When working on a specific artifact type, read the corresponding guide in `artifacts/` for the complete recipe. Supporting references (mock strategies, file conventions, gotchas) are in `references/`.

Artifact types covered: domain entities, TypeORM models, use cases, domain services, controllers (Express Router), repositories, InversifyJS container modules, middleware, error handler, error classes, and anticipated future types (validation schemas/DTOs, value objects).

## 1. Testability Foundations

### Why this stack needs specific testing guidance

**InversifyJS without decorators** — This project uses InversifyJS with `ContainerModule` and `.get()` resolution instead of `@injectable`/`@inject` decorators. For unit tests, this means you construct dependencies manually (no DI container needed). For compilation tests, you load the real `ContainerModule` and verify `.get()` resolves without throwing — this catches missing bindings that TypeScript cannot detect at compile time.

**Hexagonal architecture with explicit mapping** — Domain entities are pure TypeScript (no TypeORM decorators). TypeORM models live in `models/` as separate persistence representations. Repositories bridge the two by mapping domain entities to/from TypeORM models. This means:
- Domain entity tests are true unit tests — no framework, no DB
- Repository tests must be integration tests with a real PostgreSQL — they test the mapping AND the query contract
- Use case tests mock the repository port interface (the driving port), not the concrete TypeORM repository

**Express.js 5 procedural controllers** — Controllers are plain functions on an Express `Router`, not classes. They extract request data, call a use case, and return a response. There is no logic to unit test — E2E tests (supertest) are the only layer that validates HTTP status codes, validation wiring, and access control.

**Mock boundary principle for this project** — Mock across module boundaries, not within. If `LoginUseCase` depends on `UserRepository` (a port interface), mock the port. But use real `jsonwebtoken` with test secrets because it's a configured library — mocking `jwt.sign()` returning `'fake-token'` never catches a wrong secret or bad expiration. Similarly, use real `argon2` with low-cost parameters rather than mocking it.

**Module compilation tests exist** because InversifyJS resolves dependencies at runtime. A missing binding, a wrong scope, or a circular dependency only fails when `container.get()` is called — TypeScript compilation passes fine. These tests are mandatory for every `ContainerModule`.

### Real vs fake decisions

- **PostgreSQL** — Real (test DB in Docker). The project already has a Docker Compose setup.
- **AWS S3** — Fake (in-memory or local fs stub). Avoid network calls and costs in tests.

## 2. Testing Criteria

### Worth testing in this project

- **Use cases with branching logic** (e.g., LoginUseCase: invalid credentials, blocked account, valid login) — unit tests for branches, integration tests for DB contract
- **Domain entities with business rules** (e.g., password policy validation, role invariants like "at least one role") — unit tests
- **Repository query contracts** (e.g., `findByEmail` returns null when not found, `save` persists with correct mapping) — integration tests with real PostgreSQL
- **TypeORM model constraints** (e.g., unique email, soft delete via `@DeleteDateColumn`, `@Column({ select: false })` for passwords) — integration tests with real PostgreSQL
- **Authentication and authorization flows** (login, token refresh, logout, session invalidation on block) — E2E tests
- **Error handler mapping** (custom errors to correct HTTP status codes and response shape) — E2E tests
- **InversifyJS container wiring** (all bindings resolve, correct scopes) — compilation tests
- **Configured library contracts** (JWT secret/expiration, argon2 hashing) — unit tests with real libs + test config

### NOT worth testing in this project

- **Controller logic in isolation** — controllers are thin delegation; E2E covers them
- **Port interfaces** — they're TypeScript interfaces, not runtime code
- **TypeORM CRUD operations** — trust the framework; test YOUR queries and constraints, not `save()`/`find()`
- **Static entity field existence** — TypeScript + TypeORM decorators handle this at compile/sync time
- **Simple error classes** with no branching (e.g., `class NotFoundError extends AppError`) — unless they contain logic beyond setting a message/code

## 3. Feature Implementation Checklist

When implementing a new feature, use this checklist to ensure all artifacts have appropriate test coverage. For each artifact created or modified, check the required test layers:

| Artifact created | Required tests | Guide |
|---|---|---|
| Domain Entity with business logic | Unit: branch logic, invariants, validation rules | `artifacts/domain-entities.md` |
| TypeORM Model | Integration: constraints, defaults, select:false, soft delete | `artifacts/typeorm-models.md` |
| Use Case with branching + DB | Unit: branch logic (mock repo port) + Integration: DB contract | `artifacts/use-cases.md` |
| Use Case with DB only (no branching) | Integration: DB contract | `artifacts/use-cases.md` |
| Use Case with configured lib (JWT, argon2) | Unit: real lib with test config | `artifacts/use-cases.md` |
| Controller (Router handlers) | E2E only — do NOT write unit tests | `artifacts/controllers.md` |
| Repository | Integration: real PostgreSQL, test query contracts + mapping | `artifacts/repositories.md` |
| Container Module | Unit: compilation test (verify DI resolves) | `artifacts/container-modules.md` |
| Auth/RBAC Middleware | E2E: rejected/accepted requests | `artifacts/middleware.md` |
| Error Handler | E2E: error mapping to HTTP status + response shape | `artifacts/error-handler.md` |
| Error Class with branching | Unit: branch logic | `artifacts/error-classes.md` |
| Domain Service with branching | Unit: branch logic (mock dependencies) | `artifacts/domain-services.md` |

**How to use:** After implementing a feature, walk through each row. For each artifact you created or modified, read the corresponding guide and verify the tests exist.

## 4. Artifact Type Testing Guide

When creating or modifying an artifact, read the corresponding guide for the complete recipe.

| Artifact Type | Pattern | Test Layer(s) | Guide |
|---|---|---|---|
| Domain Entities | `*.entity.ts` in `domain/entities/` | Unit | `artifacts/domain-entities.md` |
| TypeORM Models | `@Entity` classes in `models/` | Integration (real DB) | `artifacts/typeorm-models.md` |
| Use Cases | `*.usecase.ts` in `services/` | Unit and/or Integration | `artifacts/use-cases.md` |
| Domain Services | `*.service.ts` in `domain/services/` | Unit | `artifacts/domain-services.md` |
| Controllers | `Router` in `controllers/` | E2E only | `artifacts/controllers.md` |
| Repositories | `*.repository.ts` in `repositories/` | Integration (real DB) | `artifacts/repositories.md` |
| Container Modules | `ContainerModule` in `container/index.ts` | Unit (compilation) | `artifacts/container-modules.md` |
| Middleware | `*.ts` in `shared/middleware/` | E2E | `artifacts/middleware.md` |
| Error Handler | `errorHandler.ts` | E2E | `artifacts/error-handler.md` |
| Error Classes | `*.ts` in `shared/errors/` | Unit (if branching) | `artifacts/error-classes.md` |
| Future Types | DTOs, value objects, migrations | Varies | `artifacts/future-types.md` |

## 5. Anti-patterns — Do NOT Do This

- **Unit test controllers** — controllers are thin delegation layers (`req` -> use case -> `res`); test via E2E only (see `artifacts/controllers.md`)
- **Mock configured libs** (jsonwebtoken, argon2) — use real instances with test config (test JWT secret, low argon2 cost); mocking hides configuration bugs (see section 1)
- **Skip integration tests for services with DB access** — unit tests with mocked repos don't prove queries or mappings are correct (see `artifacts/use-cases.md`)
- **Skip module compilation tests** — TypeScript catches type errors but cannot catch DI wiring errors; a missing InversifyJS binding only fails at runtime (see `artifacts/container-modules.md`)
- **Mock the repository port in integration tests** — integration tests exist to prove the real system boundary works; mock ports only in unit tests
- **Write mirror tests** — assertions that copy the implementation's return value prove nothing (see section 2)
- **Test TypeORM CRUD operations** — trust `save()`/`find()`; test YOUR constraints, mappings, and query conditions instead
- **Skip database cleanup between tests** — stale data causes false positives/negatives and flaky tests (see `references/gotchas.md`)
- **Use `synchronize: true` without `dropSchema: true` in test setup** — leftover schema from previous runs causes unpredictable failures (see `references/external-systems.md`)
- **Import concrete repositories in use cases** — use cases depend on port interfaces; importing the concrete class breaks hexagonal boundaries and makes unit testing impossible without DB

## 6. E2E Terminology Note

This guide uses "E2E" to mean **HTTP-layer integration tests** — tests that use supertest to exercise the full request-to-response chain (middleware -> controller -> use case -> repository -> DB -> response). These are NOT browser-based or multi-service end-to-end tests. The distinction matters when cross-referencing industry sources that use "E2E" differently.

## 7. References

| Topic | File |
|---|---|
| External system mock strategies | `references/external-systems.md` |
| Mock health rules & boundary principle | `references/mock-health-rules.md` |
| File naming, directory structure, coverage targets | `references/file-conventions.md` |
| Stack-specific gotchas & pitfalls | `references/gotchas.md` |

## 8. How to Use This Guide

This guide is organized as a multi-file skill:
- **This file (SKILL.md)** — always loaded. Contains core rules, quick reference, and anti-patterns.
- **`artifacts/`** — one file per artifact type. Read the relevant file when creating or modifying that type.
- **`references/`** — supporting content. Read when you need details on mock strategies, file conventions, or gotchas.

When working on a feature:
1. Check section 3 (Feature Implementation Checklist) to identify which artifacts need tests
2. Read the corresponding `artifacts/*.md` file for the complete testing recipe
3. Consult `references/` files as needed for mock strategies, conventions, or pitfalls
