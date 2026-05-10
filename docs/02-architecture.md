# 02 — Architecture

> **Status**: stub. Captures the architectural intent; details to be added as decisions are made.

## Pedagogical architecture (3 layers)

1. **Knowledge base** (the foundation) — hand-written JSON per opening: main line with per-move explanations, core ideas, common deviations, classic traps, middlegame plans. Lives in `packages/content`. This is the source of truth for pedagogy.
2. **Stockfish** (tactical truth) — Stockfish WASM client-side. Verifies positions, checks unforeseen deviations, validates moves in practice mode. Wrapped behind a `ChessEngineService` interface to allow swapping (server-side Stockfish, Maia in V2). **Not** used to generate "best opening moves".
3. **LLM (Mistral)** (the explainer) — generates dynamic explanations from `knowledge base + (optional) Stockfish eval`. Adapts to beginner level. Never invents variations, never computes.

Calibration: Stockfish capped at depth 12–15; LLM is prompted to favour principled moves over engine-best when they diverge at low depth.

## Stack

### Frontend (`apps/web`)
- Next.js 16 (App Router), React 19.2, TypeScript strict
- Tailwind CSS + shadcn/ui
- TanStack Query
- react-chessboard + chess.js
- stockfish.wasm in a Web Worker
- Serwist for PWA (asset SW at MVP, full offline V2)

### Backend (`apps/api`)
- NestJS + TypeScript strict
- Prisma → Supabase Postgres
- `@mistralai/mistralai` SDK (Mistral Small at MVP)
- Supabase Auth (JWT validation in a Nest guard)
- Lightweight usage logger (Stockfish/LLM call counts per user)

### Hosting
- Backend: Railway (confirmed)
- Frontend: Railway *or* Vercel (open question)
- DB: Supabase

## Scaling-ready seams (don't build the future, don't lock it out either)

- Stockfish behind `ChessEngineService` (swap to server / Maia later).
- `userId` on every row from day one, even single-user.
- Usage logger from day one.
- Supabase Auth wired from first online deploy.

Phases: A = me + 5–10 close users (no change), B = 50–200 users (hybrid Stockfish, quotas, monitoring), C = 1000+ (rearchitect from real data).

## Offline strategy (PWA)

- Opening entries fetched as full blocks (not move-by-move).
- TanStack Query handles client-side cache.
- Server-side LLM explanation cache at MVP; client-side cache in V2.
- Full offline reading of bookmarked openings: V2.

## Open questions

- [ ] Frontend hosting: Railway vs Vercel?
- [ ] ORM: Prisma vs Drizzle? (Prisma is the working assumption.)
- [ ] Auth wired from first deploy or later?
- [ ] Final palette and font choices (see `04-design-system.md`).

## Source

For now, see `README.md` §3, §4, §5, §6.
