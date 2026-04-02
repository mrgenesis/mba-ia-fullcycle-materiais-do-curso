---
description: Pattern for TypeORM entities and database configuration
paths:
  - src/*/models/**/*.ts
  - src/shared/config/database*.ts
---

# TypeORM Pattern

## Before implementing

Use the context7 MCP tool to query the TypeORM documentation for the specific API you are about to use (e.g., `Entity`, `Column`, `ManyToOne`, `DataSource`). Do not rely solely on the examples in this rule — always verify against the latest docs.

## Rules

1. **Dual identifier**: every entity has an auto-incremented `id` (integer, primary key, internal only) and a `uuid` (v4, unique, exposed to the external world). Never expose `id` in API responses — always use `uuid`
2. **Table naming**: lowercase, plural, snake_case (e.g., `users`, `course_prerequisites`, `quiz_attempts`)
3. **Synchronize**: `synchronize: true` in development, `synchronize: false` in production
4. **Soft delete only**: use `deletedAt` column with TypeORM's `@DeleteDateColumn()` — never hard delete (RN-022)
5. **Data Mapper pattern**: entities are plain data holders, repositories handle persistence — no Active Record


## Entity example

```typescript
import {
  Entity,
  PrimaryGeneratedColumn,
  Column,
  CreateDateColumn,
  UpdateDateColumn,
  DeleteDateColumn,
} from 'typeorm';

@Entity('users')
export class User {
  @PrimaryGeneratedColumn()
  id: number;

  @PrimaryGeneratedColumn('uuid')
  uuid: string;

  @Column({ length: 100 })
  name: string;

  @Column({ unique: true })
  email: string;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;

  @DeleteDateColumn()
  deletedAt: Date | null;
}
```
