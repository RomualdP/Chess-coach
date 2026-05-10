# Screen — Free position / Chat

> **Status**: stub. Sandbox to ask the LLM about any position.

## Purpose

Set up an arbitrary position (drag pieces, paste FEN, or play moves), ask the LLM about it, get an explanation grounded in a Stockfish eval.

## To define

- [ ] Position setup methods: play from start, paste FEN, free-place pieces
- [ ] Layout: board + chat side-by-side (desktop) / stacked (mobile)
- [ ] Conversation persistence: per-position thread? user-keyed history? ephemeral?
- [ ] Stockfish budget: when to call (on each user message? on demand?), depth
- [ ] LLM prompt: see `06-pedagogy/llm-prompts.md` — "free-position" prompt
- [ ] Guardrails: refuse to invent variations the engine hasn't checked
- [ ] States: empty, thinking, error (LLM down, engine timeout)

## Source

For now, see `README.md` §2 — "Les 4 écrans du MVP" (4).
