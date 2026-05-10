# 03 — Data model

> **Status**: v0 schema proposed. Will be lifted into `apps/api/prisma/schema.prisma` once the API is scaffolded.

## Overview

Eight entities organised in five groups:

- **Identity**: `User`, `UserPreferences`
- **Content**: `Opening` (knowledge-base entry, JSONB)
- **Repertoire & progress**: `UserRepertoire`, `UserProgress`
- **Sessions**: `StudySession`
- **LLM**: `CachedExplanation`, `LlmConversation`
- **Telemetry**: `UsageEvent`

## Decisions

- **ORM**: Prisma.
- **DB**: Supabase Postgres.
- **JSONB** for the opening knowledge base on `Opening.content` (validated against `OpeningEntry` from `@chess-coach/shared` at seed time).
- **`userId` on every user-owned row** from day one (single user now, multi-user-ready).
- **Auth**: Supabase Auth. `User.id` matches `auth.users.id` (UUID), so we can use Supabase RLS later without remapping.
- **UUIDs** as primary keys throughout. Human-friendly slugs on `Opening.slug` for routing.
- **Cascade rules**: deleting a User cascades to all their data; deleting an Opening is restricted if any repertoire holds it (prevents accidental data loss).
- **Indexes**: at least `userProgress(userId, openingId)`, `cachedExplanations(positionFen, level)`, `userRepertoires(userId)`, `usageEvents(userId, createdAt)`.

## Prisma schema (proposed)

```prisma
// schema.prisma — Chess-coach data model
// Target DB: Supabase Postgres
// Auth: Supabase Auth (User.id mirrors auth.users.id)

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// ─────────────────────────────────────────────────────────────
// Identity
// ─────────────────────────────────────────────────────────────

model User {
  id        String   @id @db.Uuid    // matches auth.users.id from Supabase
  email     String   @unique
  eloLevel  Int?                     // self-reported, optional
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  preferences      UserPreferences?
  repertoires      UserRepertoire[]
  progress         UserProgress[]
  studySessions    StudySession[]
  llmConversations LlmConversation[]
  usageEvents      UsageEvent[]

  @@map("users")
}

model UserPreferences {
  userId    String   @id @db.Uuid
  theme     Theme    @default(classic)
  pieceSet  String   @default("cburnett")
  language  Language @default(fr)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@map("user_preferences")
}

enum Theme {
  classic
  modern
  dark
}

enum Language {
  fr
  // future: en
}

// ─────────────────────────────────────────────────────────────
// Content (knowledge base)
// ─────────────────────────────────────────────────────────────

model Opening {
  id                     String    @id @default(uuid()) @db.Uuid
  slug                   String    @unique
  name                   String
  ecoCodes               String[]
  colour                 Side
  recommendedForBeginner Boolean   @default(false)
  summary                String
  // Full OpeningEntry JSON, validated against @chess-coach/shared at seed time.
  content                Json
  contentVersion         String    // mirrors content.meta.version
  lastReviewed           DateTime
  createdAt              DateTime  @default(now())
  updatedAt              DateTime  @updatedAt

  repertoires        UserRepertoire[]
  progress           UserProgress[]
  studySessions      StudySession[]
  llmConversations   LlmConversation[]
  cachedExplanations CachedExplanation[]

  @@index([recommendedForBeginner])
  @@map("openings")
}

enum Side {
  white
  black
}

// ─────────────────────────────────────────────────────────────
// Repertoire & progress
// ─────────────────────────────────────────────────────────────

model UserRepertoire {
  id         String    @id @default(uuid()) @db.Uuid
  userId     String    @db.Uuid
  openingId  String    @db.Uuid
  mastery    Mastery   @default(starting)
  addedAt    DateTime  @default(now())
  archivedAt DateTime?

  user    User    @relation(fields: [userId], references: [id], onDelete: Cascade)
  opening Opening @relation(fields: [openingId], references: [id], onDelete: Restrict)

  @@unique([userId, openingId])
  @@index([userId])
  @@map("user_repertoires")
}

enum Mastery {
  starting
  learning
  practising
  mastered
}

model UserProgress {
  id         String    @id @default(uuid()) @db.Uuid
  userId     String    @db.Uuid
  openingId  String    @db.Uuid
  ply        Int       // 1..12
  status     PlyStatus @default(not_seen)
  lastSeenAt DateTime?
  updatedAt  DateTime  @updatedAt

  user    User    @relation(fields: [userId], references: [id], onDelete: Cascade)
  opening Opening @relation(fields: [openingId], references: [id], onDelete: Cascade)

  @@unique([userId, openingId, ply])
  @@index([userId, openingId])
  @@map("user_progress")
}

enum PlyStatus {
  not_seen
  seen
  studied
  mastered
}

// ─────────────────────────────────────────────────────────────
// Sessions
// ─────────────────────────────────────────────────────────────

model StudySession {
  id        String      @id @default(uuid()) @db.Uuid
  userId    String      @db.Uuid
  openingId String?     @db.Uuid    // null for free-position sessions
  type      SessionType
  startedAt DateTime    @default(now())
  endedAt   DateTime?
  metadata  Json?       // session-type specific (quiz score, plies covered, FEN, etc.)

  user    User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  opening Opening? @relation(fields: [openingId], references: [id], onDelete: SetNull)

  @@index([userId, startedAt])
  @@map("study_sessions")
}

enum SessionType {
  study
  practice
  free_position
}

// ─────────────────────────────────────────────────────────────
// LLM
// ─────────────────────────────────────────────────────────────

model CachedExplanation {
  id          String   @id @default(uuid()) @db.Uuid
  // Cache key: (positionFen, levelBucket, promptKind). promptKind matches
  // the prompt registry in @chess-coach/shared (e.g. "main_line_move",
  // "deviation", "practice_feedback", "free_position_chat").
  positionFen String
  levelBucket Int      // user's eloLevel rounded to the nearest 100
  promptKind  String
  openingId   String?  @db.Uuid    // optional context
  prompt      String   // full prompt actually sent (for audit/replay)
  response    String
  model       String   // e.g. "mistral-small-latest"
  createdAt   DateTime @default(now())

  opening Opening? @relation(fields: [openingId], references: [id], onDelete: SetNull)

  @@unique([positionFen, levelBucket, promptKind])
  @@index([positionFen, levelBucket])
  @@map("cached_explanations")
}

model LlmConversation {
  id         String   @id @default(uuid()) @db.Uuid
  userId     String   @db.Uuid
  openingId  String?  @db.Uuid
  contextFen String?
  // [{ role: "user" | "assistant" | "system", content: string, createdAt: ISO }, ...]
  messages   Json
  createdAt  DateTime @default(now())
  updatedAt  DateTime @updatedAt

  user    User     @relation(fields: [userId], references: [id], onDelete: Cascade)
  opening Opening? @relation(fields: [openingId], references: [id], onDelete: SetNull)

  @@index([userId, updatedAt])
  @@map("llm_conversations")
}

// ─────────────────────────────────────────────────────────────
// Telemetry
// ─────────────────────────────────────────────────────────────

model UsageEvent {
  id        String    @id @default(uuid()) @db.Uuid
  userId    String    @db.Uuid
  kind      UsageKind
  details   Json?     // free-form (latency_ms, model, depth, openingSlug, etc.)
  createdAt DateTime  @default(now())

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId, createdAt])
  @@index([kind, createdAt])
  @@map("usage_events")
}

enum UsageKind {
  llm_call
  stockfish_call
  practice_attempt
  study_view
}
```

