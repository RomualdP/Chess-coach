# `@chess-coach/api` — Backend

NestJS + TypeScript backend. **Not yet scaffolded.**

## Target stack

- NestJS, TypeScript (strict)
- Prisma ORM → Supabase Postgres
- `@mistralai/mistralai` SDK (Mistral Small at MVP)
- Supabase Auth (JWT validation in a Nest guard)
- Basic usage logger (Stockfish calls, LLM calls per user)

## Bootstrap (when ready)

```bash
# from repo root
pnpm dlx @nestjs/cli new apps/api --package-manager pnpm --skip-git
```

Then wire the package name `@chess-coach/api` in its `package.json` and add `dev`/`build`/`lint`/`test`/`typecheck` scripts so Turborepo picks them up.

See `docs/02-architecture.md`, `docs/03-data-model.md`, and `docs/06-pedagogy/llm-prompts.md` before writing code.
