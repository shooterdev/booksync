# Project Context

## Purpose

**BookSync** est une application de gestion de collection de mangas conçue pour fonctionner sur Raspberry Pi avec écran tactile. Elle permet aux utilisateurs de :

- Gérer leur collection de mangas (volumes possédés, lus, wishlist)
- Consulter le planning des sorties et anticiper les achats
- Recevoir des recommandations de lecture personnalisées via IA
- Scanner des ISBN via douchette Bluetooth pour un ajout rapide
- Synchroniser avec l'API Mangacollec (source de vérité du catalogue)

L'objectif est de fournir une expérience fluide et tactile pour les collectionneurs de mangas, avec un mode offline partiel via cache local.

## Tech Stack

### Frontend (Raspberry Pi)

| Technologie         | Version | Usage                              |
|---------------------|---------|-----------------------------------|
| **Python**          | 3.11+   | Langage principal                 |
| **PySide6**         | 6.6+    | Framework UI Qt officiel          |
| **QML**             | Qt 6    | Interface déclarative             |
| **Qt Quick Controls 2** | -   | Composants optimisés tactile      |
| **Qt Charts**       | -       | Statistiques collection           |
| **httpx**           | 0.27+   | Client HTTP async                 |
| **SQLite**          | 3.x     | Cache local                       |
| **SQLAlchemy**      | 2.0+    | ORM async                         |
| **Pydantic**        | 2.x     | Validation DTOs, settings         |
| **keyring**         | 24+     | Stockage sécurisé tokens          |

### Backend (Serveur/NAS) - Microservices

| Service          | Framework     | Port | Description                           |
|------------------|---------------|------|---------------------------------------|
| **Auth API**     | FastAPI       | 8000 | Authentification JWT + OAuth2 proxy   |
| **Data API**     | FastAPI       | 8001 | Données extra volumes (scraping)      |
| **Prediction API** | FastAPI     | 8002 | Recommandations IA (pgvector)         |
| **PostgreSQL**   | 17            | 5432 | Base de données principale            |

### Dépendances Backend Communes

- `fastapi` (0.104-0.109)
- `uvicorn[standard]`
- `sqlalchemy[asyncio]` 2.0+
- `asyncpg` (driver PostgreSQL async)
- `pydantic` 2.x / `pydantic-settings`
- `httpx` (client HTTP)
- `alembic` (migrations)

### Service Prediction (spécifique)

- `pgvector` 0.2.0 (embeddings)
- `sentence-transformers` 2.2.2
- `torch` 2.1.1
- `numpy`

### Outils de Développement

| Outil          | Usage                              |
|----------------|------------------------------------|
| **uv**         | Gestionnaire de packages (rapide)  |
| **hatchling**  | Build system                       |
| **pytest**     | Tests unitaires/intégration        |
| **pytest-asyncio** | Tests async                    |
| **pytest-qt**  | Tests interface Qt                 |
| **ruff**       | Linter rapide                      |
| **black**      | Formatage code (line-length=100)   |
| **isort**      | Tri des imports                    |
| **mypy**       | Vérification types                 |
| **Docker Compose** | Orchestration services         |

## Project Conventions

### Code Style

- **Line length** : 100 caractères
- **Python version** : 3.11+ (target 3.11, 3.12)
- **Formatage** : Black (automatique)
- **Linting** : Ruff (règles E, F, W, I)
- **Imports** : isort (profile black)
- **Types** : mypy avec `disallow_untyped_defs=true` (Auth/Prediction)
- **Encodage** : UTF-8
- **Langue du code** : Anglais (variables, fonctions, classes)
- **Langue des commentaires/docs** : Français

### Naming Conventions

- **Fichiers Python** : `snake_case.py`
- **Classes** : `PascalCase`
- **Fonctions/variables** : `snake_case`
- **Constantes** : `UPPER_SNAKE_CASE`
- **Fichiers QML** : `PascalCase.qml`
- **IDs QML** : `camelCase`

