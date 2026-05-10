# CLAUDE.md

Instructions for Claude Code (and any AI assistant) working autonomously on this repo.

## Project at a glance

**Chess-coach** is a personal-first PWA to learn chess openings in French, aimed at a beginner (~380 Elo). Pedagogy comes from a hand-written knowledge base; Stockfish (WASM) verifies tactically; Mistral generates beginner-friendly explanations on top.

Authoritative product context lives in `README.md` and `docs/`. Always read the relevant doc before implementing — do not improvise product decisions.

- Vision and scope: `docs/00-vision.md`, `docs/01-product-spec.md`
- Architecture and stack: `docs/02-architecture.md`
- Data model: `docs/03-data-model.md`
- Design system: `docs/04-design-system.md`
- Per-screen specs: `docs/05-screens/`
- Pedagogy (knowledge base format, prompts, Stockfish config): `docs/06-pedagogy/`
- Opening content (knowledge base entries): `docs/07-content/` and `packages/content/`
- Roadmap: `docs/08-roadmap.md`

If a doc is empty or contradicts another, **stop and ask** — do not silently fill the gap.

## Repo layout

```
chess-coach/
├── apps/
│   ├── web/        # Next.js 16 (App Router) — frontend PWA
│   └── api/        # NestJS — backend
├── packages/
│   ├── shared/     # TS types, Zod schemas, constants shared by web + api
│   └── content/    # Opening knowledge-base JSON (versioned, hand-written)
├── docs/           # Product / architecture / design / pedagogy docs
├── CLAUDE.md       # This file
└── README.md       # Project overview
```

Monorepo managed with **pnpm workspaces + Turborepo**.

## Stack (target — not yet scaffolded)

- **Frontend**: Next.js 16 (App Router), React 19.2, TypeScript, Tailwind, shadcn/ui, TanStack Query, react-chessboard + chess.js, stockfish.wasm in a Web Worker, Serwist for PWA.
- **Backend**: NestJS + TypeScript, Prisma ORM, `@mistralai/mistralai` SDK, Supabase Auth (JWT validation).
- **DB**: Supabase Postgres (JSONB for variation trees).
- **Hosting**: Railway (frontend + backend), Supabase for DB and Auth.

Open architectural decisions are tracked in `docs/02-architecture.md` under "Open questions". Do not commit code that depends on an unresolved decision without flagging it.

## Commands

> The code is not scaffolded yet. These are the canonical commands once `apps/web` and `apps/api` exist; until then, only the root scripts work (and most will no-op).

```bash
pnpm install            # install all workspaces
pnpm dev                # turbo run dev (web + api in parallel)
pnpm build              # turbo run build
pnpm lint               # turbo run lint
pnpm test               # turbo run test
pnpm typecheck          # turbo run typecheck
```

When adding a new workspace, wire its `dev`/`build`/`lint`/`test`/`typecheck` scripts so Turbo picks them up. Update this section if commands change.

## Working conventions

**Language**
- Code, identifiers, code comments: English.
- Product copy, knowledge base, pedagogical content: French.
- Commit messages: English, imperative mood ("add opening study layout", not "added").

**Code**
- TypeScript strict mode everywhere.
- Shared types and Zod schemas live in `packages/shared` — never duplicate types between `apps/web` and `apps/api`.
- The knowledge base (JSON opening files) is the source of truth for pedagogy. Do not encode pedagogical rules in code.
- Stockfish is a verifier, not an oracle (see `docs/06-pedagogy/stockfish-config.md`). Do not call Stockfish to generate "best moves" for the opening tree.
- The LLM never invents variations or computes — it explains. Prompts must reference the knowledge base + (optionally) a Stockfish eval (see `docs/06-pedagogy/llm-prompts.md`).

**Pedagogy guardrail**
- Beginner-first: prefer principled moves (development, centre, king safety) over engine-best when they conflict at low depth. This is encoded in the knowledge base, not in heuristics.

## Git workflow

- Default branch: `main`.
- Feature branches: `feature/<short-slug>` or `claude/<short-slug>` for AI-led work.
- Never push directly to `main`. Open a PR.
- Conventional Commits encouraged (`feat:`, `fix:`, `docs:`, `chore:`, `refactor:`) but not enforced.
- Do **not** run destructive git operations (`reset --hard`, `push --force`, branch deletion) without explicit user approval.

## Doing tasks

1. Read the relevant `docs/` file before touching code.
2. If specs are missing or ambiguous, ask before coding — do not fill gaps with assumptions.
3. Keep changes minimal and scoped to the task. No drive-by refactors.
4. Update the relevant doc when the implementation reveals a spec gap.
5. After UI work, run the dev server and exercise the feature in a browser before reporting done.

## Out of scope (for the MVP)

Module 1 (game analysis from chess.com) and Module 3 (longitudinal pattern detection) are V3. Maia integration, full offline mode, native apps, custom board colors, sound toggles are V2+. Don't preemptively build for them — `docs/02-architecture.md` lists the seams that keep them cheap to add later.
