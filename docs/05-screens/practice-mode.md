# Screen — Practice mode

> **Status**: stub. Quiz on the main line of an opening.

## Purpose

Interactive quiz: the user plays the main line move-by-move; the app validates with Stockfish + the knowledge base, and explains misses.

## To define

- [ ] Quiz session model (one opening per session? mixed? user-picked length?)
- [ ] Validation logic: knowledge-base move = correct; principled-but-not-main-line = "good but not the line"; engine-tactical-best diverging from the line = explain why we still recommend the line
- [ ] Hint system (one principled hint per move? cost-based?)
- [ ] Wrong-move handling: revert / retry / show + continue
- [ ] Spaced-repetition or simple linear walkthrough at MVP? (algorithm reported in `README.md` §10 as TBD)
- [ ] End-of-session summary: what was mastered, what to revisit
- [ ] Persistence: update `userProgress` per ply

## Source

For now, see `README.md` §2 — "Les 4 écrans du MVP" (3).
