# Brief Claude Design — Position libre & chat

You are designing one screen of a French-language PWA. **Read this whole brief, then output high-fidelity wireframes for desktop (1280×) and mobile (390×844). All copy in French. No emojis. shadcn/ui primitives. Use the Classique theme by default.**

---

## Product

**Chess-coach** : PWA personnelle pour apprendre les ouvertures d'échecs en français. Cible : adulte francophone, débutant ~380 Elo. Pédagogie en couches : idées directrices → ligne principale (12 demi-coups) → déviations → pièges → plans de milieu de partie.

## Personnalité

Mentor compétent, accessible. **"Tu" partout.** Pas de condescendance. **Pas d'emojis. Pas de "Bravo !"**, pas de points d'exclamation gratuits. Density aérée. **Une action principale par écran.** Lecture d'abord, action ensuite.

## Themes (3) — render in Classique by default

### Classique — warm, book-like, ivory + brown + gold + burgundy
```
--background:#FBF7EE --foreground:#3A2E1F --card:#FFFAF0
--primary:#8E2A2A --primary-foreground:#FBF7EE
--secondary:#C4A24A --muted:#EFE8D7 --muted-foreground:#75634A
--accent:#C4A24A --border:#D9CBA8 --ring:#8E2A2A
--destructive:#B23A48
--board-light:#EBD4A6 --board-dark:#B68852
```
Fonts: titles **Lora** 500/600/700, body **Source Sans Pro** 400/600, SAN **JetBrains Mono** 500.

### Moderne — off-white, near-black, deep blue accent
```
--background:#FAFAFA --foreground:#0A0A0A --card:#FFFFFF
--primary:#1E40AF --primary-foreground:#FAFAFA
--secondary:#F1F5F9 --muted:#F1F5F9 --muted-foreground:#64748B
--accent:#1E40AF --border:#E2E8F0 --ring:#1E40AF
--board-light:#F1F5F9 --board-dark:#94A3B8
```
Fonts: all UI **Geist Sans** 400–700, SAN **JetBrains Mono** 500.

### Sombre — warm dark grey, off-white text, vivid violet accent
```
--background:#1A1916 --foreground:#EDE7DC --card:#232220
--primary:#8B5CF6 --primary-foreground:#FAFAFA
--secondary:#2D2C2A --muted:#2D2C2A --muted-foreground:#A8A39B
--accent:#8B5CF6 --border:#3A3936 --ring:#8B5CF6
--board-light:#4A4540 --board-dark:#2A2622
```
Fonts: same as Moderne.

## Composants

Stack : Tailwind + shadcn/ui. Primitives : `Button`, `Card`, `Input`, `Textarea`, `ToggleGroup`, `Select`, `Badge`, `ScrollArea`, `Skeleton`, `Toast`, `Collapsible`. Icônes Lucide outline (rotate-ccw, copy, send, chevron-down).

## Spacing, formes, ombres

Échelle px : 4, 8, 12, 16, 24, 32, 48, 64. Rayons : 4 / 8 / 12 max. Ombres `shadow-sm`/`shadow-md`. Bordures 1 px.

## App shell

Header sticky 56/48 px. Back chevron `◂ Mon répertoire` sous le header.

## Notation FR

Cavalier=**C**, Fou=**F**, Tour=**T**, Dame=**D**, Roi=**R**, `O-O` / `O-O-O`. SAN en italique en prose, mono en data.

## Échiquier

Coordonnées **on**. Last-move highlight via `--last-move`. Mode interactif legal-moves par défaut ; mode "placement libre" sur toggle.

---

# Écran à concevoir : Position libre & chat

**Route** : `/free`. Bac à sable : positionner n'importe quelle position et discuter avec l'IA. **Action principale** : poser une question. La position est un moyen.

## Layout desktop (≥ 1024 px)

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
│  │      [chessboard]        │   │                              │    │
│  │       560 × 560 px       │   │  (vide — pose une question)  │    │
│  │      (interactive)       │   │                              │    │
│  │                          │   │  · Pourquoi ce coup ?        │    │
│  │                          │   │  · Quel est le plan Blanc ?  │    │
│  │                          │   │  · Et si je joue Cd4 ?       │    │
│  │                          │   │                              │    │
│  └──────────────────────────┘   │                              │    │
│                                 │                              │    │
│  [Jouer · Placer librement]    │                              │    │
│  Trait : Blancs ▾   ⟲ Reset    │                              │    │
│  FEN : [r1bqkbnr/...]  [Coller] │                              │    │
│                                 │                              │    │
│  Évaluation moteur              │                              │    │
│  +0.4 (profondeur 14)           │                              │    │
│  Trois meilleurs coups :        │                              │    │
│  · Fc4   +0.4                   │                              │    │
│  · d3    +0.3                   │                              │    │
│  · Cc3   +0.2                   │                              │    │
│                                 │ ┌──────────────────────────┐ │    │
│                                 │ │ [textarea]              ▶│ │    │
│                                 │ └──────────────────────────┘ │    │
└────────────────────────────────────────────────────────────────────┘
```

Deux colonnes : board gauche (~560 × 560 px, interactif), chat droit (~440 px, hauteur totale).

Sous le board (gauche) :
1. **Mode toggle** : `Jouer` (legal moves) / `Placer librement` (drag-drop libre depuis tray latéral).
2. **Trait** : `Select` Blancs / Noirs (utile en mode placement).
3. **Reset** : icône rotate-ccw + label.
4. **FEN field** : `Input` mono, full-width, bouton `Coller` à droite.
5. **`EngineEvalPanel`** : `Card` muted, eval globale + top-3 PV en mono.

Colonne droite : `ScrollArea` chat occupant toute la hauteur. Input chat **sticky en bas**.

## Layout mobile (< 768 px)

Empilé : header → back → titre → board → mode toggle + trait + reset → FEN field → eval panel `Collapsible` (replié par défaut, *"Évaluation moteur ▾"*) → conversation → input sticky bas viewport.

## Composants

| Nom | Rôle | Primitive |
| --- | --- | --- |
| `Chessboard` | Interactif (jouer ou placer) | custom |
| `BoardModeToggle` | Switch jouer / placer | `ToggleGroup` |
| `TurnSelector` | À qui de jouer | `Select` |
| `ResetButton` | Retour position initiale | `Button` outline |
| `FenField` | Coller / éditer FEN | `Input` + bouton |
| `EngineEvalPanel` | Top-3 PV + cp | `Card` |
| `ChatThread` | Liste de messages | `ScrollArea` + composé |
| `ChatBubble` | Un message | custom |
| `ChatComposer` | Textarea + send | composé |
| `QuickPromptChips` | 3 chips au-dessus de l'input | `Badge` |

## `EngineEvalPanel`

```
Évaluation moteur
+0.4   (profondeur 14)

