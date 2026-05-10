# Stockfish configuration

> **Status**: stub. Per-scenario config to be locked in.

## Role

Stockfish is a **verifier**, not an oracle. It validates positions and moves; it does not decide what the user should learn.

## Constants

- Engine: stockfish.wasm in a Web Worker (frontend).
- Wrapped behind a `ChessEngineService` interface (allows future swap to server-side Stockfish or Maia).

## Per-scenario config (to lock in)

| Scenario                       | Depth   | MultiPV | Notes |
| ------------------------------ | ------- | ------- | ----- |
| Practice-mode move validation  | 12–15   | 1       | Compare user move vs KB move; flag tactical blunders only |
| Free-position chat             | 12–15   | 3       | Provide top-3 lines as LLM input |
| Deviation handling             | 12–15   | 1       | Confirm KB refutation still holds |
| Background analysis (V2)       | TBD     | TBD     | Not at MVP |

## Why depth 12–15 (and not deeper)

Beginner-first pedagogy: at low depth, principled moves and engine-best moves coincide more often. Going deeper surfaces sharp computer moves that don't generalise to a beginner's understanding.

## Open questions

- [ ] Time budget per call (fixed depth vs fixed time)?
- [ ] How do we surface eval to the user — never, on hover, on demand, always-on?
- [ ] When (if ever) do we call Stockfish from the backend?