### Architecture Patterns

**Clean Architecture (Hexagonale)** - Dépendances vers l'intérieur :

```
PRESENTATION (QML + Controllers)
        ↓
APPLICATION (Services / Use Cases)
        ↓
DOMAIN (Entities + Ports/Interfaces)
        ↓
INFRASTRUCTURE (API Client, Cache, Scanner)
```

**Règle d'or** : Le Domain ne dépend de rien.

### Structure des Services

Chaque microservice suit cette structure :

```
booksync_api_<service>/
├── src/booksync_api_<service>/
│   ├── __init__.py
│   ├── main.py
│   ├── config.py
│   ├── models/
│   ├── schemas/
│   ├── services/
│   └── routers/
├── tests/
├── pyproject.toml
├── Dockerfile
└── README.md
```

### Testing Strategy

- **Framework** : pytest + pytest-asyncio
- **Mode async** : `asyncio_mode = "auto"`
- **Options** : `-v --strict-markers --tb=short`
- **Coverage** : pytest-cov
- **Tests Qt** : pytest-qt
- **Fichiers de test** : `tests/test_*.py`
- **Règle** : Tests créés via sub-agents pour validation indépendante

### Git Workflow

- **Architecture** : Monorepo + Git Submodules
- **Branches** : `feat/<issue>-<epic>-<feature>`, `fix/<issue>-<description>`
- **Commits** : Conventional Commits (`feat:`, `fix:`, `chore:`, `docs:`)
- **Pas de mention** : "Generated with Claude Code"
- **Pas de co-auteurs** dans les commits
- **PRs** : Via `gh cli`

## Domain Context

### Entités Principales

| Entité         | Description                                |
|----------------|--------------------------------------------|
| **Volume**     | Tome individuel d'une édition              |
| **Série**      | Œuvre complète (ex: "One Piece")           |
| **Édition**    | Version publiée chez un éditeur            |
| **Possession** | Relation utilisateur ↔ volume possédé      |
| **User**       | Utilisateur avec profil et préférences     |

### Sources de Données

| Source              | Responsabilité                         | Type       |
|---------------------|----------------------------------------|------------|
| **API Mangacollec** | Catalogue, séries, volumes, collection | Externe    |
| **Auth API**        | JWT local, credentials Mangacollec     | Local      |
| **Data API**        | Données extra (dimensions, poids)      | Local      |
| **Prediction API**  | Recommandations via embeddings         | Local      |
| **Cache SQLite**    | Performance + mode offline             | Embarqué   |

### Flux d'Authentification

1. L'App Qt demande un token à l'Auth API locale
2. L'Auth API gère les tokens Mangacollec (access + refresh)
3. L'App appelle directement l'API Mangacollec avec le token valide

## Important Constraints

### Contraintes Matérielles

| Composant | Spécification                    |
|-----------|----------------------------------|
| Machine   | Raspberry Pi 4/5                 |
| Écran     | Tactile 1280 × 720 pixels        |
| RAM       | 2-4 GB                           |
| Réseau    | LAN vers serveur/NAS             |

### Contraintes Techniques

- **Performance** : Interface fluide sur RPi (optimisation GPU via Qt)
- **Offline** : Mode lecture seule avec cache SQLite
- **Sécurité** : Tokens stockés dans keyring, jamais en clair
- **Qualité** : Conformité SonarQube (Clean Code)

### Règles de Développement

- **Contexte obligatoire** : Lire 3-5 fichiers liés avant modification
- **Fichiers temporaires** : Dans `/tmp_claude` à la racine
- **Tests** : Toujours via sub-agents
- **Context7** : Utiliser pour génération de code et documentation

## External Dependencies

### API Mangacollec (Externe)

- **URL** : `https://api.mangacollec.com/`
- **Auth** : OAuth2 (Client ID + Secret)
- **Rôle** : Source de vérité pour le catalogue et la collection

