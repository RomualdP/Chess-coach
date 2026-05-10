# Briefs Claude Design — paste-ready

Ce dossier contient tout ce qu'il faut pour générer les wireframes des 4 écrans avec Claude Design (ou tout outil de design IA).

## Quel workflow choisir ?

### Workflow recommandé : "canon + écran" — pour la cohérence visuelle inter-écrans

**Une seule conversation Claude Design pour les 4 écrans.** Tu colles le brief canon une fois, puis tu colles un brief écran par message.

1. Ouvre une nouvelle conversation Claude Design.
2. Colle [`00-canon.md`](./00-canon.md). Claude Design répond "Compris" + résumé.
3. Pour chaque écran à designer, colle **uniquement** la moitié inférieure du fichier écran (à partir de la ligne `# Écran à concevoir`). Garde la même conversation.
4. Itère sur chaque écran tant que tu n'es pas content.

**Avantage** : le système de design (palette, composants, patterns) est défini une fois et appliqué uniformément aux 4 écrans. Le bouton primaire de l'écran 2 est exactement le même que celui de l'écran 1.

### Workflow alternatif : "tout-en-un" — pour un écran isolé

Si tu veux designer un seul écran sans te soucier des autres (ou si tu repars d'une conversation neuve), colle le fichier écran **en entier** — il est autonome et contient tout le système de design en préambule.

**Limite** : si tu fais ça pour les 4 écrans dans 4 conversations séparées, attends-toi à de petites incohérences entre les rendus.

## Les 5 fichiers

| Fichier | Rôle |
| --- | --- |
| [`00-canon.md`](./00-canon.md) | **Brief canon** — système de design, conventions transverses. À coller en premier. |
| [`dashboard.md`](./dashboard.md) | Écran d'accueil — répertoire d'ouvertures + flow d'ajout. |
| [`opening-study.md`](./opening-study.md) | Écran central — étude d'une ouverture en couches. |
| [`practice-mode.md`](./practice-mode.md) | Mode pratique — quiz sur la ligne principale. |
| [`free-position.md`](./free-position.md) | Position libre — bac à sable + chat IA. |

## Comment copier depuis l'app Claude / mobile

1. Ouvre le repo sur GitHub mobile.
2. Navigue dans `docs/05-screens/_paste-ready/`.
3. Tape sur le fichier voulu. Bouton **Raw** en haut à droite.
4. Press-hold → **Tout sélectionner** → **Copier**.
5. Colle dans Claude Design.

Si copier en raw est trop pénible : demande dans cette conversation, et on te dump le contenu directement comme bloc de code à press-hold.

## Versions canoniques (pour référence)

Les briefs paste-ready sont des versions condensées. Les versions complètes vivent à côté et restent la source de vérité pour le projet :

- `docs/04-design-system.md` — système de design complet (palettes, composants, accessibilité, etc.).
- `docs/05-screens/README.md` — conventions transverses (app shell, raccourcis, patterns).
- `docs/05-screens/{dashboard,opening-study,practice-mode,free-position}.md` — briefs écran complets.

Si Claude Design demande plus de détails sur un point précis, va piocher dans ces docs.
