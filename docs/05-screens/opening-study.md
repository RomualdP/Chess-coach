# Screen — Opening study

> Route: `/openings/:slug`. The core screen of the app. Spend most time here.

## Purpose

Layered, navigable study of one opening. The user reads core ideas, walks the 12-ply main line move-by-move, drills into deviations, scans traps and middlegame plans, and can ask the LLM follow-up questions on any ply.

**One primary action**: avancer dans la ligne principale (next ply). All other actions are secondary.

## Reference

- Global brief: `docs/04-design-system.md`.
- Cross-screen rules: `docs/05-screens/README.md`.
- Knowledge-base data shape: `docs/06-pedagogy/knowledge-base-format.md`.

## Desktop layout (≥ 1024 px)

```
┌────────────────────────────────────────────────────────────────────┐
│ Header                                                              │
├────────────────────────────────────────────────────────────────────┤
│  ◂ Mon répertoire                                                   │
│                                                                     │
│  Partie italienne                              Mode pratique →    │
│  Blancs · C50 · v0.1.0                                              │
│  ─────────────────────                                              │
│                                                                     │
│  ┌──────────────────────────┐   ┌──────────────────────────────┐    │
│  │                          │   │  [Idées · Ligne · Déviations │    │
│  │                          │   │   · Pièges · Plans]          │    │
│  │      [chessboard]        │   │                              │    │
│  │       560 × 560 px       │   │  Demi-coup 5 / 12             │    │
│  │                          │   │   ── 3.Fc4                   │    │
│  │                          │   │   On vise f7. Le coup-       │    │
│  │                          │   │   signature de l'Italienne.  │    │
│  │                          │   │                              │    │
│  │                          │   │   [En savoir plus ▾]         │    │
│  │                          │   │                              │    │
│  └──────────────────────────┘   │  ────────────────             │    │
│                                 │   Idées en jeu :              │    │
│  ◂ ply 4                ply 6 ▸ │   · Développer vite           │    │
│  ●●●●● ○○○○○○○                  │   · Pression sur f7          │    │
│                                 │                              │    │
│                                 │  [Ouvrir le mini-chat]       │    │
│                                 └──────────────────────────────┘    │
└────────────────────────────────────────────────────────────────────┘
```

- Two columns: board left (~560 × 560 px), content panel right (~440 px wide).
- The `◂ Mon répertoire` link is the back chevron (cross-screen rule).
- The `Mode pratique →` button (top right) is a secondary action (outline button).
- Below the board: ply navigation arrows and a 12-dot progress strip (filled = studied, half = seen, empty = not_seen).
- Content panel is tabbed: `Idées · Ligne · Déviations · Pièges · Plans`. Default tab: `Ligne`.
- Mini-chat is collapsed by default; opens as a popover anchored to the panel, OR slides as a sheet from the right.

## Mobile layout (< 768 px)

```
┌──────────────────────┐
│ Header               │
├──────────────────────┤
│ ◂ Retour              │
│ Partie italienne      │
│ Blancs · C50          │
│                      │
│ ┌──────────────────┐ │
│ │   [chessboard]   │ │
│ │   ~358 × 358 px  │ │
│ └──────────────────┘ │
│ ◂ ply 4    ply 6 ▸   │
│ ●●●●●○○○○○○○         │
│                      │
│ [Idées·Ligne·Dév·…]  │
│                      │
│ Demi-coup 5 / 12     │
│ ── 3.Fc4             │
│ On vise f7. Le coup- │
│ signature de         │
│ l'Italienne.         │
│ [En savoir plus ▾]   │
│                      │
│ Idées en jeu :       │
│ · Développer vite    │
│ · Pression sur f7    │
│                      │
│ ┌──────────────────┐ │
│ │ Mode pratique →  │ │
│ └──────────────────┘ │
│                      │
│ [💬 floating button] │
└──────────────────────┘
```

- Stacked: header → back link → title → board → ply nav → progress → tabs → tab content → secondary CTA.
- Mini-chat is a floating action button (FAB) at bottom-right that opens the chat as a full-screen sheet on mobile.
- The board fills the viewport width minus 32 px gutter.

## Components

| Component | Role | States | Primitive |
| --- | --- | --- | --- |
| `OpeningHeader` | Title, colour, ECO, version. | — | composed |
| `Chessboard` | Renders FEN + handles SAN replay. | static / animated transition | custom |
| `PlyNavigator` | Prev/next + jump-to-ply. | enabled / disabled at bounds | custom |
| `PlyProgress` | 12-dot progress strip. | not_seen / seen / studied / mastered | custom |
| `ContentTabs` | Tabs container. | — | `Tabs` (shadcn) |
| `IdeasTab` | List of `coreIdeas`. | — | composed |
| `MainLineTab` | Per-ply explanation, current-ply aware. | — | composed |
| `DeviationsTab` | Grouped by parent ply. | — | composed |
| `TrapsTab` | List of `traps`, each expandable. | — | `Collapsible` |
| `PlansTab` | List of `middlegamePlans`. | — | composed |
| `MoveExplanation` | Short + long view per ply. | short / expanded | composed |
| `MiniChat` | Position-aware chat. | closed / open / sending / error | `Sheet` or `Popover` |
| `KbdShortcutHints` | Tiny help for `← → Space`. | — | inline `Badge` |

