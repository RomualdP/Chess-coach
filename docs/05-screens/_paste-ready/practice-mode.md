# Brief Claude Design — Mode pratique

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

Stack : Tailwind + shadcn/ui. Primitives : `Button`, `Card`, `Tabs`, `Dialog`, `Sheet`, `Badge`, `Skeleton`, `Toast`, `Progress`. Icônes Lucide outline (check, x, lightbulb, half-circle).

## Spacing, formes, ombres

Échelle px : 4, 8, 12, 16, 24, 32, 48, 64. Rayons : 4 / 8 / 12 max. Ombres `shadow-sm`/`shadow-md`. Bordures 1 px.

## App shell

Header sticky 56/48 px. Back chevron `◂ Partie italienne` sous le header.

## Notation FR

Cavalier=**C**, Fou=**F**, Tour=**T**, Dame=**D**, Roi=**R**, `O-O` / `O-O-O`. SAN en italique en prose, mono en data.

---

# Écran à concevoir : Mode pratique

**Route** : `/openings/:slug/practice`. **Action principale** : jouer le bon coup. Layout proche de l'écran d'étude pour la mémoire musculaire.

## Layout desktop (≥ 1024 px)

```
┌────────────────────────────────────────────────────────────────────┐
│ Header                                                              │
├────────────────────────────────────────────────────────────────────┤
│  ◂ Partie italienne                                                 │
│                                                                     │
│  Mode pratique                                Score : 4 / 5        │
│  Demi-coup 5 / 12                                                   │
│  ─────────────────────                                              │
│                                                                     │
│  ┌──────────────────────────┐   ┌──────────────────────────────┐    │
│  │                          │   │  À toi de jouer (Blancs)      │    │
│  │      [chessboard]        │   │                              │    │
│  │       560 × 560 px       │   │  Joue le coup-signature de    │    │
│  │      (interactive)       │   │  cette ouverture.             │    │
│  │                          │   │                              │    │
│  │                          │   │  ─── Feedback ───            │    │
│  │                          │   │                              │    │
│  │                          │   │  ✓ Cc6 : exact. Le Noir       │    │
│  │                          │   │    défend e5 en développant. │    │
│  │                          │   │                              │    │
│  │                          │   │  ◐ Cf3 : jouable, mais on    │    │
│  │                          │   │    préfère 2.Cf3 d'abord.    │    │
│  │                          │   │                              │    │
│  │                          │   │  ✗ Dh5 : trop tôt — la dame  │    │
│  │                          │   │    se fait chasser.          │    │
│  │                          │   │                              │    │
│  └──────────────────────────┘   │  [Indice (1/3)] [Abandonner] │    │
│  ◂ ply 4              ply 6 ▸   └──────────────────────────────┘    │
│  ●●●●●○○○○○○○                                                       │
└────────────────────────────────────────────────────────────────────┘
```

Deux colonnes : board gauche (~560 × 560 px, **interactif**), panel droit (~440 px). Haut-droite : `Score : 4 / 5` en `Badge` accent. `PlyNavigator` sous le board (forward-only sur les plies déjà complétés).

Panel droit, du haut en bas :
1. **Prompt** : `À toi de jouer (Blancs)` en h2 (Lora) + 1 phrase d'instruction.
2. **Stream de feedback bubbles** : la plus récente en haut, scrollable.
3. **Boutons d'action** en bas : `Indice (n/3)` + `Abandonner` (outline).

## Layout mobile (< 768 px)

Empilé : header → back link → titre + score → board (sticky pendant le scroll) → ply progress → prompt → feedback stream inline scrollable → boutons d'action.

## Composants

| Nom | Rôle | Variantes |
| --- | --- | --- |
| `Chessboard` | Interactif, accepte les coups user | idle / drag / dropped |
| `PlyPrompt` | Tâche courante | — |
| `FeedbackBubble` | Message de feedback unique | `success` / `playable` / `mistake` / `hint` |
| `ScoreBadge` | Score live | — |
| `HintButton` | Demande indice (max 3) | enabled / disabled |
| `GiveUpButton` | Révèle la réponse | — |
| `SessionSummary` | Carte fin de session | — |

## `FeedbackBubble` — variantes visuelles

| Variante | Icône Lucide | Couleur | Trigger |
| --- | --- | --- | --- |
| `success` | `check` | `--accent` (or en Classique, bleu en Moderne, violet en Sombre) | coup correct |
| `playable` | `circle-half` ou `equal` | `--muted-foreground` | alternative jouable acceptée |
| `mistake` | `x` | `--destructive` | coup mauvais (KB ou eval) |
| `hint` | `lightbulb` | `--secondary` | indice demandé |

Format d'une bubble : icône (16 px) + SAN (mono) + 1 phrase d'explication. Bordure 1 px theme-aware, padding 12 px, radius 8 px. Empilées avec gap 8 px.

## `SessionSummary` (fin de session)

```
Session terminée.

Partie italienne · 12 demi-coups
Score : 9 / 12

Coups maîtrisés (3) : 1.e4, 2.Cf3, 3.Fc4
À retravailler (3) : ply 5, ply 7, ply 11

[ Recommencer ]   [ Retour à l'ouverture ]
```

`Card`, padding 32 px, deux boutons en bas (primary `Recommencer`, outline `Retour`).

## Données d'exemple

```json
{
  "openingName": "Partie italienne",
  "currentPly": 5,
  "totalPlies": 12,
  "side": "Blancs",
  "score": 4,
  "scoreOf": 5,
  "hintsUsed": 1,
  "hintsMax": 3,
  "prompt": "Joue le coup-signature de cette ouverture.",
  "feedback": [
    { "kind": "success",  "san": "Cc6", "msg": "Exact. Le Noir défend e5 en développant." },
    { "kind": "playable", "san": "Cf3", "msg": "Jouable, mais on préfère 2.Cf3 d'abord." },
    { "kind": "mistake",  "san": "Dh5", "msg": "Trop tôt — la dame se fait chasser." }
  ]
}
```

## Interactions

1. **Drop d'une pièce** sur le board : soumet le coup.
2. **Coup correct** : bubble success, ply auto-avance après 1.2 s.
3. **Alternative jouable** : bubble playable + choix `Continuer` ou `Réessayer`.
4. **Erreur 1ère tentative** : bubble hint (principe pointé, sans révéler).
5. **Erreur 2e** : bubble hint plus précise (case ou pièce).
6. **Erreur 3e** : bubble mistake qui révèle le coup + 1 phrase, puis avance.
7. `Indice` : ajoute une bubble hint (consomme 1/3).
8. `Abandonner` : révèle + avance, sans coût.
9. Clavier `←` : revisiter ply précédent (read-only, score figé).
10. Fin de ply 12 : `SessionSummary` remplace le panel droit.

## États

- **Loading** : skeleton board + skeleton bubbles (3).
- **Engine offline** (Stockfish failed) : bandeau au-dessus du board *"Le moteur n'est pas chargé. Le mode pratique fonctionne sans validation tactique."* + `Réessayer`.
- **LLM offline** : feedback bubbles utilisent un fallback templaté ; footnote muted *"Explications complètes indisponibles."*

## Hors scope (ne pas dessiner)

Spaced-repetition, mode mixte multi-ouvertures, pratique sur déviations, mode strict.

---

**Output attendu** : wireframes desktop 1280× et mobile 390×844 en thème Classique. Inclure : layout principal en cours de session, état avec 2 ou 3 feedback bubbles différentes (success + playable + mistake), `SessionSummary`, état engine offline. Copy 100 % FR.
