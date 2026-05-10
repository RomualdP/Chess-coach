# Screen — Dashboard / Répertoire

> Route: `/`. Entry point after login. The user lands here.

## Purpose

Show the user's current repertoire (openings they're studying) with a glance at progress, and give them an obvious path to add a new opening. **One primary action: ouvrir une ouverture pour étudier.** Add-an-opening is secondary.

## Reference

- Global brief: `docs/04-design-system.md`.
- Cross-screen rules: `docs/05-screens/README.md`.

## Desktop layout (≥ 1024 px)

```
┌────────────────────────────────────────────────────────────────┐
│ Header                                                          │
├────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Mon répertoire                                                 │
│  ─────────────────                                              │
│                                                                 │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌──────────┐  │
│  │            │  │            │  │            │  │    +     │  │
│  │  Italienne │  │  Caro-Kann │  │  Londres   │  │  Ajouter │  │
│  │  Blancs    │  │  Noirs     │  │  Blancs    │  │   une    │  │
│  │  ●●●●●○○○○○│  │  ●●○○○○○○○○│  │  ●○○○○○○○○○│  │ ouverture│  │
│  │  C50       │  │  B18       │  │  D02       │  │          │  │
│  └────────────┘  └────────────┘  └────────────┘  └──────────┘  │
│                                                                 │
└────────────────────────────────────────────────────────────────┘
```

- Single content column, max-width ~1120 px, centred.
- Title `Mon répertoire` (h1, theme title font).
- A 4-column grid of cards (3 openings + 1 "Ajouter" tile in this example). At more openings, wraps to multiple rows.
- Card aspect: ~280 × 200 px. Generous padding.

## Mobile layout (< 768 px)

```
┌──────────────────────┐
│ Header               │
├──────────────────────┤
│ Mon répertoire       │
│ ──────────────       │
│                      │
│ ┌──────────────────┐ │
│ │ Italienne        │ │
│ │ Blancs · C50     │ │
│ │ ●●●●●○○○○○      │ │
│ └──────────────────┘ │
│ ┌──────────────────┐ │
│ │ Caro-Kann        │ │
│ │ Noirs · B18      │ │
│ │ ●●○○○○○○○○      │ │
│ └──────────────────┘ │
│ ┌──────────────────┐ │
│ │ Londres          │ │
│ │ Blancs · D02     │ │
│ │ ●○○○○○○○○○      │ │
│ └──────────────────┘ │
│                      │
│ ┌──────────────────┐ │
│ │ + Ajouter une    │ │
│ │    ouverture     │ │
│ └──────────────────┘ │
└──────────────────────┘
```

- Cards stack vertically, full width minus 16 px gutters.
- The "Ajouter" tile is the last item, visually distinct (dashed border, secondary tone).

## Components

| Component | Role | States | Primitive |
| --- | --- | --- | --- |
| `OpeningCard` | One opening in the repertoire. | default / hover / focus / pressed | `Card` (shadcn) |
| `AddOpeningCard` | Last tile in the grid; opens the add-flow sheet. | default / hover | `Card` with dashed border |
| `ProgressDots` | 10-dot indicator showing % of plies "studied" or above. | filled / empty | custom |
| `OpeningCardHeader` | Name + colour chip + ECO. | — | composed |
| `AddOpeningSheet` | Slide-in sheet with the catalogue. | loading / loaded / error | `Sheet` (shadcn) |

### `OpeningCard` content

- Top: opening name (Lora 600 in Classique; Geist 600 in Moderne/Sombre).
- Subline: `Blancs` or `Noirs` chip + ECO code in monospace.
- Progress dots row: 10 dots representing the 12 plies bucketed (or simply % studied — designer's call).
- Bottom-right: a tiny `Repris il y a X j` ("Studied X days ago") muted-foreground.
- Hover: subtle elevation (`shadow-md`), 1px border darken.

## Sample data

```ts
const repertoire = [
  {
    slug: "italian-game",
    name: "Partie italienne",
    colour: "white",
    eco: "C50",
    studiedPlies: 5,
    totalPlies: 12,
    lastSeenAt: "2026-05-08",
  },
  {
    slug: "caro-kann",
    name: "Caro-Kann",
    colour: "black",
    eco: "B18",
    studiedPlies: 2,
    totalPlies: 12,
    lastSeenAt: "2026-05-05",
  },
  {
    slug: "london-system",
    name: "Système de Londres",
    colour: "white",
    eco: "D02",
    studiedPlies: 1,
    totalPlies: 12,
    lastSeenAt: "2026-05-09",
  },
];
```

## Interactions

1. **Click on `OpeningCard`** → navigates to `/openings/<slug>`.
2. **Click on `AddOpeningCard`** → opens `AddOpeningSheet` from the right (desktop) or bottom (mobile).
3. **Long-press / right-click on `OpeningCard`** → opens a context menu: `Archiver` (soft delete, V1.1), `Ouvrir le mode pratique` (shortcut to `/openings/<slug>/practice`).
4. **Keyboard**: `Enter` on focused card opens the opening; `Tab` / `Shift+Tab` navigates between cards.

## Add-opening flow (the `Sheet`)

```
┌──────────────────────────────┐
│  Ajouter une ouverture       │
│  ────────────────────        │
│                              │
│  Recommandées pour toi       │
│  ┌───────────────────────┐   │
│  │ Partie italienne      │   │
│  │ Blancs · C50          │   │
│  │ Active piece play …   │   │
│  └───────────────────────┘   │
│  ┌───────────────────────┐   │
│  │ Caro-Kann             │   │
│  │ Noirs · B18           │   │
│  │ Solide, pédagogique … │   │
│  └───────────────────────┘   │
│                              │
│  Tout le catalogue           │
│  [filtres: couleur ▾, eco ▾] │
│  [...autres ouvertures...]   │
│                              │
└──────────────────────────────┘
```

- "Recommandées" surfaces openings flagged `recommendedForBeginner` and not already in the user's repertoire.
- "Tout le catalogue" lists the rest, with simple filters (`Blancs` / `Noirs`, ECO range).
- An "Ajouter" button per row; on click, optimistic insert into the dashboard and the sheet stays open in case the user wants more.

> ⚠️ At MVP we don't have an LLM-powered picker (deferred to V1.1 — see `docs/06-pedagogy/llm-prompts.md`). The recommendation list is a simple sort by `recommendedForBeginner`.

## States

### Empty (new user, no repertoire)

```
┌────────────────────────────────┐
│  Mon répertoire                │
│                                │
│   (small line-art illustration)│
│                                │
│  Tu n'as pas encore d'ouverture│
│  dans ton répertoire.          │
│                                │
│  [ + Ajouter une ouverture ]   │
└────────────────────────────────┘
```

The CTA is the same primary button — but centred and prominent.

### Loading

Skeletons of `OpeningCard` (4 of them at the right grid spots).

### Error (catalogue couldn't load when adding)

Inline banner inside the `AddOpeningSheet`: *"Impossible de charger le catalogue. Réessaie."* + `Réessayer` button.

## Out of scope

- Statistics dashboards (rating progression, time spent, etc.) — V2.
- Sharing your repertoire — never.
- Repertoire import (PGN, chess.com export) — V2+.
- Multiple repertoires per user (e.g. tournament vs casual) — V2+.