### `MainLineTab` detail

```
Demi-coup 5 / 12
3.Fc4

On vise f7. Le coup-signature de l'Italienne.        ← short

[En savoir plus ▾]
                                                       ← when expanded:
3.Fc4 sort le fou sur sa meilleure diagonale et      
installe immédiatement la pression sur f7 — la case 
la plus faible du Noir au début de partie. C'est le 
coup-signature de la Partie italienne. Sans Fc4, on 
entre dans d'autres ouvertures.

Stratégiquement, on enchaîne les principes : on a
pris le centre, attaqué e5 en développant…

──────────────
Alternatives :
  · 3.Cc3      Jouable        On préfère développer 
                              le fou avant le 2e cavalier.
  · 3.d4       Inexact        Trop tôt, le pion en e4 
                              devient sensible.

──────────────
Idées en jeu :
  · Développer vite, roquer tôt
  · Le fou en c4 vise f7
```

When expanded, the LLM-augmented version (via `explain_main_line_move` prompt) replaces the static long text. A small attribution: *"Expliqué par l'IA, sur la base de la fiche."*

### `DeviationsTab` detail

Grouped by `afterPly`. Each deviation: opponent's move (SAN, monospace), frequency badge (`Fréquente` / `Courante` / `Occasionnelle` / `Rare`), our reply, short explanation, optional follow-up moves rendered inline.

```
Après 2.Cf3, l'adversaire peut jouer :
  ▸ Cf6  · Très fréquente · Défense russe (Petroff)
        Notre réponse : 3.Cxe5
        Le pion e5 ne peut pas être défendu sainement…
        Suite : d6, Cf3, Cxe4

  ▸ d6   · Occasionnelle · Philidor
        Notre réponse : 3.d4
        Contre la Philidor, on attaque immédiatement…
```

### `MiniChat` detail

- Header: position context (`Demi-coup 5 / 12 · 3.Fc4`).
- Conversation: assistant messages + user prompts, FR.
- Input: textarea + send button. Default placeholder: *"Pose une question sur cette position…"*.
- A "thinking" skeleton bubble while waiting for the response.
- Quick-prompt chips above the input: `Pourquoi ce coup ?` · `Et si Cd4 ?` · `Quelle pièce développer ensuite ?`.

## Sample data

The data driving this screen is exactly an `OpeningEntry`. See the example at `docs/06-pedagogy/knowledge-base-format.md` § "Partial example — Partie italienne".

## Interactions

1. **`→` / `←`** (or arrow buttons) → navigate between plies; the board animates to `fenBefore`.
2. **`Space`** → toggle the long explanation on the current ply.
3. **Click a ply dot in `PlyProgress`** → jump to that ply.
4. **Tab change** → URL hash updates (`#deviations`) for shareability and back-button support.
5. **Click a deviation** → board flashes to the deviation's resulting FEN; expansion of that deviation card.
6. **Open mini-chat** → opens as `Sheet` (mobile, full-height) or `Popover` (desktop, anchored to the panel). The chat is **position-aware**: the prompt always includes the current ply context.
7. **`Mode pratique →`** → navigates to `/openings/:slug/practice`.
8. **Click on a piece on the board** → highlights its legal moves in the current position (read-only on this screen — no actual move execution).

## Progress signalling

A ply transitions across `not_seen → seen → studied → mastered`:

- `not_seen` → `seen`: when the user reaches that ply in the navigator (debounced, 2 s on the ply).
- `seen` → `studied`: when the long explanation has been opened for ≥ 5 s, OR when the user has asked an LLM question anchored on this ply.
- `studied` → `mastered`: only set by the practice mode (Phase 3).

State is persisted via `UserProgress` rows.

## States

### Loading
- Skeleton: header lines, board square placeholder, tabs row, two skeleton paragraphs.

### Error (opening JSON failed to load)
- Full-panel inline banner: *"Cette ouverture n'a pas pu être chargée. Réessaie."* + `Réessayer`.

### Mini-chat error
- Inline error bubble: *"L'IA n'a pas pu répondre. Réessaie."* + `Réessayer` action on the bubble.

## Eval surface (subtle)

- Stockfish eval is **off by default**.
- A small `EvalBar` (vertical, alongside the board) can be revealed via a settings toggle. When on, it updates per ply.
- The eval is for *user reassurance*, not pedagogy. Numerical eval ("+0.4") shown small and muted.

## Out of scope

- Multi-board side-by-side comparison — V2.
- PGN export — V2.
- Save bookmarks on a specific ply — V2.
- Sharing a position — V2.
- Audio narration of explanations — V2+.
