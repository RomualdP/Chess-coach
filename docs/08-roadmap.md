# 08 — Roadmap

> **Status**: stub. High-level phasing; precise milestones to be defined.

## MVP (Module 2 — Opening study)

- [ ] Pre-dev chantiers (see `README.md` §10):
  - [ ] Wireframes for the 4 screens
  - [ ] Knowledge-base format (frozen Zod schema + 1 full example)
  - [ ] LLM prompts v0 (4–5 key prompts)
  - [ ] Full Prisma schema
  - [ ] Complete palettes for the 3 themes
  - [ ] First 3 openings selected + drafting plan
  - [ ] Formalised success metrics
  - [x] Repo structure
  - [x] CLAUDE.md
- [ ] Open decisions to lock in (see `02-architecture.md` "Open questions"):
  - [x] Frontend hosting: Railway
  - [x] ORM: Prisma
  - [x] Auth on day one (Supabase Auth wired from first deploy)
  - [ ] Final fonts + palettes
- [ ] Implementation phases (TBD, draft):
  - [ ] Scaffold `apps/web` and `apps/api`, wire Turbo, CI
  - [ ] Auth + base shell
  - [ ] Dashboard + repertoire model
  - [ ] Opening study screen (the heavy one)
  - [ ] Practice mode
  - [ ] Free position / chat
  - [ ] PWA wrap-up (manifest, icons, basic SW)
  - [ ] Polish + first-3-openings content
- [ ] Success criteria (see `01-product-spec.md`)

## V2 — On the table

- Maia integration (training opponent + deviation prediction)
- Full offline mode (cached openings + cached LLM)
- Advanced deviation practice
- Guided middlegame practice
- chess.com sync
- Custom board colours, sounds, coordinates toggles
- Additional themes

## V3 — Later

- Module 1: pedagogical analysis of imported games
- Module 3: longitudinal pattern detection across the user's games
