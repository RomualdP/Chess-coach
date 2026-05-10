# Chess-coach
Chess Coach — Récapitulatif d’étape complet
1. Vision et concept
Nature du projet : application web personnelle d’apprentissage des échecs, principalement à usage personnel (Romuald, 380 Elo, débutant), avec possibilité d’ouverture future à des proches ou à un public restreint.
Problème résolu : les outils existants (chess.com, lichess) donnent des évaluations de coups via Stockfish mais expliquent rarement pédagogiquement le pourquoi des coups, surtout pour les débutants en français.
Proposition de valeur : une app qui combine la rigueur d’un moteur d’échecs (Stockfish) avec la pédagogie d’une IA conversationnelle (Mistral) et un contenu structuré rédigé spécifiquement pour les débutants en français.
Nom de code : chess-coach (nom marketing à définir plus tard).
Personnalité : expert mais accessible — ton de mentor compétent, sans condescendance ni infantilisation.
2. Périmètre fonctionnel
Module prioritaire au MVP
Module 2 — Apprentissage des ouvertures et transition vers le milieu de jeu.
Justification : à 380 Elo en parties rapides 10 minutes, sortir d’ouverture avec un bon plan en tête est ce qui apporte le plus de valeur. Si l’ouverture est ratée, tout le reste se joue en réaction.
Modules reportés en V2/V3
	•	Module 1 — Analyse pédagogique de partie : import depuis chess.com, détection d’erreurs, explications. Reporté en V3.
	•	Module 3 — Détection de patterns récurrents : analyse longitudinale de tes parties pour identifier tes faiblesses. Reporté en V3.
Features du Module 2 retenues pour le MVP
	•	Sélection guidée d’ouvertures (catalogue avec recommandations)
	•	Étude d’une ouverture par couches (idées directrices, séquence principale, déviations courantes, pièges, plans de milieu de jeu)
	•	Mode pratique (quiz interactif sur la séquence principale)
	•	Chat libre avec l’IA sur une position donnée
Features du Module 2 reportées
	•	Mode entraînement contre l’app (avec Maia comme moteur “humain”)
	•	Anticipation des déviations adverses via Maia
	•	Pratique des déviations (mode quiz avancé)
	•	Pratique du milieu de jeu guidé
	•	Sync avec chess.com pour analyser tes vraies parties
Les 4 écrans du MVP
	1.	Dashboard / Sélection de répertoire — vue d’ensemble des ouvertures actives, ajout de nouvelles ouvertures via parcours guidé
	2.	Étude d’une ouverture — l’écran principal, avec échiquier, explications, navigation, mini-chat
	3.	Mode pratique — quiz interactif sur la séquence d’une ouverture
	4.	Position libre / Chat — bac à sable pour poser des questions sur n’importe quelle position
3. Architecture pédagogique
Le principe directeur
Stockfish est un outil de vérification, pas un oracle pédagogique. La pédagogie vient de la knowledge base (rédigée par des humains pour des humains), Stockfish sert à valider et garantir que rien de faux ne soit affirmé, et le LLM adapte le tout au niveau de l’utilisateur.
Architecture en 3 niveaux
Niveau 1 — Knowledge base structurée (le socle)
Pour chaque ouverture, une fiche JSON structurée rédigée à l’avance contenant : ligne principale avec explications par coup, idées directrices, déviations courantes adaptées au niveau, pièges classiques, plans typiques de milieu de jeu.
Niveau 2 — Stockfish (la vérité tactique)
Stockfish WASM côté client. Rôle : évaluer ponctuellement les positions, vérifier les déviations adverses non prévues, valider les coups en mode pratique. Pas utilisé pour déterminer les “meilleurs coups” d’ouverture (la knowledge base s’en charge).
Niveau 3 — LLM (Mistral) (le pédagogue)
Génère les explications dynamiques en s’appuyant sur la knowledge base + les évaluations Stockfish. Adapte au niveau débutant. Ne calcule jamais, n’invente pas de variantes.
Calibrage pour débutant
Le système est conçu pour respecter les principes échiquéens (développement, centre, sécurité du roi) plutôt que pour pousser les coups objectivement les plus forts. Stockfish sera limité à profondeur modérée (12-15) et le LLM est instruit pour privilégier les coups “principiels” sur les coups “techniques”.
Maia (V2)
Maia (réseau de neurones jouant comme un humain de niveau Elo donné) sera intégré en V2 pour :
	•	Servir d’adversaire dans le mode “joue contre l’app”
	•	Anticiper les déviations probables des adversaires réels du joueur
