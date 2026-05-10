# Knowledge base format

> **Status**: v0 draft — schema proposed, partial example below. Once you validate the shape, we'll author the full 12-ply Italian Game example and lift the Zod schema into `@chess-coach/shared`.

## Goal

A frozen JSON schema for opening entries that:

- Captures everything the LLM and the UI need to teach an opening to a beginner.
- Is authorable by a human in a reasonable amount of time (~3h per opening).
- Validates against a Zod schema in `@chess-coach/shared`.
- Is the **single source of pedagogical truth** — Stockfish verifies, the LLM explains, but the KB decides what is taught.

## Decisions locked in

| Decision | Choice |
| --- | --- |
| Main-line depth | **12 plies (~6 full moves)** — covers the opening through the transition to a recognisable middlegame. |
| Explanation granularity | **Two levels per ply: `short` (1 sentence, always shown) + `long` (paragraph, on demand)**. The LLM can also expand further at runtime. |
| Deviations layout | **Flat array, each item references its parent ply via `afterPly`**. Simpler authoring, easier LLM consumption, the UI groups visually. |
| Move encoding | **Both SAN and UCI on every move.** SAN for humans, UCI for engine integration. |
| FEN tracking | **`fenBefore` on every ply** — lets the schema be self-validating (CI can replay and check), and the LLM gets the position directly. |
| Language | **French only at MVP** — plain strings, no i18n wrapper. |
| Frequency labels (deviations) | Enum: `very_common | common | occasional | rare`. |

## Top-level shape

```
opening:
├── meta              # identity (name, ECO, colour, summary, version)
├── coreIdeas         # 3–6 directing principles (pre-move guidance)
├── mainLine          # 12-ply sequence, one entry per ply
├── deviations        # opponent off-line replies, with our handling
├── traps             # classic tactical patterns to know
└── middlegamePlans   # typical plans after the opening transition
```

## Zod schema (to lift into `@chess-coach/shared`)

```ts
import { z } from "zod";

const Slug = z.string().regex(/^[a-z0-9]+(-[a-z0-9]+)*$/);
const Fen = z.string().min(10); // a stricter FEN regex can be added later
const San = z.string().min(1);  // e.g. "Nf3", "O-O", "exd5"
const Uci = z.string().regex(/^[a-h][1-8][a-h][1-8][qrbn]?$/);
const PlyIndex = z.number().int().min(1).max(40); // 1-indexed
const Verdict = z.enum(["best", "playable", "inaccurate", "mistake", "blunder"]);
const Frequency = z.enum(["very_common", "common", "occasional", "rare"]);
const Side = z.enum(["white", "black"]);
const Principle = z.enum([
  "centre", "development", "king_safety",
  "piece_activity", "pawn_structure", "tempo", "space",
]);

const Meta = z.object({
  slug: Slug,
  name: z.string(),                     // FR
  ecoCodes: z.array(z.string().regex(/^[A-E]\d{2}$/)).min(1),
  colour: Side,                         // which side this repertoire entry is for
  recommendedForBeginner: z.boolean(),
  summary: z.string(),                  // FR, 1–2 sentences
  version: z.string().regex(/^\d+\.\d+\.\d+$/),
  lastReviewed: z.string().regex(/^\d{4}-\d{2}-\d{2}$/),
});

const CoreIdea = z.object({
  id: Slug,
  title: z.string(),                    // FR, 4–8 words
  body: z.string(),                     // FR, 1–3 sentences
});

const Alternative = z.object({
  san: San,
  verdict: Verdict,
  comment: z.string(),                  // FR, why we don't pick it
});

const Ply = z.object({
  ply: PlyIndex,                        // 1-indexed within mainLine
  side: Side,                           // who plays this ply
  fenBefore: Fen,
  san: San,
  uci: Uci,
  explanation: z.object({
    short: z.string(),                  // FR, 1 sentence, always displayed
    long: z.string(),                   // FR, 1 paragraph, on demand
  }),
  principles: z.array(Principle).min(1),
  alternatives: z.array(Alternative).default([]),
});

const Deviation = z.object({
  id: Slug,
  afterPly: PlyIndex,                   // index in mainLine; deviation occurs at afterPly+1
  opponentSan: San,
  opponentUci: Uci,
  frequency: Frequency,
  ourReply: z.object({
    san: San,
    uci: Uci,
    explanation: z.string(),            // FR, why this reply works
  }),
  followUp: z.array(z.object({          // optional 0–4 plies of continuation
    san: San,
    uci: Uci,
    comment: z.string().optional(),
  })).max(4).default([]),
});

const Trap = z.object({
  id: Slug,
  name: z.string(),                     // FR
  lesson: z.string(),                   // FR, what the trap teaches
  setupFen: Fen.optional(),             // omit if from initial position
  moves: z.array(San).min(2),           // SAN sequence demonstrating the trap
  keyMoveIndex: z.number().int().min(0),// index of the punishing/critical move
});

const MiddlegamePlan = z.object({
  id: Slug,
  structure: z.string(),                // FR, e.g. "Pions e4+d3, Italienne moderne"
  plan: z.string(),                     // FR, 2–5 sentences
  keySquares: z.array(z.string().regex(/^[a-h][1-8]$/)).default([]),
});

export const OpeningEntry = z.object({
  meta: Meta,
  coreIdeas: z.array(CoreIdea).min(3).max(6),
  mainLine: z.array(Ply).length(12),    // exactly 12 plies
  deviations: z.array(Deviation),
  traps: z.array(Trap).default([]),
  middlegamePlans: z.array(MiddlegamePlan).min(1),
});

export type OpeningEntry = z.infer<typeof OpeningEntry>;
```

