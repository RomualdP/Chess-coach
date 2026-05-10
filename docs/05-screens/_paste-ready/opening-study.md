# Brief Claude Design — Étude d'ouverture

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

Stack : Tailwind + shadcn/ui (Radix). Primitives : `Button`, `Card`, `Tabs`, `Dialog`, `Sheet`, `DropdownMenu`, `Tooltip`, `Avatar`, `Badge`, `ScrollArea`, `Skeleton`, `Toast`, `Popover`, `Collapsible`, `Progress`. Icônes Lucide outline. Custom : `Chessboard` (wraps react-chessboard, 560 px desktop / pleine largeur mobile), `PlyNavigator` (← →), `MoveNotation` (SAN mono), `MiniChat`.

## Spacing, formes, ombres

Échelle px : 4, 8, 12, 16, 24, 32, 48, 64. Rayons : 4 / 8 / 12 max. Ombres `shadow-sm`/`shadow-md` uniquement. Bordures 1 px theme-aware.

## App shell

Header sticky 56/48 px. Gauche : logo + nom (mobile : logo seul). Droite desktop : toggle thème · lien "Position libre" · avatar dropdown. Mobile : avatar dropdown seul.

## Patterns transverses

Loading = skeletons (jamais "Chargement…"). Empty = 1 phrase + bouton. Erreur inline avec `Réessayer`. Toasts top-right desktop / top-center mobile.

## Notation FR

Cavalier=**C**, Fou=**F**, Tour=**T**, Dame=**D**, Roi=**R**, `O-O` / `O-O-O`. SAN en italique en prose, mono en data.

## Échiquier

Orientation par défaut : Blancs en bas pour ouverture Blanche, Noirs en bas pour ouverture Noire. Coordonnées **on**. Last-move highlight via `--last-move`. Pas de flèches par défaut (Alt+drag pour dessiner).

---

# Écran à concevoir : Étude d'ouverture

**Route** : `/openings/:slug`. **L'écran central de l'app**, où l'utilisateur passe le plus de temps. Action principale : **avancer dans la ligne principale** (next ply).

## Layout desktop (≥ 1024 px)

```
┌────────────────────────────────────────────────────────────────────┐
│ Header                                                              │
├────────────────────────────────────────────────────────────────────┤
│  ◂ Mon répertoire                                                   │
│                                                                     │
│  Partie italienne                          Mode pratique →          │
│  Blancs · C50 · v0.1.0                                              │
│  ─────────────────────                                              │
│                                                                     │
│  ┌──────────────────────────┐   ┌──────────────────────────────┐    │
│  │                          │   │ [Idées · Ligne · Déviations  │    │
│  │      [chessboard]        │   │  · Pièges · Plans]           │    │
│  │       560 × 560 px       │   │                              │    │
│  │                          │   │  Demi-coup 5 / 12             │    │
│  │                          │   │  3.Fc4                       │    │
│  │                          │   │                              │    │
│  │                          │   │  On vise f7. Le coup-        │    │
│  │                          │   │  signature de l'Italienne.   │    │
│  │                          │   │  [En savoir plus ▾]          │    │
│  └──────────────────────────┘   │                              │    │
│  ◂ ply 4              ply 6 ▸   │  ──────                      │    │
│  ●●●●●○○○○○○○                    │  Idées en jeu :              │    │
│                                 │  · Développer vite           │    │
│                                 │  · Pression sur f7           │    │
│                                 │                              │    │
│                                 │  [Ouvrir le mini-chat]       │    │
│                                 └──────────────────────────────┘    │
└────────────────────────────────────────────────────────────────────┘
```

Deux colonnes : board gauche (~560 × 560 px), panel droit (~440 px). Au-dessus : back chevron `◂ Mon répertoire`. Titre h1 (Lora) + sous-ligne meta (mono pour ECO + version). Bouton secondaire haut-droite `Mode pratique →` (outline).

Sous le board : `PlyNavigator` (boutons ← →) + bandeau de 12 dots (`PlyProgress`).

Panel droit : tabs `Idées · Ligne · Déviations · Pièges · Plans`. **Onglet par défaut : Ligne**. Chaque onglet a son contenu propre (voir ci-dessous).

## Layout mobile (< 768 px)

Empilé vertical : header → back link → titre → board (~358 px) → ply nav → progress → tabs → contenu de l'onglet → bouton `Mode pratique →`. Le mini-chat devient un **FAB** (floating button) bas-droite qui ouvre un sheet plein écran.

