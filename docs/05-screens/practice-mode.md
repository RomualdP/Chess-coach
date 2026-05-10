# Screen — Mode pratique

> Route: `/openings/:slug/practice`. Drill the main line of an opening with calibrated feedback.

## Purpose

Quiz the user on the 12-ply main line of an opening. Each user move is checked against the knowledge base (and Stockfish). Feedback is short and calibrated: validate principled correct moves, gently nudge playable alternatives, correct mistakes with one-sentence reasoning.

**One primary action**: jouer le bon coup. Everything else is secondary.

## Reference

- Global brief: `docs/04-design-system.md`.
- Cross-screen rules: `docs/05-screens/README.md`.
- LLM behaviour: `docs/06-pedagogy/llm-prompts.md` § "practice_feedback".

## Desktop layout (≥ 1024 px)

```
┌────────────────────────────────────────────────────────────────────┐
│ Header                                                              │
├────────────────────────────────────────────────────────────────────┤
│  ◂ Partie italienne                                                 │
│                                                                     │
│  Mode pratique                                Score : 4 / 5         │
│  Demi-coup 5 / 12                                                   │
│  ─────────────────────                                              │
│                                                                     │
│  ┌──────────────────────────┐   ┌──────────────────────────────┐    │
│  │                          │   │  À toi de jouer (Blancs)      │    │
│  │                          │   │                              │    │
│  │      [chessboard]        │   │  Joue le coup-signature de    │    │
│  │       560 × 560 px       │   │  cette ouverture.             │    │
│  │                          │   │                              │    │
│  │      (interactive)       │   │  ─── feedback bubbles ───    │    │
│  │                          │   │                              │    │
│  │                          │   │  ✓  Cc6 : exact. Le Noir     │    │
│  │                          │   │     défend e5 en développant.│    │
│  └──────────────────────────┘   │                              │    │
│                                 │  ◐  Cf3 : jouable, mais on   │    │
│  ◂ ply 4                ply 6 ▸ │     préfère 2.Cf3 d'abord.   │    │
│  ●●●●● ○○○○○○○                  │                              │    │
│                                 │  ✗  Dh5 : trop tôt — la dame │    │
│                                 │     se fait chasser.         │    │
│                                 │                              │    │
│                                 │  [Indice (1/3)]  [Abandonner]│    │
│                                 └──────────────────────────────┘    │
│                                                                     │
│                                                                     │
└────────────────────────────────────────────────────────────────────┘
```

- Same two-column layout as Opening study (board left, panel right) for **muscle memory** between screens.
- Top-right: live `Score : N/M` (correct attempts vs total plies-completed).
- Below board: same `PlyNavigator` (read-only forward — you can revisit but not skip ahead).
- Right panel: prompt at top, then a stream of feedback bubbles (latest at top), then action buttons (`Indice`, `Abandonner`).

## Mobile layout (< 768 px)

```
┌──────────────────────┐
│ Header               │
├──────────────────────┤
│ ◂ Partie italienne    │
│ Mode pratique         │
│ Demi-coup 5 / 12      │
│ Score 4/5             │
│                      │
│ ┌──────────────────┐ │
│ │   [chessboard]   │ │
│ │   ~358 × 358 px  │ │
│ └──────────────────┘ │
│ ●●●●●○○○○○○○         │
│                      │
│ À toi de jouer (♔)   │
│ Joue le coup-        │
│ signature de cette   │
│ ouverture.           │
│                      │
│ ─── feedback ───     │
│ ✓ Cc6 : exact …      │
│ ◐ Cf3 : jouable …    │
│ ✗ Dh5 : trop tôt …   │
│                      │
│ [Indice]  [Abandonner│
│                      │
└──────────────────────┘
```

- Tighter vertical layout. Feedback stream is scrollable inline (not a separate panel).
- The board sticks to the top during scroll (sticky positioning) so the user can always see the position while reading.

## Components

| Component | Role | States | Primitive |
| --- | --- | --- | --- |
| `Chessboard` | Interactive — accepts user moves. | idle / drag / dropped | custom |
| `PlyPrompt` | The current task (e.g. "À toi de jouer"). | — | composed |
| `FeedbackBubble` | One feedback message. | success / playable / mistake / hint | custom + `Toast`-like |
| `HintButton` | Reveals the next-step principle. | enabled / disabled (after 3 hints) | `Button` |
| `GiveUpButton` | Reveals the answer + advances. | — | `Button` (outline) |
| `ScoreBadge` | Live score. | — | `Badge` |
| `SessionSummary` | End-of-session card. | — | `Card` |
| `PlyNavigator` | Same as Opening study (here forward-only on plies the user has completed). | — | custom |

