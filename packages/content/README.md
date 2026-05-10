# `@chess-coach/content`

Hand-written opening knowledge base — versioned in git, consumed by the API (seeding) and indirectly by the frontend (via API).

## Layout (target)

```
packages/content/
├── openings/
│   ├── italian-game.json
│   ├── caro-kann.json
│   └── london-system.json   # candidate, TBD
└── schema/                  # Zod schema mirror, single source of truth lives in @chess-coach/shared
```

## Authoring rules

- Format defined in `docs/06-pedagogy/knowledge-base-format.md`.
- Validated against the Zod schema from `@chess-coach/shared`. A CI check should fail the build if any entry breaks the schema.
- Tone: French, mentor-like, accessible. No condescension, no infantilisation.
- Pedagogy first: prefer principled moves over engine-best when they diverge at low depth.
- All claims about positions must be verifiable against Stockfish at depth 12–15. If they aren't, do not write them.

## Selecting the first 3 openings

See `docs/07-content/README.md`.
