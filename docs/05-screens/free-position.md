# Screen — Position libre & chat

> Route: `/free`. Sandbox to set up any position and ask the LLM about it.

## Purpose

Set up an arbitrary chess position — by playing from start, pasting a FEN, or placing pieces freely — and have a conversation with the LLM about it. The conversation is **engine-bounded**: every claim the LLM makes refers to the top-3 Stockfish lines provided as context.

**One primary action**: poser une question. Position setup is a means.

## Reference

- Global brief: `docs/04-design-system.md`.
- Cross-screen rules: `docs/05-screens/README.md`.
- LLM behaviour: `docs/06-pedagogy/llm-prompts.md` § "free_position_chat".

## Desktop layout (≥ 1024 px)

```
┌────────────────────────────────────────────────────────────────────┐
│ Header                                                              │
├────────────────────────────────────────────────────────────────────┤
│  ◂ Mon répertoire                                                   │
│                                                                     │
│  Position libre                                                     │
│  ─────────────                                                      │
│                                                                     │
│  ┌──────────────────────────┐   ┌──────────────────────────────┐    │
│  │                          │   │  Conversation                 │    │
│  │                          │   │  (vide — pose une question)  │    │
│  │      [chessboard]        │   │                              │    │
│  │       560 × 560 px       │   │                              │    │
│  │                          │   │                              │    │
│  │      (interactive +      │   │                              │    │
│  │       free placement)    │   │                              │    │
│  │                          │   │                              │    │
│  └──────────────────────────┘   │                              │    │
│                                 │                              │    │
│  Trait : Blancs ▾  · ⟲ Reset    │                              │    │
│  FEN: [r1bqkbnr/...] [Coller]    │                              │    │
│                                 │                              │    │
│  Évaluation moteur              │                              │    │
│  +0.4 (profondeur 14)           │                              │    │
│  Trois meilleurs coups :        │                              │    │
│  · Fc4   +0.4                   │                              │    │
│  · d3    +0.3                   │                              │    │
│  · Cc3   +0.2                   │                              │    │
│                                 │  ┌──────────────────────────┐│    │
│                                 │  │ [textarea]              ▶│    │
│                                 │  └──────────────────────────┘│    │
│                                 └──────────────────────────────┘    │
└────────────────────────────────────────────────────────────────────┘
```

- Two columns. Board left (560 px). Chat right (~440 px), full vertical extent.
- Below the board: position controls (turn selector, reset, FEN paste field) and the engine evaluation panel.
- Engine eval shows the top-3 PV with cp values. The LLM uses these as ground truth.
- The chat input is anchored at the bottom of the right column, with a send button. Press `Enter` to send, `Shift+Enter` for newline.

## Mobile layout (< 768 px)

```
┌──────────────────────┐
│ Header               │
├──────────────────────┤
│ ◂ Retour              │
│ Position libre        │
│                      │
│ ┌──────────────────┐ │
│ │   [chessboard]   │ │
│ └──────────────────┘ │
│ Trait : Blancs ▾  ⟲  │
│ [FEN…] [Coller]      │
│                      │
│ ▾ Évaluation moteur  │
│   (panel collapse)   │
│                      │
│ ─── Conversation ─── │
│ (messages)           │
│                      │
│ ┌──────────────────┐ │
│ │ [textarea]      ▶│ │
│ └──────────────────┘ │
└──────────────────────┘
```

- The engine eval panel is collapsed by default on mobile (one tap to expand).
- The chat input is sticky at the bottom of the viewport.

## Components

| Component | Role | States | Primitive |
| --- | --- | --- | --- |
| `Chessboard` | Interactive: play moves OR enter free-placement mode. | playing / placing / dragging | custom |
| `BoardModeToggle` | Switch between "Jouer des coups" and "Placer librement". | — | `ToggleGroup` (shadcn) |
| `TurnSelector` | Choose whose move it is when in free-placement mode. | white / black | `Select` |
| `ResetButton` | Reset to initial position. | enabled / disabled when already initial | `Button` (outline) |
| `FenField` | Paste / edit a FEN. | valid / invalid (red ring + tooltip) | `Input` |
| `EngineEvalPanel` | Top-3 PV with cp scores. | computing / ready / error | `Card` |
| `ChatThread` | Scrollable message list. | empty / messages / sending | composed |
| `ChatBubble` | One message (user or assistant). | user / assistant / error | custom |
| `ChatComposer` | Textarea + send button. | idle / sending / error | composed |
| `QuickPromptChips` | Suggested questions above the composer. | — | `Badge` |