## Self-validation rules (CI)

A CI check on `packages/content/openings/*.json` should verify:

1. The file parses against `OpeningEntry`.
2. Replaying `mainLine` move-by-move from the standard initial position yields the declared `fenBefore` of each ply.
3. For each `Deviation`, `afterPly` is a valid index in `mainLine` and `opponentSan` is a legal move at that branch.
4. For each `Trap`, the move sequence is legal from `setupFen` (or initial position).
5. All `id` and `slug` values are unique within the file.
6. `version` is bumped whenever the file changes meaningfully (lint warning, not blocker).

These checks are pure chess.js + Zod — no engine needed.

## Partial example — Partie italienne (first 6 plies)

> Goal of this example: validate the shape. The full 12-ply version will replace this once you sign off on the schema.

```json
{
  "meta": {
    "slug": "italian-game",
    "name": "Partie italienne",
    "ecoCodes": ["C50"],
    "colour": "white",
    "recommendedForBeginner": true,
    "summary": "Ouverture classique pour les Blancs. Développement rapide des pièces mineures et pression immédiate sur f7.",
    "version": "0.1.0",
    "lastReviewed": "2026-05-10"
  },
  "coreIdeas": [
    {
      "id": "develop-fast",
      "title": "Développer vite, roquer tôt",
      "body": "On sort cavalier puis fou, puis on roque, avant tout autre projet. La sécurité du roi prime sur les ambitions tactiques."
    },
    {
      "id": "pressure-f7",
      "title": "Le fou en c4 vise f7",
      "body": "f7 est la case la plus faible du Noir au début de partie : seul le roi la défend. Le fou en c4 prépare des combinaisons sur cette case."
    },
    {
      "id": "claim-centre",
      "title": "Occuper le centre, puis le défendre",
      "body": "e4 et d3 (ou d4 selon les lignes) tiennent le centre sans s'exposer. On évite les sacrifices spéculatifs au niveau débutant."
    }
  ],
  "mainLine": [
    {
      "ply": 1,
      "side": "white",
      "fenBefore": "rnbqkbnr/pppppppp/8/8/8/8/PPPPPPPP/RNBQKBNR w KQkq - 0 1",
      "san": "e4",
      "uci": "e2e4",
      "explanation": {
        "short": "On prend le centre et on ouvre la diagonale du fou-roi.",
        "long": "1.e4 est le coup d'ouverture le plus principiel pour les Blancs : il occupe le centre, libère le fou en f1 et la dame, et crée une menace immédiate sur f5 et d5. C'est la base de la plupart des ouvertures classiques."
      },
      "principles": ["centre", "development"],
      "alternatives": [
        { "san": "d4", "verdict": "best", "comment": "Tout aussi bon, mais on garde 1.e4 pour la simplicité et les structures plus ouvertes, plus formatrices au niveau débutant." }
      ]
    },
    {
      "ply": 2,
      "side": "black",
      "fenBefore": "rnbqkbnr/pppppppp/8/8/4P3/8/PPPP1PPP/RNBQKBNR b KQkq - 0 1",
      "san": "e5",
      "uci": "e7e5",
      "explanation": {
        "short": "Réponse symétrique : le Noir conteste aussi le centre.",
        "long": "1...e5 est la réponse classique. Le Noir occupe le centre à son tour et entre dans les ouvertures ouvertes. D'autres réponses (c5 Sicilienne, e6 Française, c6 Caro-Kann) mènent à des structures très différentes — couvertes dans d'autres fiches."
      },
      "principles": ["centre", "development"]
    },
    {
      "ply": 3,
      "side": "white",
      "fenBefore": "rnbqkbnr/pppp1ppp/8/4p3/4P3/8/PPPP1PPP/RNBQKBNR w KQkq - 0 2",
      "san": "Nf3",
      "uci": "g1f3",
      "explanation": {
        "short": "Développement + attaque immédiate du pion e5.",
        "long": "2.Cf3 est le coup le plus principiel : il développe une pièce et attaque e5. Le Noir doit réagir (généralement par 2...Cc6 pour défendre e5)."
      },
      "principles": ["development", "centre"],
      "alternatives": [
        { "san": "Bc4", "verdict": "playable", "comment": "Possible, mais on préfère développer les cavaliers avant les fous (règle des débutants)." },
        { "san": "f4", "verdict": "inaccurate", "comment": "Le Gambit du Roi : trop tactique pour notre niveau, on évite." }
      ]
    },
    {
      "ply": 4,
      "side": "black",
      "fenBefore": "rnbqkbnr/pppp1ppp/8/4p3/4P3/5N2/PPPP1PPP/RNBQKB1R b KQkq - 1 2",
      "san": "Nc6",
      "uci": "b8c6",
      "explanation": {
        "short": "Le Noir défend e5 en développant.",
        "long": "2...Cc6 défend le pion e5 attaqué et développe une pièce. C'est la réponse de loin la plus courante. Les déviations (2...d6 Philidor, 2...Cf6 Petroff) sont traitées plus bas."
      },
      "principles": ["development", "centre"]
    },
    {
      "ply": 5,
      "side": "white",
      "fenBefore": "r1bqkbnr/pppp1ppp/2n5/4p3/4P3/5N2/PPPP1PPP/RNBQKB1R w KQkq - 2 3",
      "san": "Bc4",
      "uci": "f1c4",
      "explanation": {
        "short": "Le coup-signature de l'Italienne : on vise f7.",
        "long": "3.Fc4 développe le fou sur sa meilleure diagonale et vise immédiatement f7. C'est ce coup qui définit la Partie italienne. Sans Fc4, on entre dans d'autres ouvertures (Espagnole avec 3.Fb5, etc.)."
      },
      "principles": ["development", "piece_activity"]
    },
    {
      "ply": 6,
      "side": "black",
      "fenBefore": "r1bqkbnr/pppp1ppp/2n5/4p3/2B1P3/5N2/PPPP1PPP/RNBQK2R b KQkq - 3 3",
      "san": "Bc5",
      "uci": "f8c5",
      "explanation": {
        "short": "Symétrie : le Noir développe son fou de la même façon.",
        "long": "3...Fc5 mène au Giuoco Piano (« jeu tranquille »), la ligne principale et la plus saine pour les deux camps. 3...Cf6 (Défense des deux cavaliers) est la déviation à connaître — voir plus bas."
      },
      "principles": ["development", "piece_activity"]
    }
  ],
  "deviations": [
    {
      "id": "dev-3-nf6-two-knights",
      "afterPly": 5,
      "opponentSan": "Nf6",
      "opponentUci": "g8f6",
      "frequency": "very_common",
      "ourReply": {
        "san": "d3",
        "uci": "d2d3",
        "explanation": "On joue calmement le 'Giuoco Pianissimo' avec d3. Évite la théorie pointue (4.Cg5 attaque sur f7, intéressant mais tactique) et garde une structure saine."
      },
      "followUp": [
        { "san": "Bc5", "comment": "Le Noir continue son développement normal." },
        { "san": "c3", "comment": "On prépare d4 plus tard." }
      ]
    },
    {
      "id": "dev-2-d6-philidor",
      "afterPly": 3,
      "opponentSan": "d6",
      "opponentUci": "d7d6",
      "frequency": "occasional",
      "ourReply": {
        "san": "d4",
        "uci": "d2d4",
        "explanation": "Contre la Philidor, on attaque immédiatement le centre. Le pion d6 du Noir est passif et ne défend pas e5 dynamiquement."
      },
      "followUp": []
    }
  ],
  "traps": [
    {
      "id": "scholars-mate",
      "name": "Mat du berger",
      "lesson": "Pourquoi on ne sort pas la dame trop tôt — et comment punir l'adversaire qui le fait. La dame en h5 est attaquée par Cf6, qui défend f7 et menace la dame.",
      "moves": ["e4", "e5", "Bc4", "Nc6", "Qh5", "Nf6"],
      "keyMoveIndex": 5
    }
  ],
  "middlegamePlans": [
    {
      "id": "italianissimo-centre",
      "structure": "Pions blancs e4+d3, structure du Giuoco Pianissimo",
      "plan": "Roquer du côté roi, jouer Te1 et Cbd2, puis manœuvrer le cavalier en f1-g3. À long terme, préparer la poussée d4 quand le centre est bien soutenu, ou b4-b5 sur l'aile dame contre un fou en c5.",
      "keySquares": ["d4", "f5", "g3"]
    }
  ]
}
```

## What's intentionally not in the schema (yet)

- **Branching deeper than `Deviation.followUp` (max 4 plies).** Full sub-trees are V2 — at MVP a deviation is "you get this position, here's why our reply works", not a full second opening.
- **Multilingual content.** All string fields are FR plain text. If we later open to other languages, we'll wrap fields in `{ fr, en, ... }` rather than duplicating files.
- **Audio/video assets.** Out of scope for MVP.
- **Spaced-repetition metadata.** That's user-state data, lives in `userProgress` (DB), not in the KB.

## Open questions

- [ ] Validate the schema with a real chess-aware reviewer (you, then maybe a stronger player) before locking it.
- [ ] Confirm the 12-ply hard cap. If it ever feels too rigid for a specific opening (e.g. the London System where the structure stabilises at ply 8), we can relax to `length(8, 14)`.
- [ ] Decide whether `traps` is required (currently optional) or required (`min(1)`). Probably required — every opening has at least one didactic trap.

## Next deliverable (after sign-off)

1. Lift `OpeningEntry` schema into `packages/shared/src/schemas/opening.ts`.
2. Author the **full 12-ply Italian Game** entry as `packages/content/openings/italian-game.json`.
3. Wire the CI validation script (chess.js replay + Zod parse).
