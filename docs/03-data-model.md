# 03 — Data model

> **Status**: stub. The Prisma schema will live in `apps/api/prisma/schema.prisma` once scaffolded; this doc captures the conceptual model and decisions.

## Entities (first sketch)

- **users** — `id`, `email`, `eloLevel`, `themePreferences`
- **openings** — `id`, `name`, `ecoCode`, `colour`, `content` (JSONB: `mainLine`, `coreIdeas`, `moves`, `commonDeviations`, `middlegamePlans`), `recommendedForBeginner`
- **userRepertoires** — link `user`–`opening`, mastery level, added at
- **userProgress** — per `(user, opening, ply)`, status: `not_seen | seen | studied | mastered`
- **studySessions** — per-session history
- **cachedExplanations** — LLM explanation cache, keyed by position + level
- **llmConversations** — chat history per session/position

## Decisions to lock in

- ORM: Prisma (working assumption) vs Drizzle.
- JSONB for variation trees on `openings.content`.
- `userId` on every row from day one (even pre-multi-user).
- Indexes: at least `userProgress(userId, openingId)`, `cachedExplanations(positionFen, level)`, `userRepertoires(userId)`.

## Open questions

- [ ] Position keying for caches: full FEN vs canonical hash?
- [ ] Soft delete vs hard delete on repertoires?
- [ ] Where the knowledge-base JSON lives at runtime: in `openings.content`, on disk in the API image, or both (DB seeded from `packages/content`)?

## Source

For now, see `README.md` §8.
