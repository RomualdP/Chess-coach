# Knowledge base format

> **Status**: to define. **High-priority chantier** (blocks content authoring and seeding).

## Goal

A frozen JSON schema for opening entries, with one fully-validated example, that:
- Captures everything the LLM and the UI need to teach an opening to a beginner.
- Can be authored by a human in a reasonable amount of time.
- Validates against a Zod schema in `@chess-coach/shared`.

## Sketch (to validate)

Each opening file in `packages/content/openings/<slug>.json` should expose:

- `meta`: name, slug, ECO code(s), colour (white/black), recommended-for-beginner flag, summary
- `coreIdeas`: 3–6 short principles ("control e5", "prepare castling", "knights before bishops here because…")
- `mainLine`: ordered list of plies, each with FEN before, move (SAN/UCI), explanation, why-not alternatives
- `commonDeviations`: per branch point in the main line, list of plausible opponent deviations with refutation/handling
- `traps`: classic tactical motifs to know
- `middlegamePlans`: pawn breaks, key squares, piece routes typical of the resulting structure
- `assets` (optional): annotated arrows/squares for the UI

## Decisions to make

- [ ] Move encoding: SAN, UCI, or both?
- [ ] FEN before or after each move (or both)?
- [ ] How deep does the main line go (10 plies? 15?)?
- [ ] How are deviations linked to a parent ply (by index, by FEN, by ID)?
- [ ] Multilingual now or French-only at MVP?
- [ ] Validation: Zod schema in `@chess-coach/shared`, CI check on `packages/content`.

## Deliverable

This doc should end with:
1. A complete Zod schema (or a JSONSchema equivalent).
2. One full, validated example (likely the Italian Game).
