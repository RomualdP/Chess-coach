# 07 — Content

Drafting plans for the opening knowledge-base entries. The validated JSON entries themselves live in `packages/content/openings/`.

## The 3 first openings (locked)

| Slug | Colour | Why | Drafting plan | JSON file (target) |
| --- | --- | --- | --- | --- |
| `italian-game` | White | Active piece play, attack/development showcase, dominant at low Elo. | [`italian-game.md`](./italian-game.md) | `packages/content/openings/italian-game.json` |
| `caro-kann` | Black | Solid, forgiving, the strategic counterpart to the Italian. Covers vs 1.e4. | [`caro-kann.md`](./caro-kann.md) | `packages/content/openings/caro-kann.json` |
| `london-system` | White | Easy-to-learn system covering everything that isn't 1...e5. Pairs with the Italian for a near-complete White repertoire. | [`london-system.md`](./london-system.md) | `packages/content/openings/london-system.json` |

### Why this set

For a 380 Elo player playing 10-min Rapid, this trio yields:

- A **complete White repertoire**: 1.e4 against 1...e5 (Italian), 1.d4 against everything else (London).
- A **principled Black response vs 1.e4** (Caro-Kann), the most common opening Romuald will face.
- **Vs 1.d4 in Black**, no specific repertoire at MVP — at this level, sound development beats preparation. We'll add a Black-vs-1.d4 entry in V1.1 if needed.

A single trade-off accepted: at MVP Romuald has no specific reply to 1.d4 with Black. He'll fall back on principles (...Nf6, ...e6, ...d5, ...Be7, ...O-O) and the LLM in free-position chat. Tracked as a known gap.

## Per-opening drafting plan structure

Each `<slug>.md` covers:

1. **Why this opening** — pedagogical justification.
2. **Target shape** — exactly 12 plies (per `OpeningEntry` schema), suggested main line.
3. **Core ideas to write** — target count of `coreIdea` entries with proposed titles.
4. **Deviations to cover** — table of `afterPly` × `opponentSan` × frequency × reply approach.
5. **Traps to write** — proposed trap entries (or rationale for leaving the section empty).
6. **Middlegame plans to write** — proposed `middlegamePlan` entries.
7. **Open questions** — what needs Romuald's chess judgement before authoring.
8. **Effort estimate** — rough hours.

## Recommended authoring order

1. **Italian Game** (~3 h). Simplest deviation graph, validates the schema by use.
2. **Caro-Kann** (~3 h). Switches colour and mindset; tests `coreIdeas` for defensive openings.
3. **London System** (~4 h). Largest deviation list, the schema's stress test.

Total: ~10 hours of focused authoring + Stockfish cross-checking.

## Authoring workflow (target)

For each opening:

1. Read the corresponding plan in this folder.
2. Resolve any "Open questions" with Romuald (or ourselves if non-controversial).
3. Draft `packages/content/openings/<slug>.json` against the `OpeningEntry` Zod schema.
4. Run a (yet-to-be-built) script that:
   - Validates the JSON against `OpeningEntry`.
   - Replays every `mainLine` and `deviation.followUp` with chess.js, checking `fenBefore` consistency.
   - Cross-checks every cited variation with Stockfish at depth 12–15 to flag tactical errors.
5. Review the generated LLM explanations on a couple of plies (sanity check the prompts).
6. Commit, with `contentVersion` bump.

## Open meta-questions

- [ ] **Authoring tooling**: free-form JSON editing in VS Code, or a tiny CLI/UI to scaffold an entry from a PGN? Probably free-form at MVP, tooling at V2.
- [ ] **Linting**: where the validation script lives — `apps/api` (TS) or a standalone `tools/` package? Recommend `apps/api` since it shares the schema.
- [ ] **CI**: should `packages/content` validation block the build? Yes — bad content shipped to prod is the worst-case failure.
