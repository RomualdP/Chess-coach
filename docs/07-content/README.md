# 07 — Content

This folder hosts the **drafting plan** for opening knowledge-base entries. The validated JSON entries themselves live in `packages/content/openings/`.

## Selecting the first 3 openings

Criteria for the MVP set (TBD, working draft):
1. Suitable for a beginner playing 10-min rapid at ~380 Elo
2. Complementary coverage: at least one White repertoire and one Black response
3. Reaches positional middlegames (not razor-sharp theory)

Working candidates (per `README.md` §11):
- **Italian Game** (White) — classical, principled, rich in pedagogy
- **Caro-Kann** (Black vs 1.e4) — solid, beginner-friendly structures
- **London System** *(candidate, to confirm)* (White) — easy to learn, but sometimes critiqued as too samey

## Per-opening drafting plan

For each opening, we'll add a `<slug>.md` here containing:

- Why this opening for a beginner
- Target depth (number of plies in the main line)
- List of common deviations to cover
- Traps to include
- Middlegame plans to teach
- Open questions for content review

The validated JSON output goes to `packages/content/openings/<slug>.json`.

## Open questions

- [ ] Confirm or replace the London System
- [ ] Do we want a third entry to be a **second** Black response (e.g. vs 1.d4) or a second White option?
- [ ] Order of authoring (which one first?)
