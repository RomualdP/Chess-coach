# Brief Claude Design — Dashboard / Répertoire

You are designing one screen of a French-language PWA. **Read this whole brief, then output high-fidelity wireframes for desktop (1280×) and mobile (390×844). All copy in French. No emojis. shadcn/ui primitives. Use the Classique theme by default.**

---

## Product

**Chess-coach** : PWA personnelle pour apprendre les ouvertures d'échecs en français. Cible : adulte francophone, débutant ~380 Elo. Pédagogie en couches : idées directrices → ligne principale (12 demi-coups) → déviations → pièges → plans de milieu de partie.

## Personnalité

Mentor compétent, accessible. **"Tu" partout.** Pas de condescendance. **Pas d'emojis. Pas de "Bravo !"**, pas de "Excellent !", pas de points d'exclamation gratuits. Density aérée. **Une action principale par écran.** Lecture d'abord, action ensuite.

## Themes (3) — render the Classique mockup by default; you may show the others as variants

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
--destructive:#DC2626
--board-light:#F1F5F9 --board-dark:#94A3B8
```
Fonts: all UI **Geist Sans** 400/500/600/700, SAN **JetBrains Mono** 500.

### Sombre — warm dark grey, off-white text, vivid violet accent
```
--background:#1A1916 --foreground:#EDE7DC --card:#232220
--primary:#8B5CF6 --primary-foreground:#FAFAFA
--secondary:#2D2C2A --muted:#2D2C2A --muted-foreground:#A8A39B
--accent:#8B5CF6 --border:#3A3936 --ring:#8B5CF6
--destructive:#EF4444
--board-light:#4A4540 --board-dark:#2A2622
```
Fonts: same as Moderne.

## Composants

Stack : Tailwind + shadcn/ui (Radix). Primitives attendues : `Button`, `Card`, `Tabs`, `Dialog`, `Sheet`, `DropdownMenu`, `Tooltip`, `Avatar`, `Badge`, `ScrollArea`, `Skeleton`, `Toast`, `Popover`, `Collapsible`, `Progress`. Icônes : Lucide outline uniquement, stroke 1.5.

## Spacing, formes, ombres

Échelle px : 4, 8, 12, 16, 24, 32, 48, 64. Rayons : 4 (chips), 8 (boutons/inputs), 12 (cards). **Jamais > 12 px.** Ombres : `shadow-sm` ou `shadow-md` uniquement, jamais lg/xl. Bordures 1 px theme-aware.

## App shell (commun à tous les écrans)

Header sticky 56 px desktop / 48 px mobile :
- **Gauche** : logo + nom "Chess-coach" (mobile : logo seul).
- **Droite desktop** : icône toggle thème · lien "Position libre" · avatar dropdown.
- **Droite mobile** : avatar dropdown uniquement (le lien Position libre vit dedans).

Avatar dropdown : sélecteur de thème (3 options), sélecteur de jeu de pièces, déconnexion. Pas de sidebar, pas de footer.

## Patterns transverses

- **Loading** : skeleton screens, jamais de spinner ni le mot "Chargement…".
- **Empty** : 1 phrase + bouton de l'action suivante.
- **Erreur inline** avec action "Réessayer". Toasts pour non-bloquant (top-right desktop, top-center mobile, 4 s succès / 6 s erreur).
- **Mobile < 768 px** : colonne unique empilée, gouttière 16 px.

## Notation FR

Pièces : Cavalier=**C**, Fou=**F**, Tour=**T**, Dame=**D**, Roi=**R**. Roque : `O-O` / `O-O-O`. Dans la prose : SAN en italique. Dans les composants data : SAN en monospace (`<MoveNotation />`).

---

# Écran à concevoir : Dashboard / Répertoire

**Route** : `/`. Atterrissage après login. Action principale : **ouvrir une ouverture pour étudier**. Action secondaire : **ajouter une nouvelle ouverture**.

## Layout desktop (≥ 1024 px)

```
┌────────────────────────────────────────────────────────────────┐
│ Header                                                          │
├────────────────────────────────────────────────────────────────┤
│  Mon répertoire                                                 │
│  ─────────────────                                              │
│                                                                 │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌──────────┐  │
│  │ Italienne  │  │ Caro-Kann  │  │ Londres    │  │    +     │  │
│  │ Blancs·C50 │  │ Noirs·B18  │  │ Blancs·D02 │  │ Ajouter  │  │
│  │ ●●●●●○○○○○ │  │ ●●○○○○○○○○ │  │ ●○○○○○○○○○ │  │   une    │  │
│  │ 5/12 vus  │  │ 2/12 vus   │  │ 1/12 vu    │  │ouverture │  │
│  └────────────┘  └────────────┘  └────────────┘  └──────────┘  │
└────────────────────────────────────────────────────────────────┘
```

Une seule colonne, max-width ~1120 px, centrée. Titre `Mon répertoire` (h1). Grille de cartes 4 colonnes (auto-wrap si plus). Chaque carte ~280×200 px, padding généreux. La carte "Ajouter" en dernier, **bordure dashed**, ton secondaire.

## Layout mobile (< 768 px)

Cartes empilées pleine largeur (− 16 px gouttières). La carte "Ajouter" tout en bas, dashed.

## Composants

| Nom | Rôle | Primitive |
| --- | --- | --- |
| `OpeningCard` | Une ouverture du répertoire | `Card` shadcn |
| `AddOpeningCard` | Tile d'ajout, bordure dashed | `Card` |
| `ProgressDots` | Bandeau de 10–12 dots, pleins/vides | custom |
| `AddOpeningSheet` | Sheet glissant droite (desktop) / bas (mobile) | `Sheet` shadcn |

## Contenu d'une `OpeningCard`

- **Titre** : nom de l'ouverture (Lora 600 en Classique).
- **Sous-ligne** : chip `Blancs` ou `Noirs` + ECO en mono (`C50`).
- **ProgressDots** : 12 dots. Plein = `studied`, demi = `seen`, vide = `not_seen`.
- **Footnote** muted : `Repris il y a 2 j` (ou `aujourd'hui`).
- **Hover desktop** : `shadow-md` + bordure légèrement plus foncée.

