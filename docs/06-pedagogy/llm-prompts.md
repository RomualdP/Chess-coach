# LLM prompts (v0)

> **Status**: to define. Target 4–5 key prompts written and tested before MVP dev starts.

## Provider

Mistral (Mistral Small at MVP, can move to Mistral Medium if quality requires). French. Prompt caching enabled to control costs.

## Universal guardrails (system message scaffold)

- Audience: French-speaking adult beginner (~380 Elo). Tone: mentor, expert, accessible. Never condescending, never childish.
- Pedagogy first: explain *why*, not just *what*. Prefer principled justifications (development, centre, king safety, piece activity) over deep tactics when the position allows.
- **Never invent variations.** Only discuss moves and lines that come from the provided knowledge base or the provided Stockfish eval.
- **Never compute.** If the user asks for a calculation you don't have data for, say so and suggest checking with the practice mode or the engine.
- Keep responses short by default; expand on request.

## Prompts to draft

1. **Explain a main-line move** — input: opening meta + current ply (FEN, move, KB explanation) + optional Stockfish eval. Output: a 2–4 sentence explanation tailored to the user's level.
2. **Explain a deviation** — input: parent ply + deviation move + KB handling. Output: short refutation/handling explanation.
3. **Practice-mode feedback** — input: KB-correct move, user's move, Stockfish eval of both. Output: a single calibrated message — congratulate / nudge / correct.
4. **Free-position chat** — input: FEN + Stockfish eval (top 3 lines, depth 12–15) + user question. Output: grounded answer, explicitly refusing to go beyond the engine output.
5. **Opening selection helper** *(maybe)* — input: user-stated goals/style + catalogue. Output: 2–3 ranked recommendations with one-line rationale.

## Per-prompt deliverable

For each prompt:
- System message
- Variable inputs (typed, mirroring `@chess-coach/shared` schemas)
- 2–3 example inputs + expected outputs
- Failure modes to watch (hallucinated lines, over-confidence, wrong tone)
