# 04 — Design system & product brief

> **Status**: v0 design brief — ready to feed into Claude Design (or any AI design tool). Palette hex values and font choices are starting proposals; user validates them before implementation.

## How to use this doc with a design tool

When generating wireframes / mockups, paste these sections in order:

1. **Product context** (below)
2. **Audience** + **Personality**
3. **Information density** + **Spacing rules**
4. The **theme** the user wants the mockup in (Classique by default)
5. The relevant **per-screen brief** from `docs/05-screens/<screen>.md`
6. **French copy guidelines** (so generated copy matches the tone)

## Product context

Chess-coach is a personal-first PWA to learn chess openings in French. The user is a 380 Elo beginner who wants to **understand the why of chess moves**, not memorise lines. The app combines a hand-written knowledge base, a chess engine (Stockfish WASM) for verification, and an LLM (Mistral) for adaptive explanations.

Pedagogy is layered: core ideas → main line → opponent deviations → traps → middlegame plans. Every opening is presented as a **12-ply main line** plus contextual material.

## Audience

- Single primary user: a French-speaking adult, beginner (~380 Elo), playing 10-min Rapid online.
- Studies in 5–20 minute sessions (commute, lunch break, evening).
- Reads on desktop **and** mobile, often on mobile.
- Curious, motivated, but not ready to memorise dense theory.

## Personality

Mentor, expert, accessible. Speaks "tu" (personal app, not corporate). **No condescension, no infantilisation, no emojis, no exclamation-mark cheerleading.** A coach, not a coach-teacher-from-a-cartoon.

## Information density

- **Airy.** Generous whitespace. One primary action per screen.
- **Progressive disclosure.** Default = summary. Details revealed on click.
- **Read first, interact second.** Reading paths must be obvious before any action is needed.

## The 3 themes

The app ships with three themes, switchable via the header. They share **structure**; only colours, fonts, and board styles change.

### Theme: Classique (default) — "studying like a book"

Warm ivory, dark brown, gold + burgundy accents. Light/dark wood board. Serif title. Encourages slow, focused study.

```
--background:        #FBF7EE   /* warm ivory */
--foreground:        #3A2E1F   /* dark brown */
--card:              #FFFAF0
--card-foreground:   #3A2E1F
--primary:           #8E2A2A   /* burgundy — primary action */
--primary-foreground:#FBF7EE
--secondary:         #C4A24A   /* muted gold */
--secondary-foreground:#3A2E1F
--muted:             #EFE8D7
--muted-foreground:  #75634A
--accent:            #C4A24A   /* gold highlights */
--accent-foreground: #3A2E1F
--border:            #D9CBA8
--input:             #D9CBA8
--ring:              #8E2A2A   /* focus ring */
--destructive:       #B23A48
--destructive-foreground:#FBF7EE
--board-light:       #EBD4A6   /* light wood */
--board-dark:        #B68852   /* dark wood */
--last-move:         rgba(196,162,74,0.45)
--legal-move:        rgba(58,46,31,0.18)
```

Fonts:
- Titles: **Lora** (warm serif), weights 500/600/700.
- Body: **Source Sans Pro**, weights 400/600.
- Move notation: **JetBrains Mono**, weight 500.

### Theme: Moderne — "tech productivity tool"

Off-white, black, deep blue. Neutral minimal board. Sans-serif throughout. Encourages focused, efficient sessions.

```
--background:        #FAFAFA
--foreground:        #0A0A0A
--card:              #FFFFFF
--card-foreground:   #0A0A0A
--primary:           #1E40AF   /* deep blue */
--primary-foreground:#FAFAFA
--secondary:         #F1F5F9
--secondary-foreground:#0A0A0A
--muted:             #F1F5F9
--muted-foreground:  #64748B
--accent:            #1E40AF
--accent-foreground: #FAFAFA
--border:            #E2E8F0
--input:             #E2E8F0
--ring:              #1E40AF
--destructive:       #DC2626
--destructive-foreground:#FAFAFA
--board-light:       #F1F5F9
--board-dark:        #94A3B8
--last-move:         rgba(30,64,175,0.30)
--legal-move:        rgba(15,23,42,0.20)
```

Fonts:
- All UI: **Geist Sans**, weights 400/500/600/700.
- Move notation: **JetBrains Mono**, weight 500.

### Theme: Sombre — "evening session"

Warm dark grey, off-white text, vivid violet accent. Dark-adapted board. Calm, no aggression, suitable for end-of-day use.

```
--background:        #1A1916   /* warm dark grey */
--foreground:        #EDE7DC   /* off-white warm */
--card:              #232220
--card-foreground:   #EDE7DC
--primary:           #8B5CF6   /* vivid violet */
--primary-foreground:#FAFAFA
--secondary:         #2D2C2A
--secondary-foreground:#EDE7DC
--muted:             #2D2C2A
--muted-foreground:  #A8A39B
--accent:            #8B5CF6
--accent-foreground: #FAFAFA
--border:            #3A3936
--input:             #3A3936
--ring:              #8B5CF6
--destructive:       #EF4444
--destructive-foreground:#FAFAFA
--board-light:       #4A4540
--board-dark:        #2A2622
--last-move:         rgba(139,92,246,0.40)
--legal-move:        rgba(237,231,220,0.18)
```