**Endpoints principaux** :
- `GET /volumes` - Catalogue des tomes
- `GET /series` - Liste des séries
- `GET /editions` - Éditions par éditeur
- `GET /releases` - Planning des sorties
- `GET /users/me/possessions` - Collection utilisateur
- `POST /users/me/possessions` - Ajouter un tome
- `DELETE /users/me/possessions/{id}` - Retirer un tome
- `GET /search` - Recherche globale

### Services Locaux

| Service        | URL                            | Port |
|----------------|--------------------------------|------|
| Auth API       | `http://192.168.1.100:8000`    | 8000 |
| Data API       | `http://192.168.1.100:8001`    | 8001 |
| Prediction API | `http://192.168.1.100:8002`    | 8002 |
| PostgreSQL     | `192.168.1.100:5432`           | 5432 |

## Docker & Déploiement

### Images Docker

| Service        | Image                            | Base         | Architectures          |
|----------------|----------------------------------|--------------|------------------------|
| Auth API       | `booksync-api-auth:latest`       | python:3.12-slim | linux/amd64, linux/arm64 |
| Data API       | `booksync-api-data:latest`       | python:3.12-slim | linux/amd64, linux/arm64 |
| Prediction API | `booksync-api-prediction:latest` | python:3.12-slim | linux/amd64, linux/arm64 |
| PostgreSQL     | `pgvector/pgvector:pg17`         | PostgreSQL 17    | linux/amd64, linux/arm64 |

### Utilisateurs PostgreSQL (sécurité)

Chaque service dispose de son propre utilisateur PostgreSQL :

| Service        | Utilisateur           | Base de données |
|----------------|-----------------------|-----------------|
| Auth API       | `booksync_auth`       | `booksync`      |
| Data API       | `booksync_data`       | `booksync`      |
| Prediction API | `booksync_prediction` | `booksync`      |

### Commandes Docker

```bash
# Build local (architecture courante)
docker build -t booksync-api-auth:latest ./booksync_api_auth
docker build -t booksync-api-data:latest ./booksync_api_data
docker build -t booksync-api-prediction:latest ./booksync_api_prediction

# Build multi-architecture (arm64 + amd64)
docker buildx build --platform linux/amd64,linux/arm64 -t booksync-api-auth:latest --push ./booksync_api_auth

# Démarrage des services
docker-compose up -d

# Avec Prediction API (V3)
docker-compose --profile v3 up -d

# Logs
docker-compose logs -f
```

### Réseau Docker

- **Network** : `booksync-network` (bridge)
- **Communication inter-services** : Via nom de service (ex: `postgres:5432`)

## Architecture Git

### Structure Monorepo + Submodules

```
shooterdev/booksync                    # Monorepo principal (orchestration)
├── shooterdev/booksync-api-auth       # Submodule → Auth API
├── shooterdev/booksync-api-data       # Submodule → Data API
├── shooterdev/booksync-api-prediction # Submodule → Prediction API
└── shooterdev/booksync-app-qt         # Submodule → App desktop
```

### Correspondance Repos ↔ Dossiers

| Repo GitHub                          | Dossier local              | Description                     |
|--------------------------------------|----------------------------|---------------------------------|
| `shooterdev/booksync`                | `/` (racine)               | Monorepo principal              |
| `shooterdev/booksync-api-auth`       | `booksync_api_auth/`       | Auth API - JWT & OAuth2 (8000)  |
| `shooterdev/booksync-api-data`       | `booksync_api_data/`       | Data API - Données extra (8001) |
| `shooterdev/booksync-api-prediction` | `booksync_api_prediction/` | Prediction API - Reco V3 (8002) |
| `shooterdev/booksync-app-qt`         | `booksync_app_qt/`         | App desktop PySide6/QML         |

### Commandes Git Submodules

```bash
# Cloner avec tous les submodules
git clone --recurse-submodules git@github.com:shooterdev/booksync.git

# Mettre à jour tous les submodules
git submodule update --remote --merge

# Après modification dans un submodule
cd booksync_api_auth
git add . && git commit -m "feat: nouvelle fonctionnalité" && git push
cd ..
git add booksync_api_auth
git commit -m "chore: update booksync-api-auth submodule"
```