4. Stack technique
Frontend
	•	Next.js 16 (App Router) avec React 19.2 et TypeScript
	•	Tailwind CSS + shadcn/ui pour les composants
	•	TanStack Query pour le data fetching et le cache
	•	react-chessboard + chess.js pour l’échiquier
	•	stockfish.wasm dans un Web Worker
	•	Serwist (ou next-pwa) pour la PWA
Backend
	•	NestJS + TypeScript
	•	Prisma comme ORM
	•	@mistralai/mistralai SDK pour le LLM
	•	Authentification via Supabase Auth (validation JWT côté NestJS)
Base de données
	•	Supabase PostgreSQL (nouveau projet à créer dans l’organisation existante)
	•	Utilisation de JSONB pour les structures complexes (arbres de variantes)
	•	Auth Supabase pour le compte utilisateur
Hébergement
	•	Frontend Next.js : Railway (à confirmer vs Vercel — décision restant à prendre)
	•	Backend NestJS : Railway
	•	Base de données : Supabase
LLM
	•	Mistral (français, moins cher, données européennes)
	•	Mistral Small au MVP, possibilité de remonter à Mistral Medium si besoin
	•	Caching de prompts pour optimiser les coûts
5. Architecture pour le scaling progressif
Décisions prises pour préparer une éventuelle ouverture (sans sur-anticiper)
	1.	Stockfish encapsulé derrière une interface ChessEngineService permettant un switch ultérieur (vers Stockfish serveur ou Maia)
	2.	Schéma Prisma avec userId partout dès le départ, même en utilisateur unique
	3.	Logger basique côté backend trackant usages (analyses Stockfish, appels LLM, par utilisateur)
	4.	Auth Supabase fonctionnelle dès le déploiement en ligne
Évolution prévue selon les paliers
	•	Scénario A (toi + 5-10 proches) : aucun changement architectural
	•	Scénario B (50-200 utilisateurs) : ajout de Stockfish serveur en hybride, quotas, monitoring
	•	Scénario C (1000+) : refonte plus large basée sur les données d’usage réelles
6. Compatibilité et déploiement
Type d’app
Progressive Web App (PWA) responsive, fonctionnant sur mobile et desktop via le navigateur.
Décisions PWA
	•	PWA installable au MVP : manifest, icônes, service worker basique pour les assets statiques
	•	Mode hors-ligne complet reporté en V2 : architecture préparée (API “data-block oriented”, TanStack Query) pour ajouter le hors-ligne sans refonte
	•	Pas d’app native : la PWA est suffisante pour l’usage prévu
Stratégie offline-ready
	•	Les fiches d’ouvertures sont récupérées en blocs complets (pas coup par coup)
	•	TanStack Query gère le cache côté client
	•	Cache d’explications LLM côté serveur au MVP, ajout d’un cache client en V2
7. Système de design
Principe : structure commune, thèmes variables
Structure figée : hiérarchie visuelle, agencement des écrans, patterns d’interaction, espacements proportionnels — communs à tous les thèmes.
Thèmes variables : couleurs, polices, style d’échiquier et de pièces — modifiables par l’utilisateur.
Densité d’information
Aérée : espacement généreux, hiérarchie visuelle nette, une action principale par écran, info progressive (résumé par défaut, détails au clic).
3 thèmes proposés au MVP
Thème Classique (par défaut) — l’apprentissage comme un livre
	•	Fond ivoire chaud, texte brun foncé, accents doré et bordeaux
	•	Échiquier bois clair / bois foncé classique
	•	Police titres : Lora (serif chaleureuse)
	•	Police texte : Source Sans Pro ou Public Sans
	•	Sentiment : prendre son temps, étudier sérieusement
Thème Moderne — l’app productivité tech
	•	Fond blanc cassé, texte noir, accent bleu profond
	•	Échiquier neutre épuré
	•	Police partout : Inter ou Geist (sans-serif moderne)
	•	Sentiment : focus, efficacité, outil pro
Thème Sombre — la session du soir
	•	Fond gris foncé chaud, texte blanc cassé, accent bleu vif ou violet
	•	Échiquier sombre adapté
	•	Police : Geist ou Inter
	•	Sentiment : ambiance posée, fin de journée, session sans agression visuelle
Design tokens (architecture)
Système basé sur CSS variables avec data-attribute (data-theme="classic", etc.) pour switcher entre thèmes. Pattern aligné avec shadcn/ui.
Personnalisation utilisateur au MVP
	•	Choix entre les 3 thèmes globaux
	•	Choix du jeu de pièces (Cburnett par défaut, plus 2-3 alternatives)
	•	Toggle rapide clair/sombre dans le header
