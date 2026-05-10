# 05 — Screens

This folder holds **per-screen design briefs** for the 4 MVP screens. Each brief is structured to be paste-ready into Claude Design (or any AI design tool), together with `docs/04-design-system.md` for global context.

## The 4 MVP screens

| Brief | Route | Purpose |
| --- | --- | --- |
| [`dashboard.md`](./dashboard.md) | `/` | Repertoire overview + entry to add new openings. |
| [`opening-study.md`](./opening-study.md) | `/openings/:slug` | Layered study of one opening — the core screen. |
| [`practice-mode.md`](./practice-mode.md) | `/openings/:slug/practice` | Quiz on the main line. |
| [`free-position.md`](./free-position.md) | `/free` | Free-form position + chat with the LLM. |

## Cross-screen conventions

These rules apply on every screen. Each screen brief assumes them.

### App shell

```
┌──────────────────────────────────────────────────────────┐
│ Header (sticky, 56 px desktop / 48 px mobile)             │
├──────────────────────────────────────────────────────────┤
│ Main content (per-screen)                                 │
└──────────────────────────────────────────────────────────┘
```

No sidebar, no footer. Navigation is minimal — the app has 4 screens.

### Header anatomy

| Slot | Desktop | Mobile |
| --- | --- | --- |
| Left | Logo + product name "Chess-coach" | Logo only |
| Centre | (empty) | (empty) |
| Right | Theme toggle (icon) · Free position link · Avatar dropdown | Avatar dropdown only (Free position lives in the dropdown menu on mobile) |

The avatar dropdown contains: theme picker (3 options), piece set picker, log out.

### Routes & navigation

- The dashboard is the home and the back-stop. Logo always returns there.
- Each opening study screen has a **back chevron** in the top-left of the main content (under the header).
- Mobile back is a swipe-from-left gesture (browser-default) plus the explicit chevron.

### Loading

- **Skeleton screens**, not spinners. Each component renders a skeleton variant matching its shape.
- Never use the word `Chargement…` as visible text.

### Empty states

- A short statement (1 sentence) + the primary next-action button.
- Visual: small contextual illustration (line-art, monochrome, accent-coloured) — optional, designer's call.

### Error states

- Inline, where the error happened. Not a full-page error unless the whole shell can't load.
- Always offer one action: *Réessayer*.
- Toasts for non-blocking errors (failed sync, etc.). Errors that block the user's task get an inline banner.

### Toasts

- Position: top-right (desktop), top-centre (mobile).
- Lifetime: 4 seconds for success, 6 seconds for error.
- Single line of copy, optional action. No icons (the colour does the work).

### Mobile breakpoint

- `< 768 px`: single-column stacked layout, board ~92 % of viewport width.
- `768 – 1023 px`: tablet — most screens still stack (board first).
- `≥ 1024 px`: desktop layouts as specified per screen.

### Chessboard rules

- Default orientation: White at the bottom for White repertoire entries; Black at the bottom for Black entries.
- Click-to-select then click-to-place is supported (in addition to drag).
- Last-move highlight uses `--last-move`. Legal-move dots use `--legal-move`. No arrows by default; arrows can be drawn with `Alt + click-drag` (parity with lichess).
- Coordinates **on by default** at MVP (V2 makes them togglable).

### Keyboard shortcuts (desktop)

- `←` / `→` — previous / next ply (on Opening study and Practice mode).
- `Space` — toggle the long explanation on a ply (Opening study).
- `?` — open the keyboard-shortcuts help dialog.
- `T` — cycle themes.
- `Esc` — close the topmost overlay (dialog, sheet, popover).

### Sample-copy rules for designers

When generating sample data, use these realistic strings rather than lorem ipsum:

- Opening names: "Partie italienne", "Caro-Kann", "Système de Londres".
- ECO codes: `C50`, `B18`, `D02`.
- Moves (SAN): `1.e4 e5 2.Cf3 Cc6 3.Fc4 Fc5 4.d3` (note: French uses `C` for Cavalier, `F` for Fou, `T` for Tour, `D` for Dame, `R` for Roi). Castle: `O-O` (kingside), `O-O-O` (queenside).
- Status labels: *non vue* / *vue* / *étudiée* / *maîtrisée*.
- Tone of explanations: see `docs/04-design-system.md` § French copy guidelines.

## How to feed this to Claude Design

Recommended prompt skeleton:

```
You are designing a screen for Chess-coach, a French-language chess coaching PWA.

Product context, audience, personality, and theme tokens:
[paste docs/04-design-system.md]

Cross-screen conventions:
[paste this README's "Cross-screen conventions" section]

Screen to design:
[paste docs/05-screens/<screen>.md]

Output: high-fidelity wireframes for desktop (1280×) and mobile (390×844), in the
{Classique | Moderne | Sombre} theme. Include all listed states. Use shadcn/ui
primitives. French copy throughout. No emojis.
```

If Claude Design supports tokenised palettes, paste the relevant theme's CSS variable block. Otherwise the named theme (Classique / Moderne / Sombre) plus the personality description from `04-design-system.md` should suffice.