## Priorisation MVP / V1 / V2 / V3

### MVP — App utilisable (Core)

| Fonctionnalité                        | Justification                      |
|---------------------------------------|-----------------------------------|
| Navigation latérale                   | Structure de l'app                |
| Thème clair/sombre                    | Confort visuel dès le départ      |
| Collection                            | Cœur de l'app                     |
| Recherche globale (Titres uniquement) | Trouver un manga                  |
| Fiche Volume                          | Détail + actions (ajouter/retirer)|
| Fiche Série                           | Voir les éditions                 |
| Fiche Édition                         | Voir tous les tomes               |
| Synchronisation API + Cache local     | Infrastructure obligatoire        |

### V1 — Valeur quotidienne

| Fonctionnalité                         | Justification            |
|----------------------------------------|--------------------------|
| Accueil / Nouveautés                   | Page d'entrée, découverte|
| Compléter (Tomes manquants)            | Identifier les trous     |
| Envies (Wishlist)                      | Planifier ses achats     |
| Planning personnalisé + global         | Ne plus rater une sortie |
| Recherche (Auteurs + Éditeurs)         | Recherche complète       |
| Fiche Auteur + Fiche Éditeur           | Navigation enrichie      |
| Gestion du compte (basique)            | Profil + connexion       |

### V2 — Lecture & Avancé

| Fonctionnalité              | Justification              |
|-----------------------------|----------------------------|
| Pile à lire                 | Suivre sa progression      |
| Statistiques de collection  | Visualisation (graphiques) |
| Historique collection/lecture| Journal des ajouts/lectures|
| Planning nouveautés (T1)    | Découverte nouvelles séries|
| Planning coffrets           | Éditions spéciales         |
| Panier d'achats             | Gestion des achats intégrée|
| Scanner code-barres         | Ajout rapide               |

### V3 — Prediction / Recommandations

| Fonctionnalité            | Description                                               |
|---------------------------|-----------------------------------------------------------|
| QCM de préférences        | Questionnaire rapide sur les envies du moment             |
| Sélection d'humeur        | Choix d'une humeur (joyeux, mélancolique, stressé...)     |
| Prédiction de lecture     | Algorithme de suggestion basé sur QCM/humeur + embeddings |
| Prédiction historique     | Historique des prédictions                                |

### Hors-périmètre (V4+)

- Gestion des prêts
- Multi-utilisateur
- Gestion du compte (avancée)

## Charte Graphique

### Mode Sombre (Dark Mode) — Principal

| Variable              | Valeur                      | Usage                       |
|-----------------------|-----------------------------|-----------------------------|
| `colors.primary`      | `#fc3117`                   | Accents, boutons principaux |
| `colors.background`   | `#000000`                   | Fond application            |
| `colors.card`         | `#121212`                   | Fond des cartes             |
| `colors.text`         | `#e5e5e7`                   | Texte principal             |
| `colors.textDetail`   | `#AAAAAA`                   | Texte secondaire            |
| `colors.border`       | `#3c3c43`                   | Bordures                    |
| `colors.icon`         | `#666666`                   | Icônes                      |
| `colors.read`         | `#536DFE`                   | Badge "Lu"                  |
| `colors.cart`         | `#f38f21`                   | Panier                      |
| `colors.stateHovered` | `rgba(255, 255, 255, 0.1)`  | État survolé                |
| `colors.statePressed` | `rgba(255, 255, 255, 0.15)` | État pressé                 |

### Mode Clair (Light Mode)

