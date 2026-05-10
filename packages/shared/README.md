# `@chess-coach/shared`

Shared TypeScript types, Zod schemas, and constants used by both `apps/web` and `apps/api`.

## What lives here

- Domain types (Opening, Move, KnowledgeBaseEntry, etc.)
- Zod schemas validating the knowledge-base JSON format
- Shared enums and constants (theme keys, ply status values, etc.)
- Pure helpers with no Node/Browser dependencies

## What does not live here

- React components → `apps/web`
- Nest providers, Prisma models → `apps/api`
- Anything pulling on `fs`, `next`, `@nestjs/*`, or browser-only globals

The package must remain runtime-agnostic.