Personnalisation reportée en V2
	•	Couleurs custom de l’échiquier
	•	Sons et animations on/off
	•	Coordonnées on/off
	•	Thèmes additionnels (néon, vintage, communautaires)
Détails techniques
	•	Arrondis modérés (4px / 8px / 12px), ni trop iOS ni brutaliste
	•	Ombres subtiles, pas de “carte qui flotte” agressif
	•	Espacement basé sur 4px (4, 8, 12, 16, 24, 32, 48, 64)
	•	Polices serif acceptées dans le thème Classique uniquement
	•	Notation des coups en monospace (JetBrains Mono)
8. Modèle de données (vue d’ensemble)
Schéma à approfondir, mais entités principales identifiées :
	•	users : id, email, niveau Elo, préférences thème
	•	openings : id, nom, code ECO, couleur, contenu structuré (JSONB pour main_line, core_ideas, moves, common_deviations, middlegame_plans), recommandé_débutant
	•	user_repertoires : association user-opening, niveau de maîtrise, date d’ajout
	•	user_progress : par opening et par ply, statut (not_seen / seen / studied / mastered)
	•	study_sessions : historique des sessions
	•	cached_explanations : cache des explications LLM par position et niveau
	•	llm_conversations : historique des chats par session/position
9. Critères de succès du MVP
À approfondir, mais grandes orientations :
	•	L’app est utilisable quotidiennement sans bug bloquant
	•	Le contenu pédagogique des fiches d’ouvertures est de qualité (validé par toi)
	•	Les explications générées sont pertinentes et adaptées au niveau débutant (>80% de qualité ressentie)
	•	Tu utilises l’app au moins 3 fois par semaine pendant 1 mois
	•	Tu sens une progression dans ton jeu (objectif moyen terme : passer de 380 à 600+ Elo)
10. Ce qui reste à approfondir
Pour atteindre le “plan de bataille pour Claude Code en autonomie”, il reste à traiter :
Avant le développement (à ce stade-ci)
	1.	Les wireframes des 4 écrans — layout précis desktop et mobile, composants, interactions
	2.	Le format définitif de la knowledge base — structure JSON figée avec un exemple complet et validé
	3.	Les prompts LLM v0 — au moins 4-5 prompts clés rédigés et testés
	4.	Le schéma Prisma complet — avec tous les champs, relations, index
	5.	Les palettes complètes des 3 thèmes — codes hex précis pour tous les tokens (Moderne et Sombre, en plus du Classique déjà esquissé)
	6.	La liste des 3 premières ouvertures à rédiger et leur plan de rédaction
	7.	Les critères de succès formalisés — métriques précises et seuils
	8.	La structure du repo — organisation des dossiers frontend et backend
	9.	Le CLAUDE.md — instructions pour Claude Code
À approfondir au moment de chaque feature
	•	Spécifications détaillées de chaque écran lors de son implémentation
	•	Configuration précise de Stockfish par scénario d’usage
	•	Algorithme de sélection des positions à réviser en mode pratique
	•	Détails des animations et transitions
Points décisionnels encore en suspens
	1.	Frontend sur Railway ou Vercel ?
	2.	Prisma vs Drizzle comme ORM (Prisma recommandé pour la productivité)
	3.	Détails finaux des palettes (validation des couleurs précises pour les 3 thèmes)
	4.	Choix précis des polices (Lora confirmé pour Classique, mais Inter vs Geist pour Moderne et Sombre à valider)
	5.	Auth dès le déploiement ou plus tard
11. Méthode de travail
Approche : préparer un package de documents complet structuré pour optimiser le travail avec Claude Code en autonomie.
Structure de docs cible :
chess-coach/
├── docs/
│   ├── 00-vision.md
│   ├── 01-product-spec.md
│   ├── 02-architecture.md
│   ├── 03-data-model.md
│   ├── 04-design-system.md
│   ├── 05-screens/
│   │   ├── dashboard.md
│   │   ├── opening-study.md
│   │   ├── practice-mode.md
│   │   └── free-position.md
│   ├── 06-pedagogy/
│   │   ├── knowledge-base-format.md
│   │   ├── llm-prompts.md
│   │   └── stockfish-config.md
│   ├── 07-content/
│   │   ├── italian-game.md
│   │   ├── caro-kann.md
│   │   └── london-system.md (à confirmer)
│   └── 08-roadmap.md
├── CLAUDE.md
└── README.md