| Variable              | Valeur                | Usage                       |
|-----------------------|-----------------------|-----------------------------|
| `colors.primary`      | `#CF000A`             | Accents, boutons principaux |
| `colors.background`   | `#FFFFFF`             | Fond application            |
| `colors.card`         | `#FFFFFF`             | Fond des cartes             |
| `colors.text`         | `#1c1c1e`             | Texte principal             |
| `colors.textDetail`   | `#777777`             | Texte secondaire            |
| `colors.border`       | `#e0e0e0`             | Bordures                    |
| `colors.icon`         | `#BBBBBB`             | Icônes                      |
| `colors.read`         | `#3D5AFE`             | Badge "Lu"                  |
| `colors.cart`         | `#F5B027`             | Panier                      |
| `colors.stateHovered` | `rgba(0, 0, 0, 0.07)` | État survolé                |
| `colors.statePressed` | `rgba(0, 0, 0, 0.12)` | État pressé                 |

## Stratégie de Cache

### TTL par Type de Données

| Données                     | TTL       | Justification                             |
|-----------------------------|-----------|-------------------------------------------|
| Collection utilisateur      | 5 min     | Données personnelles fréquemment modifiées|
| Catalogue (séries, volumes) | 24h       | Rarement modifié                          |
| Planning sorties            | 1h        | Mises à jour quotidiennes                 |
| Images couvertures          | Permanent | Stockage définitif                        |

### Variables d'Environnement Cache

```bash
BOOKSYNC_CACHE_DIR=~/.local/share/booksync
BOOKSYNC_CACHE_TTL_COLLECTION=300      # 5 minutes
BOOKSYNC_CACHE_TTL_CATALOG=86400       # 24 heures
```

### Mode Offline

- **Lecture** : Données du cache SQLite disponibles
- **Écriture** : **Désactivée** — Toute modification nécessite une connexion

## Modèle de Données Principal

### Tables Catalogue

| Table        | Clé primaire | Description                          |
|--------------|--------------|--------------------------------------|
| `types`      | `id` (uuid)  | Types de séries (manga, manhwa...)   |
| `kinds`      | `id` (uuid)  | Genres (action, romance...)          |
| `publishers` | `id` (uuid)  | Éditeurs                             |
| `authors`    | `id` (uuid)  | Auteurs                              |
| `jobs`       | `id` (uuid)  | Métiers (scénariste, dessinateur...) |
| `series`     | `id` (uuid)  | Séries (œuvres)                      |
| `editions`   | `id` (uuid)  | Éditions (version chez un éditeur)   |
| `volumes`    | `id` (uuid)  | Volumes (tomes individuels)          |
| `tasks`      | `id` (uuid)  | Relation auteur ↔ série ↔ métier     |

### Tables Utilisateur

| Table             | Clé primaire | Description                     |
|-------------------|--------------|--------------------------------|
| `users`           | `id` (uuid)  | Utilisateurs                   |
| `possessions`     | `id` (uuid)  | Volumes possédés               |
| `reads`           | `id` (uuid)  | Volumes lus                    |
| `follow_editions` | `id` (uuid)  | Éditions suivies               |
| `read_editions`   | `id` (uuid)  | Éditions en cours de lecture   |

### Tables Coffrets

| Table          | Clé primaire | Description                |
|----------------|--------------|----------------------------|
| `box_editions` | `id` (uuid)  | Éditions de coffrets       |
| `boxes`        | `id` (uuid)  | Coffrets individuels       |
| `box_volumes`  | `id` (uuid)  | Volumes inclus dans coffret|

### Table Données Extra (Data API)

| Table          | Clé primaire  | Description                  |
|----------------|---------------|------------------------------|
| `volume_extra` | `volume_id`   | Dimensions, poids, pages... |

### Schéma Relationnel Simplifié

```
Author ──M:N── Series ──M:N── Kind
                 │
                 │ 1:N
                 ▼
Publisher ──1:N── Edition
                    │
                    │ 1:N
                    ▼
                  Volume
                    │
     ┌──────────────┼───────────────┐
     ▼              ▼               ▼
 Possession       Read          CartItem
     │              │               │
     └──────────────┼───────────────┘
                    ▼
                  User
```