## Notes on choices

- **`User.id` is not auto-generated.** It's set explicitly when we create the row, copying `auth.users.id` from Supabase. This keeps the FK chain clean for Supabase RLS.
- **`Opening.content` is the JSONB blob.** It's the same shape as the canonical JSON files in `packages/content/openings/*.json` — at seed/migration time we validate every entry against the `OpeningEntry` Zod schema in `@chess-coach/shared` and refuse to load invalid ones.
- **Why `contentVersion` is denormalised** (also stored inside `content.meta.version`): so we can index/filter on it and trigger re-validation on bumps without parsing the JSONB.
- **Why `levelBucket` (not raw `eloLevel`) on `CachedExplanation`**: at 380 vs 410 Elo the explanation we want is the same; bucketing maximises cache hits. Bucket size = 100 Elo.
- **Why we keep `prompt` in `CachedExplanation`**: lets us replay/audit a cached response if the prompt template changes (we can detect stale entries by comparing).
- **Cascade rules**:
  - `User` deletion → cascade to all owned rows (preferences, repertoires, progress, sessions, conversations, usage events).
  - `Opening` deletion → restricted from `UserRepertoire` (don't silently nuke a user's tracked opening); cascade for `UserProgress` (per-ply state has no meaning without the opening); SetNull for sessions/conversations/cached explanations (we keep historical traces but lose the link).

## Open questions

- [ ] **Auth strategy in detail**: Supabase RLS + service-role bypass on the API, or Nest-side auth only? Probably RLS so the DB enforces tenancy even if the API is buggy. To pin down before scaffolding.
- [ ] **Should `User.email` live here, or only in `auth.users` (Supabase)?** Mirroring is simpler; the alternative is FK-only on `auth.users.id` and `email` looked up via Supabase admin API.
- [ ] **`UsageEvent` retention policy.** Forever? 90 days? Roll into aggregates after a while? Not urgent — decide once we have real volume.
- [ ] **`StudySession.metadata` schema.** Currently free-form JSON. Could be made strict via a Zod discriminated union per `SessionType` if we hit consistency issues.
- [ ] **Spaced-repetition state.** If we ever add SR, does it live on `UserProgress` (per-ply) or in a separate `ReviewSchedule` table? Not at MVP.

## Next deliverables

1. Lift this schema into `apps/api/prisma/schema.prisma` when the API is scaffolded.
2. Write a seed script that loads `packages/content/openings/*.json`, validates each against `OpeningEntry`, and upserts into `Opening` keyed on `slug`.
3. Write the Supabase Auth → User mirror (a Nest interceptor or a webhook on auth.users insert).
