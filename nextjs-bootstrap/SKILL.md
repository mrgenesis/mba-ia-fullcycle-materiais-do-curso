---
name: nextjs-bootstrap
description: |
  Bootstrap a new Next.js project with our standard architecture, CLAUDE.md, and operational scripts.
  Use this skill whenever the user wants to start a new Next.js project, scaffold a new app, create a new
  project from scratch, or set up a greenfield application. Also use it when the user says things like
  "new project","bootstrap", "scaffold","setup nextjs", or "setup a nextjs using AI".
---

# Next.js Project Bootstrap

Sets up a new Next.js project with a battle-tested architecture: App Router, TypeScript strict, Prisma 7, PostgreSQL, NextAuth v5, and Tailwind CSS 4. Generates a complete CLAUDE.md, operational scripts (init, down, check, watch, tmux, deploy), and project structure ready for AI-assisted development.

## Workflow

### 1. Gather Project Info

Ask the user for:

1. **Project name** (kebab-case, used for directory, DB name, tmux session)
2. **Project description** (one-liner for CLAUDE.md)
3. **Target directory** (where to create/setup the project)

### 2. Run Bootstrap Script

Run the bootstrap script with the gathered info:

```bash
<skill-path>/scripts/bootstrap.sh <project-name> "<project-description>" <target-directory>
```

The script will:
- Create a Next.js app (if target is empty/missing)
- Remove Tailwind 4 incompatible files (tailwind.config, postcss.config)
- Install all dependencies (Prisma, NextAuth, bcryptjs, Zod)
- Copy and configure operational scripts (init, down, check, watch, tmux, deploy)
- Copy source file templates (lib/db, lib/auth, lib/validations, middleware, auth route)
- Set up Prisma schema, config, and seed file
- Generate CLAUDE.md and .env.example with correct project values
- Add package.json scripts (db:push, db:seed, db:migrate, etc.)
- Update .gitignore and generate Prisma client

All templates live in `<skill-path>/templates/` as plain, editable files. The script replaces `{{PROJECT_NAME}}`, `{{PROJECT_DESCRIPTION}}`, and `{{PROJECT_DB_NAME}}` placeholders.

### 3. Report Results

Tell the user:
1. Run `./scripts/init.sh` to start everything (env, database, dev server)
2. The CLAUDE.md is ready -- Claude Code will follow the project patterns
3. Suggest next steps: create first feature, add seed data, etc.

## Adaptation Rules

- **Project name** replaces all instances of project-specific values (DB name, tmux session, log file path)
- **DB names** are derived from project name: `project_name` (dev), `project_name_test` (test)
- Scripts always read `SERVER_PORT` from `.env.local` -- never hardcoded
- Scripts use `PROJECT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"` for portability
- All scripts are idempotent -- safe to run multiple times

## Code Patterns

All code patterns and conventions are defined in the generated CLAUDE.md template (`<skill-path>/templates/claude-md.md`). Follow those patterns when generating any code during bootstrap.

## Rules
- .env.example must never contain real credentials
- Always ask before installing additional packages beyond the core stack