## Données d'exemple (utilise-les telles quelles)

```json
[
  { "name": "Partie italienne", "colour": "Blancs", "eco": "C50", "studied": 5, "total": 12, "seenAgo": "il y a 2 j" },
  { "name": "Caro-Kann",        "colour": "Noirs",  "eco": "B18", "studied": 2, "total": 12, "seenAgo": "il y a 5 j" },
  { "name": "Système de Londres","colour": "Blancs","eco": "D02", "studied": 1, "total": 12, "seenAgo": "aujourd'hui" }
]
```

## Interactions

1. Clic sur `OpeningCard` → navigation vers `/openings/<slug>`.
2. Clic sur `AddOpeningCard` → ouvre `AddOpeningSheet` (Sheet droite desktop, bas mobile).
3. `Tab` / `Enter` clavier : navigation entre cartes, `Enter` ouvre.

## Sheet "Ajouter une ouverture"

Contenu :
- **Titre** : `Ajouter une ouverture`.
- **Section** "Recommandées pour toi" : 2–3 cartes d'ouvertures `recommendedForBeginner = true` non encore au répertoire. Chaque carte : nom + chip couleur + ECO + 1 phrase de résumé + bouton `Ajouter`.
- **Séparateur**.
- **Section** "Tout le catalogue" : filtres `Couleur ▾` (Blancs / Noirs / Tout) + `ECO ▾`, puis liste similaire mais condensée.
- Au clic `Ajouter` : insertion optimiste dans la grille du dashboard, le sheet reste ouvert.

## États

- **Empty** (aucune ouverture) : centre de l'écran, illustration line-art monochrome accent (optionnelle), texte *"Tu n'as pas encore d'ouverture dans ton répertoire."*, bouton primaire `Ajouter une ouverture`.
- **Loading** : 3–4 skeletons d'`OpeningCard` aux mêmes positions.
- **Erreur catalogue** dans le Sheet : bandeau inline *"Impossible de charger le catalogue. Réessaie."* + bouton `Réessayer`.

## Hors scope (ne pas dessiner)

Stats / progression Elo, partage de répertoire, import PGN, sessions multiples.

---

**Output attendu** : wireframes desktop (1280×) et mobile (390×844) en thème Classique. Inclure les états Default, Empty, Loading, Erreur catalogue. Copy 100 % FR.
