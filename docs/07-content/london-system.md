# London System — drafting plan

> Slug: `london-system` · Colour: White · ECO: A45 / D02 / D04.
> Third opening to author. Provides a full White repertoire when paired with the Italian Game (1.e4 e5 → Italian, everything else → London via 1.d4).

## Why this opening (and the case against it)

**For**:
- Easiest opening to learn at any level: same setup vs 80 % of Black tries.
- Reaches a stable middlegame structure within 6 moves.
- Almost no traps to avoid — perfect for someone who doesn't want to memorise.
- Pairs well with the Italian: Romuald can choose his first move depending on his mood (1.e4 → Italian, 1.d4 → London).

**Against**:
- Critique from stronger players: "samey", limited learning value, doesn't expose the player to varied structures.
- At 380 Elo this critique is noise — variety comes later. The London is right *for now*.

> Decision: **keep the London at MVP.** If, post-MVP, it feels like Romuald has plateaued because his White games are too monotonous, we'll add a more dynamic 1.d4 system in V2 (Trompowsky, or Colle-Zukertort).

## Target shape

- **12 plies**, against the most common Black setup (`...d5` + `...Nf6` + `...e6` + `...c5`).
- Suggested main line *(to confirm with Romuald)*:
  ```
  1. d4   d5
  2. Nf3  Nf6
  3. Bf4  e6
  4. e3   c5
  5. c3   Nc6
  6. Nbd2 Bd6
  ```
- The London is a *system*: the order of `Bf4 / e3 / c3 / Nf3 / Nbd2` is largely flexible. We pick **one canonical order** for the main line and treat alternative move orders as transpositions in the deviation list.

## Core ideas to write (target 4–5)

1. **Le London est un système**, pas une ligne. La structure de pions (d4+e3+c3) est l'identité ; l'ordre des coups est secondaire.
2. **Le fou en f4 d'abord, hors de la chaîne**. Sortir le fou avant `e3` est la marque distinctive (sinon il est enterré comme dans le Colle).
3. **Plan minoritaire** : `b4-b5` peut faire sauter le pion c5 noir et créer des faiblesses durables.
4. **Centre flexible** : on ne joue pas `e4` (ce serait un autre système). On vise plutôt `Ne5` à terme et la pression sur c-file.
5. **Roque court rapide**, comme partout.

## Deviations to cover (target 5–6)

The London faces *more* deviation diversity than the Italian, because Black has many setups vs `1.d4`. We need to cover the main families.

| `afterPly` | Deviation | Frequency | Reply / treatment |
| --- | --- | --- | --- |
| 1 (after 1.d4) | 1...Nf6 (delayed ...d5) | very_common | Continue Nf3, Bf4, e3, c3 — same plan; transposes back. |
| 1 | 1...g6 (KID setup) | common | Bf4 + e3 + c3 + h3 (avoid ...Nh5xf4); plan to play Nbd2-c4-e5. |
| 1 | 1...c5 (Benoni-style) | occasional | Decline with Nf3; refuse to enter sharp Benoni structures. |
| 3 (after 2.Nf3) | 2...Bf5 (early bishop sortie) | common | Reply with c4 or e3; punish the bishop's exposure. |
| 5 (after 3.Bf4) | 3...c5 (early central tension) | very_common | Reply with e3 (don't take cxd4 — strengthen, don't capture). |
| 7 (after 4.e3) | 4...Nh5 (chasing the bishop) | occasional | Reply with Bg5 or Be5; the bishop has good squares. |
| 9 (after 5.c3) | 5...Qb6 (pressure on b2) | very_common | Reply with Qb3 (offer queen trade) or Qc2; don't panic. |

> ⚠️ The London's deviation list is the longest of the three openings. Strongly consider authoring it last so the schema's `Deviation` shape is battle-tested.

## Traps to write (target 1)

The London has very few classical traps. Suggested:

1. **Le piège du cavalier en h5**. Lesson: si les Blancs ne jouent pas `h3` au bon moment, `...Nh5` capture le fou en f4 et casse la structure. Pédagogie : pourquoi `h3` est presque toujours utile dans le London.

(*Honest note*: the London is a system, not a tactical opening. We could omit `traps` entirely; `OpeningEntry` makes it optional. To revisit during authoring.)

## Middlegame plans to write (target 2–3)

1. **Plan minoritaire à l'aile dame** : `Rb1` puis `b4-b5`, échange en c6 ou faiblissement de c6. Cases clés : b5, c6.
2. **Plan central avec Ne5** : doubler les cavaliers (Nbd2-Nf3-Ne5), puis pression sur f7 si le Noir ouvre le centre. Cases clés : e5, f7.
3. **Plan de l'aile roi** *(plus rare, V2 ?)* : `Qe2 / Bd3 / h4-h5` quand le Noir a roqué petit et joué ...g6. À discuter — risqué pour un débutant.

## Open questions for review

- [ ] Confirm the canonical move order (`Nf3 → Bf4 → e3 → c3 → Nbd2` in this exact sequence) vs the more flexible `Bf4 → Nf3 → e3 → c3 → Nbd2`. Pedagogically, which is easier to remember?
- [ ] Should `traps` be empty for this opening, or keep the `Nh5` trap as didactic content? (Schema allows empty.)
- [ ] How do we handle the *Jobava London* (1.d4 d5 2.Nc3 Nf6 3.Bf4) — is that a separate opening or a deviation here? Recommend: separate opening, V2.
- [ ] Cover or omit the *Indian setup deviation* (1...Nf6 2.Nf3 g6 3.Bf4 Bg7 4.e3 d6 — King's Indian setup with no ...d5)? It's common at low Elo. Decision needed.

## Effort estimate

~4 hours, the longest of the three because of the deviation diversity. Recommended authoring order: **Italian → Caro-Kann → London** (so the schema is tested on simpler cases first).
