# 08 — Roadmap

> **Status**: phased development plan for the MVP (Module 2 — Opening study). Estimates assume part-time work; numbers are calibrated for a single-developer pace.

## Pre-dev chantiers

These prepare the ground for coding. Most are draft-and-sign-off cycles; once signed off, they freeze and the schema lifts into code.

| # | Chantier | Status | Doc |
| --- | --- | --- | --- |
| 1 | Wireframes for the 4 screens | ⏳ pending | `docs/05-screens/` |
| 2 | Knowledge-base format (frozen Zod) | ✅ v0, sign-off pending | `docs/06-pedagogy/knowledge-base-format.md` |
| 3 | LLM prompts v0 | ✅ v0, sign-off pending | `docs/06-pedagogy/llm-prompts.md` |
| 4 | Full Prisma schema | ✅ v0, sign-off pending | `docs/03-data-model.md` |
| 5 | Complete palettes for the 3 themes | ⏳ pending | `docs/04-design-system.md` |
| 6 | First 3 openings + drafting plan | ✅ locked, sign-off pending | `docs/07-content/` |
| 7 | Formalised success metrics | ✅ done | `docs/01-product-spec.md` |
| 8 | Repo structure | ✅ done | repo root |
| 9 | CLAUDE.md | ✅ done | `CLAUDE.md` |

Outstanding decisions to lock in:

- [x] Frontend hosting: **Railway**
- [x] ORM: **Prisma**
- [x] Auth: **Supabase Auth wired from day one**
- [ ] Final fonts and palettes (chantier 5)

## Development plan (MVP)

Six phases, ~20–25 PRs, ~4 weeks of focused work (longer part-time). Each phase has a definition of done; nothing in the next phase starts until it's met.

### Phase 0 — Scaffold (1–2 days)

> Goal: `pnpm install && pnpm dev` runs both apps and they say hello.

| PR | Title | Notes |
| --- | --- | --- |
| 0.1 | Scaffold `apps/web` (Next.js 16) | App Router, TS, Tailwind, ESLint. Wire to Turbo. |
| 0.2 | Scaffold `apps/api` (NestJS) | Nest CLI default, TS strict. Wire to Turbo. |
| 0.3 | Initialise `packages/shared` | TS config, lift `OpeningEntry` Zod schema from `docs/06-pedagogy/knowledge-base-format.md`. |
| 0.4 | Initialise `packages/content` | Placeholder JSON + validation script (chess.js replay + Zod parse). |
| 0.5 | Turborepo pipelines | `dev`, `build`, `lint`, `test`, `typecheck` across all workspaces. |
| 0.6 | GitHub Actions CI | PR validation: lint + typecheck + test + content validation. Block merge on red. |

**Definition of done**: green CI on a sample PR; both apps boot locally; content validation script catches a deliberately-broken JSON.

### Phase 1 — Foundation (3–5 days)

> Goal: a logged-in user with a seeded catalogue, no UI features yet.

| PR | Title | Notes |
| --- | --- | --- |
| 1.1 | Supabase project + Prisma schema | New Supabase project; lift schema from `docs/03-data-model.md`; first migration. |
| 1.2 | API skeleton + Supabase JWT guard | Nest auth guard validating Supabase JWTs. |
| 1.3 | User mirror on Supabase Auth | On signup, mirror to `User` table (webhook or interceptor). |
| 1.4 | Web app shell + theme tokens | Header, theme switcher (3 themes), shadcn base, Tailwind tokens for `data-theme`. |
| 1.5 | End-to-end auth flow | Login on web → JWT to API → user row exists. |
| 1.6 | Seed script | Loads `packages/content/openings/*.json` into the DB, validates each, upserts on `slug`. |
| 1.7 | Authoring CLI | `pnpm content:check` runs the validation script locally. |

**Definition of done**: a clean DB can be seeded from `packages/content`; logged-in user receives `/api/me` correctly; Supabase RLS strategy chosen and documented.

### Phase 2 — Opening study screen (5–7 days, the heavy one)

> Goal: the user picks an opening and studies it end-to-end.

| PR | Title | Notes |
| --- | --- | --- |
| 2.1 | Dashboard read-only | List of openings in repertoire, empty state, link to add flow. |
| 2.2 | Add opening to repertoire | Mutation, optimistic update, simple catalogue picker (skip the LLM-powered picker at MVP). |
| 2.3 | Opening study layout | Board + content panel; desktop side-by-side, mobile stacked. |
| 2.4 | Ply navigation + board sync | `react-chessboard` + `chess.js`; FEN before each ply drives the board. |
| 2.5 | Layered content rendering | `coreIdeas` (collapsible), `mainLine` (per ply), `deviations` (grouped by parent ply), `traps` (collapsible), `middlegamePlans` (collapsible). |
| 2.6 | Wire `explain_main_line_move` | LLM call on "expliquer plus" + caching via `CachedExplanation`. |
| 2.7 | Mini-chat anchored on the current ply | Reuses prompt 1; conversation persisted via `LlmConversation`. |
| 2.8 | Progress tracking | Mark a ply `seen` on view, `studied` on long view, `mastered` after practice success (Phase 3). |

