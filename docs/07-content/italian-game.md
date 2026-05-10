# Italian Game — drafting plan

> Slug: `italian-game` · Colour: White · ECO: C50 (with C53/C54 for sub-variants).
> First opening to author — drives schema validation.

## Why this opening

- **Principle showcase.** Develop minor pieces, fight for the centre, target f7, prepare king safety. Every concept a 380 Elo player must internalise is on display.
- **Low forced-theory load.** The Giuoco Pianissimo (slow Italian) leads to playable middlegames without razor-sharp memorisation.
- **Statistically dominant at low Elo.** 1.e4 e5 is the most common reply at < 1000 Elo on chess.com Rapid, so this opening covers a large share of Romuald's actual games.

## Target shape

- **12 plies**, ending on a recognisable Giuoco Pianissimo middlegame structure (pawns e4+d3, knights d2+c3, both sides castled short).
- Suggested main line *(to confirm with Romuald)*:
  ```
  1. e4   e5
  2. Nf3  Nc6
  3. Bc4  Bc5
  4. d3   Nf6
  5. Nc3  d6
  6. O-O  O-O
  ```
- Why this exact line: the Black side mirrors White's plan, so each ply teaches a single concept without competing tensions. We trade a bit of "objectively most popular" for "easier to teach".

## Core ideas to write (target 4)

1. Développer vite, roquer tôt.
2. Le fou en c4 vise f7.
3. Occuper le centre, le défendre avant de l'étendre.
4. Symétrie ≠ égalité : qui développe le plus utilement gagne du tempo.

## Deviations to cover (target 5)

| `afterPly` | Deviation | Frequency | Note |
| --- | --- | --- | --- |
| 3 (after 2.Nf3) | 2...d6 (Philidor) | occasional | Reply with d4, exploit the passive d6. |
| 3 | 2...Nf6 (Petroff) | common | Reply with Nxe5, then handle d6 / Nxe4. |
| 5 (after 3.Bc4) | 3...Nf6 (Two Knights) | very_common | Reply with d3 (Pianissimo), avoid 4.Ng5 theory. |
| 7 (after 4.d3) | 4...Be7 | occasional | Calm: continue with Nc3, O-O. |
| 7 | 4...Nf6 (transposition into mainline ply 7) | very_common | Mark as "leads to main line". |

> Other rare lines (Bird's Defence 3...Nd4, Hungarian 3...Be7) can be omitted at v1 — flag in `OpeningEntry.meta.summary` that uncommon Black tries default to "develop sensibly, don't panic".

## Traps to write (target 2)

1. **Mat du berger** (defensive). Lesson: pourquoi sortir la dame trop tôt est puni. Sequence: `1.e4 e5 2.Bc4 Nc6 3.Qh5 Nf6 — la dame est attaquée, le mat est paré`. Key index: 5 (3...Nf6).
2. **Légal's mate** (offensive — *playable* example, the user is on the giving end). Lesson: comment le pion en e5 mal défendu peut entraîner un sacrifice de dame gagnant si le Noir cloue le cavalier sans précaution.

## Middlegame plans to write (target 2)

1. **Plan central**: après le roque, jouer `Re1 + Nbd2 + Nf1-g3` puis casser au centre avec `d4` quand soutenu. Cases clés : d4, f5.
2. **Plan ailier dame**: `a3 + b4` pour repousser le fou en c5, gagner de l'espace à l'aile dame. Cases clés : b5, c5.

## Open questions for review

- [ ] Confirm the suggested 12-ply main line (Giuoco Pianissimo with `5.Nc3 d6 6.O-O O-O`) vs the more popular `5.c3` (Italian classical) — `c3` is theoretically richer but pedagogically heavier.
- [ ] Should the Two Knights deviation be a *deviation* or a *parallel main line*? It's almost as common as 3...Bc5 at low Elo. Compromise: keep it as a deviation with a 4-ply `followUp` so the student gets a clear picture.
- [ ] Include or omit the Evans Gambit (3...Bc5 4.b4!?) as a "do not play this at your level" cultural note in `coreIdeas`?

## Effort estimate

~3 hours of authoring, assuming the schema is locked. Includes Stockfish cross-check at depth 12–15 of every claim.