### `FeedbackBubble` variants

| Variant | Icon | Colour token | Trigger |
| --- | --- | --- | --- |
| `success` | check | `--accent` (Classique gold / Moderne blue / Sombre violet) | `userMove === ply.san` |
| `playable` | half-circle | muted-foreground | `userMove ∈ ply.alternatives` with `verdict: playable` |
| `mistake` | x | `--destructive` | none of the above; engine eval drop > 100 cp |
| `hint` | lightbulb | `--secondary` | user requested a hint |

Bubble copy comes from `practice_feedback` prompt (LLM, single sentence). Fallback text (engine offline) is templated.

### `SessionSummary`

After ply 12 (or after `Abandonner`), show:

```
Session terminée.

Partie italienne · 12 demi-coups
Score : 9 / 12

Coups maîtrisés (3) : 1.e4, 2.Cf3, 3.Fc4
À retravailler (3)  : ply 5, ply 7, ply 11

[ Recommencer ]   [ Retour à l'ouverture ]
```

`Recommencer` resets the session in place. `Retour` navigates to `/openings/:slug`.

## Sample data

```ts
type AttemptOutcome =
  | { kind: "correct" }
  | { kind: "playable"; expectedSan: string }
  | { kind: "mistake"; expectedSan: string; userMoveCp: number; expectedCp: number };

const session = {
  openingSlug: "italian-game",
  attempts: [
    { ply: 1, userSan: "e4", outcome: { kind: "correct" }, attemptCount: 1 },
    { ply: 3, userSan: "Cf3", outcome: { kind: "correct" }, attemptCount: 1 },
    { ply: 5, userSan: "Fb5", outcome: { kind: "playable", expectedSan: "Fc4" }, attemptCount: 1 },
    { ply: 5, userSan: "Fc4", outcome: { kind: "correct" }, attemptCount: 2 },
    { ply: 7, userSan: "d4", outcome: { kind: "mistake", expectedSan: "d3", userMoveCp: -45, expectedCp: 18 }, attemptCount: 1 },
  ],
  scoreAtPly: { 1: 1, 3: 1, 5: 1, 7: 0 },
};
```

## Interactions

1. **Drop a piece** → submit the move.
2. **Correct move** → success bubble appears; ply auto-advances after 1.2 s.
3. **Playable alternative** → soft-warning bubble: *"Jouable, mais on préfère X parce que…"*. The user can choose to keep their move (advances) or retry.
4. **Mistake** →
   - 1st attempt: hint-only bubble (principle-pointer, no answer).
   - 2nd attempt: more specific hint (case or piece).
   - 3rd attempt: reveal the answer + 1-sentence explanation; advance after the user reads.
5. **`Indice`** → manually request a hint (counts toward the 3-attempt budget).
6. **`Abandonner`** → reveal the answer + advance, no hint cost (already capped at 3 hints).
7. **`←`** key → revisit the previous ply (read-only, can't change the score).
8. **End-of-session** → `SessionSummary` replaces the right panel.

## Stockfish involvement

- Practice mode runs Stockfish locally per move at depth 12–15, MultiPV 1.
- Used to:
  - Confirm the user's move isn't a tactical blunder when it differs from the KB.
  - Compute eval delta (cp loss) used to decide `playable` vs `mistake`.
- Not surfaced numerically to the user; just informs feedback severity.

## States

### Loading
- Board skeleton + skeleton lines in the panel.

### Engine offline (Stockfish failed to load)
- Banner above the board: *"Le moteur n'est pas chargé. Le mode pratique fonctionne sans validation tactique."* + `Réessayer` to reload the engine.
- Practice continues using KB-only validation; mistakes are flagged purely on KB mismatch.

### LLM offline
- Feedback bubbles use the templated fallback copy. A muted footnote: *"Explications complètes indisponibles."*

## Out of scope

- Spaced-repetition scheduling — V2 (see `docs/06-pedagogy/llm-prompts.md` for the placeholder note).
- Mixed-opening practice (random ply across multiple openings) — V2.
- Practice on deviations or middlegame plans — V2.
- Move scrambler / random starting ply — V2.
- Multiplayer or shared scoring — never.

## Open questions

- [ ] Should playable alternatives count as "correct" in the score, or as "alt-correct"? Recommend the latter, with a distinct count (`Score 9/12 (+2 alt)`).
- [ ] How many hints are allowed per ply? v0 = 3.
- [ ] Should the user be able to **enable strict mode** (no hints, mistakes lock you out)? Not at MVP.