## Composants principaux

| Nom | Rôle | Primitive |
| --- | --- | --- |
| `OpeningHeader` | Titre + chip couleur + ECO + version | composé |
| `Chessboard` | Position courante, animation entre plies | custom |
| `PlyNavigator` | Boutons ← → + jump-to-ply | custom + `Button` |
| `PlyProgress` | 12 dots, états `not_seen / seen / studied / mastered` | custom |
| `ContentTabs` | Tabs container | `Tabs` shadcn |
| `MoveExplanation` | Vue courte + longue dépliable | composé + `Collapsible` |
| `MiniChat` | Conversation ancrée sur le ply courant | `Sheet` mobile / `Popover` desktop |

## Contenu de l'onglet "Ligne" (par défaut)

```
Demi-coup 5 / 12
3.Fc4

[short]   On vise f7. Le coup-signature de l'Italienne.

[En savoir plus ▾]

[expanded] 3.Fc4 sort le fou sur sa meilleure diagonale et installe la
           pression sur f7 — la case la plus faible du Noir au début de
           partie. C'est le coup-signature de la Partie italienne.

──────
Alternatives :
  · 3.Cc3   Jouable     On préfère développer le fou avant le 2e cavalier.
  · 3.d4    Inexact     Trop tôt, le pion en e4 devient sensible.

──────
Idées en jeu :
  · Développer vite, roquer tôt
  · Le fou en c4 vise f7
```

Le SAN dans la prose en italique, le SAN dans la liste alternatives en mono. Verdict (`Jouable`/`Inexact`) en `Badge` discret.

## Contenu de l'onglet "Déviations"

Groupé par parent ply. Chaque déviation = carte avec : SAN adverse (mono) + `Badge` fréquence (`Très fréquente` / `Courante` / `Occasionnelle` / `Rare`) + notre réponse (mono) + 1 phrase d'explication + suite éventuelle (chips de moves).

## Mini-chat

Header : contexte position (`Demi-coup 5 / 12 · 3.Fc4`). Conversation : bulles assistant (gauche, `Card`) et user (droite, fond accent-tinté). Input bas : textarea + bouton envoi. Au-dessus de l'input : 3 chips quick-prompt FR (`Pourquoi ce coup ?` · `Et si Cd4 ?` · `Quelle pièce ensuite ?`). Bulle "thinking" skeleton pendant l'attente.

## Données d'exemple

```json
{
  "openingName": "Partie italienne",
  "colour": "Blancs",
  "eco": "C50",
  "version": "v0.1.0",
  "currentPly": 5,
  "currentSan": "Fc4",
  "explanationShort": "On vise f7. Le coup-signature de l'Italienne.",
  "explanationLong": "3.Fc4 sort le fou sur sa meilleure diagonale et installe la pression sur f7…",
  "alternatives": [
    { "san": "Cc3", "verdict": "Jouable", "comment": "On préfère développer le fou avant le 2e cavalier." },
    { "san": "d4",  "verdict": "Inexact", "comment": "Trop tôt, le pion en e4 devient sensible." }
  ],
  "ideasInPlay": ["Développer vite, roquer tôt", "Le fou en c4 vise f7"]
}
```

## Interactions

1. `→` / `←` (clavier ou boutons) : navigation entre plies, board s'anime vers `fenBefore`.
2. `Espace` : toggle `En savoir plus`.
3. Clic sur dot dans `PlyProgress` : jump direct.
4. Changement d'onglet : URL hash `#deviations` (back-button compatible).
5. Clic sur déviation : board flashe vers la position résultante + carte se déplie.
6. Bouton mini-chat : ouvre Sheet (mobile, plein écran) ou Popover (desktop, ancré).
7. `Mode pratique →` : navigation vers `/openings/:slug/practice`.

## États

- **Loading** : skeletons header + carré board + ligne tabs + 2 paragraphes.
- **Erreur** chargement ouverture : bandeau plein panel *"Cette ouverture n'a pas pu être chargée. Réessaie."* + bouton.
- **Erreur** mini-chat : bulle d'erreur in-place avec `Réessayer`.

## Hors scope (ne pas dessiner)

Comparaison multi-ouvertures, export PGN, bookmarks, partage de position, narration audio.

---

**Output attendu** : wireframes desktop 1280× et mobile 390×844 en thème Classique. Inclure : layout principal (onglet Ligne), vue déviations, mini-chat ouvert (popover desktop, sheet plein écran mobile), état loading. Copy 100 % FR.