Fonts:
- All UI: **Geist Sans**, weights 400/500/600/700.
- Move notation: **JetBrains Mono**, weight 500.

## Token-architecture rules

- All themes expose the **same set of tokens** (above).
- Themes are switched via `data-theme="classic|modern|dark"` on `<html>`. Aligned with shadcn/ui conventions.
- Components must read tokens, never hardcoded colours.
- Dark/light system preference detection: respected on first visit; once the user chooses, their pick wins.

## Customisation at MVP

- **Theme picker** in header (3 options).
- **Piece set** (Cburnett default + 2–3 alternates), in a Settings drawer.
- **Quick light/dark toggle** in header (cycles Classique → Sombre and back).

V2 adds: custom board colours, sound on/off, coordinates on/off, more themes.

## Component library

- **Stack**: Tailwind CSS + shadcn/ui (Radix primitives under the hood).
- **Primitives expected**: `Button`, `Card`, `Tabs`, `Dialog`, `Sheet`, `DropdownMenu`, `Tooltip`, `Avatar`, `Badge`, `ScrollArea`, `Separator`, `Skeleton`, `Toast`, `Popover`, `Collapsible`, `Progress`.
- **Custom components**:
  - `<Chessboard />` — wraps `react-chessboard`. Accepts `fen`, `orientation`, `lastMove`, `onMove`, `arrows`.
  - `<PlyNavigator />` — pagination over a 12-ply main line, current-state aware (not_seen / seen / studied / mastered).
  - `<MoveNotation />` — renders SAN in monospace, with a `verdict` chip if applicable.
  - `<EvalBar />` *(opt-in)* — Stockfish evaluation surfaced subtly. Off by default at MVP.
  - `<MiniChat />` — anchored conversation surface, position-aware.

## Spacing, radius, shadows

- **Spacing scale (px)**: 4, 8, 12, 16, 24, 32, 48, 64. Compose with Tailwind's default `space-*` scale.
- **Radius**: 4 px (tight UI: chips, badges), 8 px (buttons, inputs), 12 px (cards). Never > 12 px (no iOS-soft balloons).
- **Shadows**: subtle. `shadow-sm` for raised cards, `shadow-md` for floating menus. **No `shadow-lg`/`shadow-xl`** — we don't levitate.
- **Borders**: 1 px, theme-aware via `--border`. Avoid double borders (use one of border-or-shadow, not both).

## Typography rules

- Base body size: 16 px (1 rem).
- Line-height: 1.5 for body, 1.25 for headings.
- Move notation: monospace, slightly tighter line-height (1.2).
- Headings: only one `<h1>` per screen. Hierarchy is visual *first*, semantic *second*.

## Iconography

- **Lucide React** (the shadcn/ui default). Stroke 1.5, size 16/20/24 px.
- Avoid filled icons (too heavy for our tone). Outline only.

## French copy guidelines

- **Tone**: coach-mentor. Direct. Calm. No exclamation marks unless genuinely warranted.
- **Form**: "tu" everywhere. The app speaks personally.
- **Forbidden**: "Bravo !", "Excellent !", "Parfait !", "Oups !", emojis (anywhere), question-mark stacking ("?!").
- **Microcopy patterns**:
  - Confirms: `Confirmer` (primary), `Annuler` (secondary).
  - Empty states: declarative, with the next action. *"Tu n'as pas encore d'ouverture dans ton répertoire. Ajoute-en une pour commencer."*
  - Errors: factual, actionable. *"Connexion perdue. Réessaie dans un instant."*
  - Loading: avoid "Chargement…". Prefer skeletons.
- **Move notation in prose**: italicise, not bold. *"Après 3.Fc4..."*. SAN is rendered in `<MoveNotation />`, not free text, when it appears as data.

## Accessibility baselines

- Contrast: AA minimum (4.5:1 for body text). Verify each theme.
- Focus rings: always visible (`--ring`).
- Keyboard: every interactive element reachable, with logical tab order.
- Screen-reader labels on the chessboard: `aria-label` on each square (e.g. `"e4, pion blanc"`).
- The chessboard is operable via keyboard (arrows + enter), not only drag-and-drop.

## What this design system intentionally omits

- Marketing pages, landing pages — the app starts on the dashboard after login.
- Settings galleries, account flows beyond login/logout — minimal at MVP.
- Animation system — animations should be reactive and minimal (no orchestrated entrance choreography).

## Source

Originally outlined in `README.md` §7. Hex values, fonts and component lists added in v0 here.