### Position-setup details

- Default mode: **Jouer des coups** (legal moves only, starting from initial position or last paste).
- **Placer librement** mode lets the user grab any piece from a side-tray and drop it on any square. Used for setting up positions that didn't arise from the start (e.g. a problem from a book).
- The FEN field is **bidirectional**: paste a FEN to set the position; copy the field to share the current position.
- Invalid FEN → red ring on the field + an inline tooltip: *"FEN invalide."*. The board stays unchanged.

### `EngineEvalPanel` content

```
Évaluation moteur
+0.4 (profondeur 14)

Trois meilleurs coups :
  · Fc4    +0.4
  · d3     +0.3
  · Cc3    +0.2
```

- Eval is recomputed **on every position change** (debounced 400 ms).
- Numbers are small, monospace.
- Sign convention: positive = good for White (chess.com style).
- Whilst computing: skeleton "—— (en cours)".

### `ChatBubble`

- User: right-aligned, accent-tinted background.
- Assistant: left-aligned, card surface with 1 px border. SAN inside the prose is rendered with `<MoveNotation />` (monospace).
- Error: left-aligned, destructive-tinted, with a `Réessayer` action.

### `QuickPromptChips`

Three context-aware chips, all in French:

- *"Pourquoi ce coup est-il fort ?"*
- *"Quel est le plan pour les Blancs ?"*
- *"Et si je joue [hovered_san] ?"*

Clicking a chip pre-fills the composer; the user can edit before sending.

## Sample data

```ts
const session = {
  fen: "r1bqkbnr/pppp1ppp/2n5/4p3/2B1P3/5N2/PPPP1PPP/RNBQK2R b KQkq - 3 3",
  stockfish: {
    depth: 14,
    lines: [
      { pv: ["Fc5", "d3", "d6"], cp: 40 },
      { pv: ["Cf6", "d3", "d6"], cp: 25 },
      { pv: ["Fe7", "Cc3", "Cf6"], cp: 18 },
    ],
  },
  history: [
    { role: "user", content: "Pourquoi le fou en c4 vise f7 ?", createdAt: "..." },
    { role: "assistant", content: "Parce que f7 n'est défendue que par le roi...", createdAt: "..." },
  ],
};
```

## Interactions

1. **Make a legal move** on the board → the FEN updates, eval recomputes, the chat keeps its history.
2. **Paste a FEN** → the board snaps to it. Any in-progress chat thread is preserved.
3. **Reset** → returns to the initial position. Confirmation dialog if there's an active conversation: *"Réinitialiser la position effacera la conversation. Continuer ?"*.
4. **Free-placement mode** → switch via `BoardModeToggle`; opens a side-tray of pieces.
5. **Send a message** → assistant streams (or arrives, if non-streaming at MVP) a response. Quick-prompt chips refresh based on the current position.
6. **Hover a piece** on the board → chip *"Et si je joue [san] ?"* appears with that move pre-filled.
7. **Click an SAN inside an assistant message** → the board flashes that move (preview, then reverts unless the user confirms).

## States

### Loading (engine starting)
- Eval panel: skeleton.
- Composer: disabled, placeholder *"Le moteur se charge…"*.

### Engine error
- Eval panel: inline error *"Le moteur n'est pas disponible."* + `Réessayer`.
- The chat is **disabled** in this state — without engine context, the LLM has no ground truth, and we don't want hallucinations.

### LLM error
- Error bubble in the chat with `Réessayer`. Eval and board remain functional.

### Empty chat
- Centred placeholder: *"Pose une question sur cette position. L'IA s'appuie sur l'évaluation du moteur."* + the 3 quick-prompt chips.

## Conversation persistence

- Each session is one `LlmConversation` row.
- A new conversation is created when the user changes position **and** clicks `Réinitialiser` or comes from a fresh navigation.
- Otherwise, conversations persist across moves on the board (a single thread, anchored on changing positions).
- The conversation can be cleared at any time via a `Vider la conversation` action (in the chat header, V1.1).

## Out of scope

- Saving / bookmarking positions to revisit — V2.
- Sharing a conversation publicly — V2.
- Multi-user collaborative analysis — never.
- Engine analysis at deep depth (> 20) — V2.
- Importing PGN games and stepping through them — V3.
