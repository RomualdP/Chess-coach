# 00 — Brief canon Chess-coach

> **À coller en premier dans une session Claude Design.** Ce document fixe le système de design et les conventions transverses pour les 4 écrans de l'app. Une fois en contexte, tu peux coller un brief d'écran sans en répéter le préambule. Garde la **même conversation Claude Design** pour les 4 écrans afin de garantir la cohérence visuelle.

---

## Produit

**Chess-coach** : PWA personnelle pour apprendre les ouvertures d'échecs en français. Cible : adulte francophone, débutant ~380 Elo. Pédagogie en couches : idées directrices → ligne principale (12 demi-coups) → déviations → pièges → plans de milieu de partie.

L'app a 4 écrans : Dashboard (répertoire), Étude d'ouverture (l'écran central), Mode pratique (quiz), Position libre (chat IA). Tous partagent **le même app shell, les mêmes thèmes, les mêmes composants**.

## Personnalité

Mentor compétent, accessible. **"Tu" partout.** Pas de condescendance. **Pas d'emojis. Pas de "Bravo !"**, pas de "Excellent !", pas de points d'exclamation gratuits. Density aérée. **Une action principale par écran.** Lecture d'abord, action ensuite.

## Thèmes (3) — rends en Classique par défaut

L'app supporte 3 thèmes, switchables via le header. Ils partagent **la même structure** ; seuls les couleurs, polices et styles de plateau changent.

### Classique (par défaut) — chaud, livre, ivoire + brun + or + bordeaux
```
--background:#FBF7EE --foreground:#3A2E1F --card:#FFFAF0
--primary:#8E2A2A --primary-foreground:#FBF7EE
--secondary:#C4A24A --muted:#EFE8D7 --muted-foreground:#75634A
--accent:#C4A24A --border:#D9CBA8 --ring:#8E2A2A
--destructive:#B23A48
--board-light:#EBD4A6 --board-dark:#B68852
```
Polices : titres **Lora** 500/600/700, corps **Source Sans Pro** 400/600, SAN **JetBrains Mono** 500.

### Moderne — blanc cassé, noir, accent bleu profond
```
--background:#FAFAFA --foreground:#0A0A0A --card:#FFFFFF
--primary:#1E40AF --primary-foreground:#FAFAFA
--secondary:#F1F5F9 --muted:#F1F5F9 --muted-foreground:#64748B
--accent:#1E40AF --border:#E2E8F0 --ring:#1E40AF
--destructive:#DC2626
--board-light:#F1F5F9 --board-dark:#94A3B8
```
Polices : tout UI **Geist Sans** 400/500/600/700, SAN **JetBrains Mono** 500.

### Sombre — gris foncé chaud, texte off-white, accent violet vif
```
--background:#1A1916 --foreground:#EDE7DC --card:#232220
--primary:#8B5CF6 --primary-foreground:#FAFAFA
--secondary:#2D2C2A --muted:#2D2C2A --muted-foreground:#A8A39B
--accent:#8B5CF6 --border:#3A3936 --ring:#8B5CF6
--destructive:#EF4444
--board-light:#4A4540 --board-dark:#2A2622
```
Polices : idem Moderne.

Le switch de thème se fait via `data-theme="classic|modern|dark"` sur `<html>`. Composants lisent les tokens, jamais de hex codés.

## Composants

Stack : **Tailwind + shadcn/ui** (Radix sous le capot). Primitives attendues sur l'app entière : `Button`, `Card`, `Tabs`, `Dialog`, `Sheet`, `DropdownMenu`, `Tooltip`, `Avatar`, `Badge`, `ScrollArea`, `Separator`, `Skeleton`, `Toast`, `Popover`, `Collapsible`, `Progress`, `ToggleGroup`, `Select`, `Input`, `Textarea`.

Composants custom (réutilisés entre écrans) :
- **`Chessboard`** : wrap de `react-chessboard`. Taille desktop ~560 × 560 px, mobile ~92 % largeur viewport.
- **`PlyNavigator`** : boutons ← → + jump, état conscient du progrès.
- **`MoveNotation`** : SAN en mono, optionnellement avec un chip de verdict.
- **`MiniChat`** : conversation ancrée sur la position courante.

Icônes : **Lucide outline** uniquement, stroke 1.5, tailles 16/20/24 px. Pas d'icônes pleines.

## Spacing, formes, ombres

- Échelle px : **4, 8, 12, 16, 24, 32, 48, 64**.
- Rayons : **4** (chips/badges), **8** (boutons/inputs), **12** (cards). **Jamais > 12.**
- Ombres : **`shadow-sm` ou `shadow-md` uniquement.** Pas de `shadow-lg`/`xl`.
- Bordures : **1 px** theme-aware via `--border`. Éviter le double cadrage (border + shadow ensemble).

## Typographie

- Corps : **16 px** (1 rem), line-height **1.5**.
- Titres : line-height **1.25**. Un seul `<h1>` par écran.
- SAN (notation) : monospace, line-height **1.2**.

## App shell (commun à tous les écrans)

Header **sticky** 56 px desktop / 48 px mobile :

| Slot | Desktop | Mobile |
| --- | --- | --- |
| Gauche | Logo + nom "Chess-coach" | Logo seul |
| Droite | Icône toggle thème · lien "Position libre" · avatar dropdown | Avatar dropdown seul |

Avatar dropdown : sélecteur de thème (3 options), sélecteur de jeu de pièces, déconnexion.

**Pas de sidebar, pas de footer.** L'app a 4 écrans, la navigation est minimale. Le logo retourne toujours au dashboard. Chaque écran non-dashboard a un **back chevron** sous le header (`◂ Mon répertoire` ou similaire).

## Patterns transverses

- **Loading** : skeleton screens, **jamais** de spinner ni le mot "Chargement…".
- **Empty state** : 1 phrase déclarative + bouton de l'action suivante. Optionnel : illustration line-art monochrome accent.
- **Erreur** : inline là où ça s'est produit, jamais full-page sauf si le shell entier est mort. Toujours un bouton `Réessayer`.
- **Toasts** : top-right desktop, top-center mobile. 4 s succès, 6 s erreur. Une seule ligne, optionnellement une action. Pas d'icônes (la couleur fait le travail).
- **Mobile < 768 px** : colonne unique empilée, gouttière 16 px, board ~92 % largeur viewport.

## Notation française des pièces

Cavalier=**C**, Fou=**F**, Tour=**T**, Dame=**D**, Roi=**R**. Roque petit `O-O`, grand `O-O-O`. Dans la prose : SAN en *italique*. Dans les composants data (listes d'alternatives, déviations, etc.) : SAN en monospace via `MoveNotation`.

## Échiquier

- Orientation par défaut : **Blancs en bas pour ouverture Blanche**, **Noirs en bas pour ouverture Noire**.
- Coordonnées **on** par défaut.
- Last-move highlight via `--last-move`. Legal-move dots via `--legal-move`.
- Pas de flèches par défaut ; `Alt + click-drag` pour en dessiner (parité lichess).
- Sélection au clic puis placement au clic supportée en plus du drag.

## Raccourcis clavier (desktop)

- `←` / `→` : ply précédent / suivant (Étude, Pratique).
- `Espace` : toggle explication longue (Étude).
- `?` : aide raccourcis.
- `T` : cycler les thèmes.
- `Esc` : fermer l'overlay du dessus (dialog, sheet, popover).

## French copy guidelines

- **Confirms** : `Confirmer` (primary), `Annuler` (secondary).
- **Empty** : *"Tu n'as pas encore d'ouverture dans ton répertoire. Ajoute-en une pour commencer."*
- **Erreur** : factuel, actionnable. *"Connexion perdue. Réessaie dans un instant."*
- **Interdits** : "Bravo !", "Excellent !", "Parfait !", "Oups !", emojis (partout), "?!".

## Accessibilité

- Contraste AA minimum (4.5:1 corps, 3:1 grand texte). Vérifier sur les 3 thèmes.
- Focus rings toujours visibles via `--ring`.
- Tab order logique. Tous les interactifs au clavier.
- Échiquier : `aria-label` par case (e.g. *"e4, pion blanc"*). Opérable au clavier (flèches + entrée).

## Output attendu (toutes les générations qui suivront)

Pour **chaque écran** que je te demanderai de concevoir :
- Wireframes haute fidélité **desktop 1280×** et **mobile 390×844**.
- Tous les états listés dans le brief écran (loading, empty, error, etc.).
- Copy **100 % en français**.
- Thème **Classique** sauf demande explicite contraire.
- Composants nommés dans l'inventaire de chaque brief.

## Cohérence inter-écrans

Le même bouton primaire, le même `Card`, le même header, la même tabbed nav doivent **avoir le même look exactement** d'un écran à l'autre. Si tu introduis un pattern visuel sur un écran (par exemple le rendu d'une déviation comme une carte avec un chip de fréquence), réutilise le même pattern partout où il s'applique.

---

**Acquittement attendu :** réponds par "Compris" puis résume en 5 points : (1) la palette dominante du thème Classique, (2) les polices, (3) le ton, (4) les primitives clés, (5) les patterns d'erreur/empty/loading. Ensuite attends le brief écran que je collerai dans le message suivant.