**Definition of done**: Romuald can open the Italian Game, navigate all 12 plies, expand explanations, ask a follow-up question, and see his progress reflect on the dashboard.

### Phase 3 — Practice mode (3–4 days)

> Goal: drill the main line of an opening, with calibrated feedback.

| PR | Title | Notes |
| --- | --- | --- |
| 3.1 | Practice mode layout | Board + feedback panel + attempt counter. |
| 3.2 | `ChessEngineService` interface | Abstracts Stockfish calls; first impl is `stockfish.wasm` in a Web Worker. |
| 3.3 | Move validation logic | KB-correct → success; matches `ply.alternatives` with `verdict: playable` → soft warning; otherwise → mistake. Stockfish eval used to gauge severity. |
| 3.4 | Wire `practice_feedback` | LLM call after each attempt, escalating hints. Not cached. |
| 3.5 | Session persistence + summary | `StudySession` row with `metadata.score`, end-of-session summary screen. |

**Definition of done**: Romuald can run a full 12-ply quiz, get useful feedback, and see his progress update.

### Phase 4 — Free position chat (2–3 days)

> Goal: ask the LLM about any position, with engine grounding.

| PR | Title | Notes |
| --- | --- | --- |
| 4.1 | Free position screen layout | Board + chat panel; FEN input field; basic free piece placement. |
| 4.2 | Position setup methods | Paste FEN, play from start, drag-place pieces (basic). |
| 4.3 | Stockfish multi-PV (depth 12–15) | Top-3 lines surfaced to the LLM. |
| 4.4 | Wire `free_position_chat` | Conversation persisted via `LlmConversation`; auto-check no SAN appears outside the engine output. |

**Definition of done**: Romuald can paste a FEN from a real game, ask "pourquoi ce coup est mauvais ?", and get a grounded answer.

### Phase 5 — PWA polish + content (2–3 days)

| PR | Title | Notes |
| --- | --- | --- |
| 5.1 | Serwist setup | Manifest, icons, base SW for assets and fonts. |
| 5.2 | Install prompt UX | "Add to home screen" affordance on supported platforms. |
| 5.3 | Author the 3 openings | Italian Game, Caro-Kann, London System (chess content work — author by Romuald + validation). |

**Definition of done**: app installable as a PWA, all 3 openings live in production, content validates green in CI.

### Phase 6 — Production deployment

| PR | Title | Notes |
| --- | --- | --- |
| 6.1 | Railway deploy | Frontend + backend services, env vars, secrets. |
| 6.2 | Supabase prod project | Migrate, seed, set up backups. |
| 6.3 | Smoke test + real-use kickoff | Romuald uses it for real for 2 weeks; bugs feed back into a polish PR list. |

**Definition of done**: app runs in production, Romuald has logged at least one real study session, the success-criteria timer (`docs/01-product-spec.md`) starts.

## Effort summary

| Phase | Days (focused) | PRs |
| --- | --- | --- |
| 0 — Scaffold | 1–2 | 6 |
| 1 — Foundation | 3–5 | 7 |
| 2 — Opening study | 5–7 | 8 |
| 3 — Practice | 3–4 | 5 |
| 4 — Free position | 2–3 | 4 |
| 5 — PWA + content | 2–3 | 3 |
| 6 — Deploy | 1–2 | 3 |
| **Total** | **17–26 days** | **36** |

(Original "20–25 PRs" estimate was conservative; the breakdown lands around 36 small PRs. Each is sized to land in < 1 day.)

## V2 — On the table

- Maia integration (training opponent + deviation prediction).
- Full offline mode (cached openings + cached LLM responses).
- Advanced deviation practice (full sub-trees per deviation).
- Guided middlegame practice.
- chess.com sync (read-only at first).
- Custom board colours, sound toggles, coordinate toggles.
- Additional themes (neon, vintage, community).
- LLM-powered opening picker (waits until catalogue ≥ 10 openings).
- Streaming LLM responses.
- Opening picker UX upgrade.

## V3 — Later

- **Module 1**: pedagogical analysis of imported games (chess.com PGN import, error detection, explanations).
- **Module 3**: longitudinal pattern detection across the user's games.
