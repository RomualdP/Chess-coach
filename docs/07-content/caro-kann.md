# Caro-Kann — drafting plan

> Slug: `caro-kann` · Colour: Black · ECO: B10–B19 (we focus on the Classical, B18).
> Second opening to author. Covers the most common attacking choice (1.e4) for the user's opponents.

## Why this opening

- **Solid by design.** Hard-to-break pawn structure (c6+d5+e6), good bishop comes out *before* being locked in.
- **Forgiving for a beginner.** Strategic mistakes don't immediately lose material. There's almost always a "calm consolidating move" available.
- **Pedagogical contrast** with the Italian Game (active piece play). Romuald learns *both* the attacking mindset (Italian) *and* the defensive mindset (Caro-Kann), one with each colour.

## Target shape

- **12 plies**, in the **Classical Variation** (4...Bf5).
- Suggested main line *(to confirm with Romuald)*:
  ```
  1. e4   c6
  2. d4   d5
  3. Nc3  dxe4
  4. Nxe4 Bf5
  5. Ng3  Bg6
  6. h4   h6
  ```
- Why end at `6...h6`: stable structure reached, the next moves (Nf3, Bd3, ...e6, ...Nd7) are interchangeable and belong to the middlegame plan rather than the opening tree. Stopping at ply 12 gives a clean teaching break.

## Core ideas to write (target 4)

1. La structure Caro-Kann : le pion en c6 prépare ...d5 sans bloquer le fou-dame.
2. Le « bon fou » sort vite : 4...Ff5 avant d'être enterré derrière ...e6.
3. Solide ne veut pas dire passif : ...e6 + ...Cd7 + ...Cgf6 + ...Fd6/...Fe7, puis le roque, puis on cherche ...c5.
4. Pourquoi accepter d'échanger en e4 (3...dxe4) plutôt que de défendre : l'échange ouvre la diagonale du fou de c8 et clarifie la structure.

## Deviations to cover (target 5)

| `afterPly` | Deviation | Frequency | Note |
| --- | --- | --- | --- |
| 2 (after 1.e4) | (no deviation — c6 is forced by us) | — | — |
| 4 (after 2.d4) | 3.exd5 (Exchange Variation) | very_common | Reply with cxd5; structure devient symétrique, jouer ...Cc6, ...Fg4 ou ...Ff5. |
| 4 | 3.e5 (Advance Variation) | very_common | Reply with Bf5; sortir le fou avant ...e6. |
| 4 | 3.f3 (Fantasy / Tartakower) | occasional | Reply with e6 ou dxe4; éviter les complications tactiques. |
| 6 (after 3.Nc3) | 3...Nf6 (Two Knights / Tartakower) | rare-occasional | Sub-line si Romuald veut éviter 3...dxe4 ; à discuter, peut-être omettre. |
| 8 (after 4.Nxe4) | 4...Nf6 (échange de cavaliers) | occasional | Reply with Nxf6+ ; mène à des structures différentes (gxf6 vs exf6). |

> The Advance and Exchange variations are critical to cover well — they account for ~40 % of practical Caro-Kann games at low Elo.

## Traps to write (target 1–2)

1. **Sortie prématurée en h5** *(à connaître pour ne pas y tomber)*. Lesson: après `4...Bf5 5.Ng3 Bg6 6.h4 h6 7.Nf3?? Nf6??`, le coup blanc `8.Ne5` a été testé et ne marche pas — *mais* sans `6...h6` les Blancs peuvent jouer `7.h5 Bh7 8.Ne5` avec menaces sur f7. Lesson: pourquoi `6...h6` est obligatoire.
2. *(Optional)* **Échange précipité du fou** : démontrer pourquoi `5...Bxe4` (attiré par le gain temporaire d'un pion) est mauvais sur la 6e tentative — le fou ne devrait *jamais* abandonner sa diagonale tôt.

## Middlegame plans to write (target 2)

1. **Plan classique Caro-Kann** : ...e6, ...Nd7, ...Ngf6, ...Bd6 ou ...Be7, roque court, puis ...c5 quand le moment est venu. Cases clés : c5, d5, e6.
2. **Plan vs Exchange Variation** : structure symétrique (pions c6+d5 et c2+d4). Plan : ...Nc6, ...Bg4 (ou ...Bf5), pression sur la colonne c. Cases clés : c-file, e4.

## Open questions for review

- [ ] Confirm Classical (4...Bf5) over the Advance + ...Bf5 / Exchange + ...Nc6 alternatives. Classical is the canonical "first Caro-Kann to learn".
- [ ] Decide whether `3...Nf6` (Tartakower) is worth covering at all — it's < 5 % at our target Elo.
- [ ] Whether to include a "vs 1.d4" footnote: should we say "this fiche only covers vs 1.e4; vs 1.d4 develop principially" or stay silent?

## Effort estimate

~3 hours, similar to the Italian Game. Slight extra effort to verify the Advance and Exchange deviations are pedagogically tight (they're full sub-openings in disguise).