Trois meilleurs coups :
  · Fc4    +0.4
  · d3     +0.3
  · Cc3    +0.2
```

`Card` discrète, ton muted. Eval globale en taille moyenne (mono). Top-3 PV : SAN mono à gauche, cp mono à droite (signe `+` ou `−`, convention positif = avantage Blancs). En cours de calcul : skeleton "—— (en cours)".

## `ChatBubble` — variantes

- **User** : aligné à droite, fond accent-tinté (`--accent` à 15 % d'opacité), `--accent-foreground`, radius 12.
- **Assistant** : aligné à gauche, `Card` (`--card`), bordure 1 px. SAN dans la prose rendu via `MoveNotation` (mono).
- **Erreur** : aligné gauche, fond `--destructive` à 15 %, bouton `Réessayer` inline.

## `QuickPromptChips`

3 chips horizontaux au-dessus du textarea. Texte dynamique selon contexte :
- *"Pourquoi ce coup est-il fort ?"*
- *"Quel est le plan pour les Blancs ?"*
- *"Et si je joue [hovered_san] ?"* (apparaît si l'utilisateur survole une pièce)

Clic sur un chip → pré-remplit le textarea, focus sur celui-ci.

## Données d'exemple

```json
{
  "fen": "r1bqkbnr/pppp1ppp/2n5/4p3/2B1P3/5N2/PPPP1PPP/RNBQK2R b KQkq - 3 3",
  "turn": "Noirs",
  "stockfish": {
    "depth": 14,
    "globalCp": 40,
    "lines": [
      { "san": "Fc4", "cp": 40 },
      { "san": "d3",  "cp": 30 },
      { "san": "Cc3", "cp": 20 }
    ]
  },
  "messages": [
    { "role": "user",      "content": "Pourquoi le fou en c4 vise f7 ?" },
    { "role": "assistant", "content": "Parce que f7 n'est défendue que par le roi en début de partie. C'est le point le plus faible de la position noire. Le fou en c4 prépare aussi les combinaisons sur cette case." }
  ],
  "quickPrompts": [
    "Pourquoi ce coup est-il fort ?",
    "Quel est le plan pour les Blancs ?",
    "Et si je joue Cd4 ?"
  ]
}
```

## Interactions

1. Coup légal sur le board : FEN se met à jour, eval recompute (debounce 400 ms), conversation préservée.
2. Coller un FEN : board snap à la position, conversation préservée.
3. `Reset` : confirmation `Dialog` *"Réinitialiser la position effacera la conversation. Continuer ?"* (uniquement si conversation non vide).
4. `Placer librement` : tray latéral de pièces apparaît, drag-drop libre.
5. Hover d'une pièce : 3e chip se met à jour avec *"Et si je joue [SAN] ?"*.
6. Envoi message : bulle assistant arrive (ou stream si activé). Chips se rafraîchissent.
7. Clic sur un SAN dans une bulle assistant : board flashe ce coup en preview (puis revient sauf confirmation).

## États

- **Loading engine** : eval skeleton, composer désactivé, placeholder *"Le moteur se charge…"*.
- **Engine error** : eval inline error *"Le moteur n'est pas disponible."* + `Réessayer`. **Chat désactivé** (pas de contexte sain).
- **LLM error** : bulle d'erreur in-place avec `Réessayer`, eval et board OK.
- **Empty chat** : centre du panel droit, texte muted *"Pose une question sur cette position. L'IA s'appuie sur l'évaluation du moteur."* + 3 chips quick-prompt.

## Hors scope (ne pas dessiner)

Sauvegarde / bookmark de positions, partage public de conversation, analyse multi-utilisateur, profondeur > 20, import PGN.

---

**Output attendu** : wireframes desktop 1280× et mobile 390×844 en thème Classique. Inclure : état empty chat (3 chips visibles), conversation avec 1 question + 1 réponse, état loading engine, état engine error. Copy 100 % FR.
