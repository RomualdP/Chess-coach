# 04 — Design system

> **Status**: stub. Principles set; full token tables and component specs to be added.

## Principle

Common structure, variable themes. Visual hierarchy, screen layout, interaction patterns, proportional spacing — fixed across themes. Colours, fonts, board/piece styles — user-switchable.

## Information density

Airy: generous spacing, clean visual hierarchy, one primary action per screen, progressive disclosure (summary by default, details on click).

## Themes (3 at MVP)

### Classic (default) — "studying like a book"
- Warm ivory background, dark brown text, gold + burgundy accents
- Light/dark wood board
- Titles: Lora (warm serif)
- Body: Source Sans Pro or Public Sans (TBD)
- Feel: take your time, study seriously

### Modern — "tech productivity tool"
- Off-white background, black text, deep blue accent
- Neutral, minimal board
- Type: Inter or Geist (TBD)
- Feel: focus, efficiency

### Dark — "evening session"
- Warm dark grey, off-white text, vivid blue or purple accent
- Dark-adapted board
- Type: Geist or Inter (TBD)
- Feel: calm, end-of-day, no visual aggression

## Tokens (architecture)

CSS variables + `data-theme="classic|modern|dark"` data-attribute switching, aligned with shadcn/ui conventions.

## User customisation

**At MVP**:
- Theme selector (3 options)
- Piece set (Cburnett default, 2–3 alternates)
- Quick light/dark toggle in header

**V2**:
- Custom board colours
- Sounds & animations on/off
- Coordinates on/off
- Additional themes (neon, vintage, community)

## Detail rules

- Border radii: 4 / 8 / 12 px — neither iOS-soft nor brutalist
- Shadows: subtle, no floating-card effect
- Spacing scale (px): 4, 8, 12, 16, 24, 32, 48, 64
- Serif fonts: Classic theme only
- Move notation: monospace (JetBrains Mono)

## Open questions

- [ ] Final hex palettes for all 3 themes (only Classic roughly sketched)
- [ ] Inter vs Geist for Modern and Dark
- [ ] Source Sans Pro vs Public Sans for Classic body

## Source

For now, see `README.md` §7.
