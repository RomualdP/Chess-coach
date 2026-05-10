# 01 — Product spec

> **Status**: scope locked, success criteria formalised below. Per-screen detail lives in `docs/05-screens/`.

## MVP scope

**One module: Module 2 — Opening study and transition to middlegame.**

The user picks openings, studies them in layers, drills the main line in practice mode, and asks the LLM about arbitrary positions.

### In scope at MVP

- **Guided opening selection** (catalogue + recommendations from the LLM helper).
- **Layered opening study** (`coreIdeas → mainLine → deviations → traps → middlegamePlans`, see `docs/06-pedagogy/knowledge-base-format.md`).
- **Practice mode** (interactive quiz on the main line of an opening).
- **Free chat** with the LLM on a given position (Stockfish-bounded, see `docs/06-pedagogy/llm-prompts.md`).

### Out of scope at MVP

| Feature | Status |
| --- | --- |
| Module 1 — Game analysis from chess.com import | V3 |
| Module 3 — Longitudinal pattern detection | V3 |
| Maia training opponent + deviation prediction | V2 |
| Advanced deviation practice (full sub-lines) | V2 |
| Guided middlegame practice | V2 |
| chess.com sync | V2 |
| Custom board colours, sound toggles, coordinate toggles | V2 |
| Additional themes (neon, vintage, community) | V2 |
| Native mobile apps | V2+ (PWA is sufficient) |

See `docs/08-roadmap.md` for the full phasing.

## Success criteria (formalised)

The MVP is considered a success if **all** of the following hold after 30 days of real use.

### Content quality

- All openings in `packages/content/openings/` validate against `OpeningEntry` (Zod parse + chess.js replay of every `mainLine` and `deviation.followUp`).
- Manual review by Romuald: every `coreIdea`, ply explanation, deviation, trap, and middlegame plan rated **"correct"** or **"excellent"** on a 4-point scale (`wrong / weak / correct / excellent`).
- **0 factually wrong claims** surface during use (tracked in a lightweight `content-issues.md` log).

### LLM quality

- **≥ 80%** of LLM responses rated **"useful"** or better on a thumbs up/down + 4-point scale (`harmful / useless / useful / excellent`), measured on a sample of at least 50 responses across the 5 prompt kinds.
- **0 hallucinated variations** in production traffic. Auto-check: every cited SAN move must appear in the prompt's input context. Failures are logged as `usage_events` of kind `llm_call` with a `details.hallucinationDetected = true` flag.
- Response latency targets: **p50 < 1.5 s** for cached, **p50 < 4 s** for fresh, **p95 < 8 s** for fresh.

### Reliability

- **0 blocking bugs** (defined: a bug that prevents completing a study or practice session) over any rolling 7-day window.
- Service worker installs and the app loads its shell + fonts when offline (full-content offline reading is V2).
- All routes return < 500 errors over the same 7-day window.

### Engagement (the personal-use signal)

- **≥ 3 study or practice sessions per week** for **4 consecutive weeks**.
  *Session* = at least 5 minutes spent on the opening-study or practice screen, recorded as a `StudySession` row.
- **≥ 1 free-position chat per week** over the same period.

### Skill progression (long-horizon signal, not pass/fail)

- chess.com Rapid (10|0) rating tracked monthly.
- Target at +3 months: **+50 Elo** (380 → 430).
- Target at +6 months: **+100 Elo** (380 → 480).
- This is a *signal*: rating progression depends on many factors outside the app. We use it to detect *direction*, not to gate the MVP.

## Anti-criteria (what failure looks like)

- The app feels like a chore. *"Je dois forcer pour l'utiliser."*
- The LLM frequently gives generic, wrong, or misaligned answers — Romuald stops trusting it after week 1.
- Romuald falls back to chess.com or lichess to look things up because Chess-coach's explanations don't land.
- The catalogue feels too thin: Romuald wants a 4th opening within the first month and there's no path to it.

## Source

For the original sketch, see `README.md` §2 and §9.
