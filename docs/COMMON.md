# BookSync — Référence Commune

Ce fichier centralise les informations partagées entre
[PRD.md](./PRD.md) et [ARCHITECTURE.md](./ARCHITECTURE.md).

> **Règle** : Toute modification de ces sections doit être faite **uniquement** dans ce fichier.

---

## Table des matières

1. [Architecture déployée](#1-architecture-déployée)
2. [Sources de données](#2-sources-de-données)
3. [Stack technique](#3-stack-technique)
4. [Charte graphique](#4-charte-graphique)
5. [Variables d'environnement](#5-variables-denvironnement)
6. [Contraintes matérielles](#6-contraintes-matérielles)
7. [Glossaire](#7-glossaire)
8. [Clean Architecture](#8-clean-architecture)
9. [Flux d'authentification](#9-flux-dauthentification)
10. [Flux de données](#10-flux-de-données)
11. [Architecture Git](#11-architecture-git)

---

## 1. Architecture déployée

```
┌─────────────────────────────────────────────────────────────────────────────────────────────┐
│                                        INTERNET                                             │
│                          ┌───────────────────────────────────┐                              │
│                          │   API Mangacollec                 │                              │
│                          │   https://api.mangacollec.com/    │                              │
│                          │   (Source de vérité catalogue)    │                              │
│                          └───────────────┬───────────────────┘                              │
└──────────────────────────────────────────┼──────────────────────────────────────────────────┘
                                           │ HTTPS (connexion directe)
                                           │
┌──────────────────────────────────────────┼──────────────────────────────────────────────────┐
│ RÉSEAU LOCAL                             │                                                  │
├──────────────────────────────────────────┼──────────────────────────────────────────────────┤
│                                          │                                                  │
│   ┌──────────────────────────────────────┼────────┐     ┌─────────────────────────────────┐ │
│   │      RASPBERRY PI (Frontend)         │        │     │        SERVEUR / NAS            │ │
│   │                                      ▼        │     │        (Services locaux)        │ │
│   │  ┌───────────────────────────────────────┐    │     │                                 │ │
│   │  │         BookSync App Qt               │    │     │  ┌─────────────────────────┐    │ │
│   │  │         PySide6 / QML                 │    │ HTTP│  │  Auth API (8000)        │    │ │
│   │  │                                       │◄───┼─────┼─►│  Authentification JWT   │    │ │
│   │  │  ┌─────────────┐  ┌─────────────┐     │    │     │  └─────────────────────────┘    │ │
│   │  │  │ Mangacollec │  │  APIs       │     │    │     │                                 │ │
│   │  │  │ Client      │  │  Locales    │     │    │ HTTP│  ┌───────────────────────────┐  │ │
│   │  │  └─────────────┘  └─────────────┘     │◄───┼─────┼─►│  Data API (8001)          │  │ │
│   │  └───────────────────────────────────────┘    │     │  │  Données extra (scraping) │  │ │
│   │            │                                  │     │  └───────────────────────────┘  │ │
│   │            ▼                                  │     │            │                    │ │
│   │  ┌───────────────────────┐                    │     │            ▼                    │ │
│   │  │   Cache SQLite        │                    │     │  ┌─────────────────────────┐    │ │
│   │  │   + Images locales    │                    │     │  │  PostgreSQL 17          │    │ │
│   │  └───────────────────────┘                    │     │  │  (volume_extra, etc.)   │    │ │
│   │                                               │     │  └─────────────────────────┘    │ │
│   │  ┌───────────────────────┐                    │     │                                 │ │
│   │  │   Douchette Bluetooth │                    │     │  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐    │ │
│   │  │   (Scanner ISBN)      │                    │     │     Prediction API (8002)       │ │
│   │  └───────────────────────┘                    │     │  │  Recommandations (V3)   │    │ │
│   │                                               │     │     Embeddings pgvector         │ │
│   └───────────────────────────────────────────────┘     │  └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘    │ │
│                                                         │                                 │ │
│                                                         └─────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Sources de données

| Source              | Responsabilité                                                | Connexion            |
|---------------------|---------------------------------------------------------------|----------------------|
| **API Mangacollec** | Catalogue, séries, volumes, planning, collection              | Directe depuis l'app |
| **Auth API locale** | JWT local, credentials Mangacollec, profils multi-utilisateur | Serveur/NAS          |
| **Data API locale** | volume_extra (dimensions, poids, pages) via scraping          | Serveur/NAS          |
| **Prediction API**  | Recommandations de lecture (V3, embeddings pgvector)          | Serveur/NAS          |
| **Cache SQLite**    | Performance + mode offline (lecture seule)                    | Local RPi            |

### API Mangacollec (externe)

| Aspect           | Valeur                                                                            |
|------------------|-----------------------------------------------------------------------------------|
| URL              | `https://api.mangacollec.com/`                                                    |
| Rôle             | **Source de vérité** pour le catalogue et la collection                           |
| Authentification | OAuth2 (Client ID + Secret)                                                       |
| Données fournies | Séries, éditions, volumes, auteurs, éditeurs, planning, possessions, Read, etc... |

#### Endpoints par page (authentifié)

Les tableaux suivants détaillent les endpoints API Mangacollec appelés par chaque page de l'application.

> **Note** : Les endpoints communs à toutes les pages authentifiées sont :
> - `/v1/users/me` — Informations utilisateur courant
> - `/v1/users/me/cart` — Panier utilisateur
> - `/v1/users/me/recommendation` — Recommandations utilisateur

##### Page News

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/volumes/news`           | Dernières sorties        |
| `GET /v2/publishers`             | Liste des éditeurs       |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

##### Page Collection

**Section Pile à lire / Collection / Compléter / Envies :**

| Endpoint                      | Description              |
|-------------------------------|--------------------------|
| `GET /v2/users/me/collection` | Collection utilisateur   |
| `GET /v2/publishers`          | Liste des éditeurs       |
| `GET /v2/user/{username}`     | Profil utilisateur       |

**Section Prêts (Loans) :**

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/users/me/collection`    | Collection utilisateur   |

**Section Statistiques (tous onglets) :**

| Endpoint                      | Description              |
|-------------------------------|--------------------------|
| `GET /v2/publishers`          | Liste des éditeurs       |
| `GET /v2/user/{username}`     | Profil utilisateur       |
| `GET /v2/users/me/collection` | Collection utilisateur   |

##### Page Planning

**Section Personnalisé :**

| Endpoint                                    | Description                 |
|---------------------------------------------|-----------------------------|
| `GET /v2/users/me/collection`               | Collection utilisateur      |
| `GET /v2/publishers`                        | Liste des éditeurs          |
| `GET /v2/users/me/ad_native_planning_perso` | Planning personnalisé natif |

**Section Tout / Nouveautés / Coffrets :**

| Endpoint                         | Description                              |
|----------------------------------|------------------------------------------|
| `GET /v2/planning?month=YYYY-MM` | Planning du mois (paramètre obligatoire) |
| `GET /v2/users/me/collection`    | Collection utilisateur                   |
| `GET /v2/publishers`             | Liste des éditeurs (section Tout)        |

##### Page Recherche

**Section Titres :**

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/series`                 | Recherche de séries      |
| `GET /v2/kinds`                  | Genres disponibles       |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

**Section Auteurs :**

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/authors`                | Recherche d'auteurs      |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

**Section Éditeurs :**

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/publishers`             | Recherche d'éditeurs     |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

##### Page Panier

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v1/users/me/cart`          | Panier utilisateur       |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

##### Page Volume (détail)

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/volumes/{id_volume}`    | Détail du volume         |
| `GET /v2/editions/{id_edition}`  | Édition associée         |
| `GET /v1/bdfugue_offer/{isbn}`   | Offre BDfugue            |
| `GET /v1/amazon_offer/{asin}`    | Offre Amazon             |
| `GET /v1/img_offer/{isbn}`       | Offre IMG                |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

##### Page Série (détail)

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/series/{id_serie}`      | Détail de la série       |
| `GET /v2/kinds`                  | Genres de la série       |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

##### Page Édition (détail)

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/editions/{id_edition}`  | Détail de l'édition      |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

##### Page Auteur (détail)

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/authors/{id_author}`    | Détail de l'auteur       |
| `GET /v2/users/me/collection`    | Collection utilisateur   |

##### Page Éditeur (détail)

| Endpoint                            | Description            |
|-------------------------------------|------------------------|
| `GET /v2/publishers/{id_publisher}` | Détail de l'éditeur    |
| `GET /v2/users/me/collection`       | Collection utilisateur |

##### Page Paramètres (Settings)

| Endpoint                      | Description              |
|-------------------------------|--------------------------|
| `GET /v2/user/{username}`     | Profil utilisateur       |
| `GET /v2/users/me/collection` | Collection utilisateur   |

### APIs locales (Serveur/NAS)

| Service          | Port | Description                               |
|------------------|------|-------------------------------------------|
| Auth API         | 8000 | Auth locale + credentials Mangacollec     |
| Data API         | 8001 | Données extra (volume_extra via scraping) |
| Prediction API   | 8002 | Recommandations V3 (embeddings pgvector)  |
| PostgreSQL 17    | 5432 | users, volume_extra, volume_embeddings    |

### Structure des projets

> **Règle obligatoire** : Chaque service doit respecter la structure de dossiers suivante.

| Service        | Dossier                    | Description                               |
|----------------|----------------------------|-------------------------------------------|
| Auth API       | `booksync_api_auth/`       | Service d'authentification (port 8000)    |
| Data API       | `booksync_api_data/`       | Service de données (port 8001)            |
| Prediction API | `booksync_api_prediction/` | Service de recommandations V3 (port 8002) |
| App Frontend   | `booksync_app_qt/`         | Application PySide6/QML                   |

---

## 3. Stack technique

### Frontend (Raspberry Pi)

| Couche       | Technologie         | Version | Justification                                 |
|--------------|---------------------|---------|-----------------------------------------------|
| Framework UI | **PySide6**         | 6.6+    | Binding Python officiel Qt, performances GPU  |
| Langage UI   | **QML**             | Qt 6    | Interface déclarative, animations fluides     |
| Composants   | Qt Quick Controls 2 | -       | Optimisés tactile                             |
| Graphiques   | Qt Charts           | -       | Statistiques collection                       |
| HTTP Client  | httpx               | 0.27+   | Async, moderne                                |
| Cache local  | SQLite              | 3.x     | Léger, embarqué                               |
| ORM          | SQLAlchemy          | 2.0+    | Async support                                 |
| Validation   | Pydantic            | 2.x     | DTOs, settings                                |
| Credentials  | keyring             | 24+     | Stockage sécurisé tokens                      |

### Backend (Serveur/NAS)

| Service          | Technologie    | Port | Description                               |
|------------------|----------------|------|-------------------------------------------|
| Auth API         | FastAPI        | 8000 | Auth locale + credentials Mangacollec     |
| Data API         | FastAPI        | 8001 | Données extra (volume_extra via scraping) |
| Prediction API   | FastAPI        | 8002 | Recommandations V3 (embeddings pgvector)  |
| Base de données  | PostgreSQL 17  | 5432 | users, volume_extra, volume_embeddings    |
| Scraping         | Scrapy         | -    | Alimentation volume_extra                 |
| Containerisation | Docker-Compose | -    | Déploiement simplifié                     |

### Outils de développement

| Outil          | Usage                                   |
|----------------|-----------------------------------------|
| **uv**         | Gestionnaire de packages (ultra-rapide) |
| **pytest**     | Tests unitaires et intégration          |
| **pytest-qt**  | Tests interface Qt                      |
| **ruff**       | Linting rapide                          |
| **black**      | Formatage code                          |
| **mypy**       | Vérification types                      |
| **pre-commit** | Hooks Git                               |

---

## 4. Charte graphique

### Mode sombre (Dark Mode) — Principal

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

### Mode clair (Light Mode)

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

---

## 5. Variables d'environnement

```bash
# API Mangacollec (source de vérité catalogue)
MANGACOLLEC_API_URL=https://api.mangacollec.com
MANGACOLLEC_CLIENT_ID=38fee110b53a75af6cc72f6fb66fa504fc6241e566788f4b2f5b21c25ba2fefb
MANGACOLLEC_CLIENT_SECRET=060658630f7d199c19ab1cf34ed4e50935c748267e318a24e048d6ab45871da2
MANGACOLLEC_API_TIMEOUT=30

# Serveur Backend local
BOOKSYNC_AUTH_API_URL=http://192.168.1.100:8000
BOOKSYNC_DATA_API_URL=http://192.168.1.100:8001
BOOKSYNC_PREDICTION_API_URL=http://192.168.1.100:8002  # V3

# Cache local
BOOKSYNC_CACHE_DIR=~/.local/share/booksync
BOOKSYNC_CACHE_TTL_COLLECTION=300      # 5 minutes
BOOKSYNC_CACHE_TTL_CATALOG=86400       # 24 heures

# Logging
BOOKSYNC_LOG_LEVEL=INFO
BOOKSYNC_LOG_FILE=~/.local/share/booksync/app.log

# UI
BOOKSYNC_THEME=dark                    # dark | light
BOOKSYNC_FULLSCREEN=true

# Debug
DEBUG=false
```

---

## 6. Contraintes matérielles

| Composant | Spécification                            |
|-----------|------------------------------------------|
| Machine   | Raspberry Pi 4/5                         |
| Écran     | Tactile 1280 × 720 pixels                |
| RAM       | 2-4 GB                                   |
| Stockage  | SD Card / SSD USB                        |
| Scanner   | Douchette Bluetooth (simulation clavier) |
| Réseau    | Connexion LAN vers serveur/NAS           |

---

## 7. Glossaire

| Terme          | Définition                               |
|----------------|------------------------------------------|
| **Volume**     | Tome individuel d'une édition            |
| **Série**      | Œuvre complète (ex: "One Piece")         |
| **Édition**    | Version publiée chez un éditeur          |
| **Possession** | Relation utilisateur ↔ volume possédé    |
| **Cache**      | Base SQLite locale pour accès rapide     |
| **Controller** | QObject exposant des méthodes au QML     |
| **Port**       | Interface abstraite (Clean Architecture) |

---

## 8. Clean Architecture

L'application suit une architecture hexagonale stricte où les dépendances pointent vers l'intérieur :

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PRESENTATION                                │
│                    (QML + Controllers)                              │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         APPLICATION                                 │
│                    (Services / Use Cases)                           │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                           DOMAIN                                    │
│              (Entities + Ports / Interfaces)                        │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────────┐
│                       INFRASTRUCTURE                                │
│          (API Client, Cache SQLite, Scanner, etc.)                  │
└─────────────────────────────────────────────────────────────────────┘
```

**Règle d'or** : Le Domain ne dépend de rien. Les dépendances pointent toujours vers l'intérieur.

---

## 9. Flux d'authentification

### 9.1 Flux de login

```
┌─────────────────┐      Login       ┌─────────────────┐
│   App Qt        │─────────────────►│   Auth API      │
│   (RPi)         │                  │   (Serveur)     │
│                 │◄─────────────────│                 │
│                 │   Token local    │                 │
│                 │   (sans expir.)  │                 │
└─────────────────┘                  └────────┬────────┘
                                              │
                                              │ Gère en interne
                                              ▼
                                     ┌─────────────────┐
                                     │ Tokens          │
                                     │ Mangacollec     │
                                     │ (access+refresh)│
                                     └─────────────────┘
```

### 9.2 Flux d'appel API Mangacollec

```
┌─────────────────┐  1. Demande token  ┌─────────────────┐
│   App Qt        │───────────────────►│   Auth API      │
│                 │                    │                 │
│                 │  2. Token valide   │  - Vérifie      │
│                 │◄───────────────────│  - Refresh si   │
│                 │     Mangacollec    │    expiré       │
│                 │                    │                 │
└────────┬────────┘                    └─────────────────┘
         │
         │ 3. Appel avec token
         ▼
┌─────────────────┐
│ API Mangacollec │
│   (Internet)    │
└─────────────────┘
```

### 9.3 Niveaux de gestion des tokens

| Niveau                 | Gestion  | Description                                |
|------------------------|----------|--------------------------------------------|
| **Token local**        | App Qt   | Token sans expiration, stocké dans keyring |
| **Tokens Mangacollec** | Auth API | Access + refresh tokens gérés côté serveur |

---

## 10. Flux de données

```
┌──────────┐    Signal    ┌────────────┐         Async   ┌──────────┐
│   QML    │─────────────►│ Controller │────────────────►│ Service  │
│  (View)  │              │  (QObject) │                 │          │
└──────────┘              └────────────┘                 └────┬─────┘
     ▲                          │                             │
     │                          │              ┌──────────────┼─────────────┼──────────────┐
     │                          ▼              ▼              ▼             ▼              ▼
     │                    ┌───────────┐ ┌─────────────┐ ┌────────────┐ ┌──────────┐ ┌──────────────┐
     │                    │ ListModel │ │ Mangacollec │ │ Auth API   │ │ Data API │ │Prediction API│
     │                    │(QAbstract)│ │    API      │ │  (local)   │ │ (local)  │ │  (V3 local)  │
     │                    └───────────┘ └─────────────┘ └────────────┘ └──────────┘ └──────────────┘
     │                          │              │               │
     │                          │              │               │
     │                          ▼              ▼               │
     │                    ┌───────────┐ ┌─────────────┐        │
     └────────────────────│  Cache    │ │   Images    │        │
         Binding          │ (SQLite)  │ │   (local)   │        │
                          └───────────┘ └─────────────┘        │
                                                               │
                                                    ┌──────────┴──────────┐
                                                    │ Credentials         │
                                                    │ Mangacollec (OAuth) │
                                                    └─────────────────────┘
```

---

## 11. Architecture Git

### 11.1 Structure multi-repos

Le projet utilise une architecture **monorepo + submodules** pour séparer les responsabilités tout en maintenant une orchestration centralisée.

```
shooterdev/booksync                    # Monorepo principal (orchestration)
├── shooterdev/booksync-api-auth       # Submodule → Service d'authentification
├── shooterdev/booksync-api-data       # Submodule → Service de données extra
├── shooterdev/booksync-api-prediction # Submodule → Service de recommandations V3
└── shooterdev/booksync-app-qt         # Submodule → Application desktop PySide6/QML
```

### 11.2 Correspondance repos ↔ dossiers

| Repo GitHub                          | Dossier local              | Description                      |
|--------------------------------------|----------------------------|----------------------------------|
| `shooterdev/booksync`                | `/` (racine)               | Monorepo principal               |
| `shooterdev/booksync-api-auth`       | `booksync_api_auth/`       | Auth API - JWT & OAuth2 (8000)   |
| `shooterdev/booksync-api-data`       | `booksync_api_data/`       | Data API - Données extra (8001)  |
| `shooterdev/booksync-api-prediction` | `booksync_api_prediction/` | Prediction API - Reco V3 (8002)  |
| `shooterdev/booksync-app-qt`         | `booksync_app_qt/`         | App desktop PySide6/QML          |

### 11.3 Contenu du monorepo

Le monorepo principal contient uniquement les fichiers d'orchestration et de documentation :

```
booksync/
├── .gitmodules                    # Configuration des submodules
├── docker_compose.yml             # Orchestration Docker de tous les services
├── docs/
│   ├── PRD.md                     # Spécification produit
│   ├── ARCHITECTURE.md            # Architecture technique
│   └── COMMON.md                  # Référence commune
├── .env.example                   # Variables d'environnement globales
├── CLAUDE.md                      # Configuration Claude Code
├── .claude/                       # Prompts et configuration Claude
├── openspec/                      # Spécifications OpenAPI
│
├── booksync_api_auth/             # Submodule
├── booksync_api_data/             # Submodule
├── booksync_api_prediction/       # Submodule
└── booksync_app_qt/               # Submodule
```

### 11.4 Commandes Git essentielles

**Cloner le projet complet :**

```bash
# Clone avec tous les submodules
git clone --recurse-submodules git@github.com:shooterdev/booksync.git

# Ou après un clone simple
git submodule update --init --recursive
```

**Mettre à jour les submodules :**

```bash
# Mettre à jour tous les submodules vers leur dernière version
git submodule update --remote --merge

# Mettre à jour un submodule spécifique
git submodule update --remote booksync_api_auth
```

**Travailler dans un submodule :**

```bash
# Naviguer dans le submodule
cd booksync_api_auth

# Les commits sont indépendants du monorepo
git add .
git commit -m "feat: nouvelle fonctionnalité"
git push

# Revenir au monorepo et enregistrer la nouvelle référence
cd ..
git add booksync_api_auth
git commit -m "chore: update booksync-api-auth submodule"
git push
```

### 11.5 CI/CD

Chaque service peut avoir son propre pipeline CI/CD dans son repo :

| Repo                       | CI/CD                                    |
|----------------------------|------------------------------------------|
| `booksync-api-auth`        | Tests unitaires, lint, build Docker      |
| `booksync-api-data`        | Tests unitaires, lint, build Docker      |
| `booksync-api-prediction`  | Tests unitaires, lint, build Docker      |
| `booksync-app-qt`          | Tests unitaires, lint, build PyInstaller |
| `booksync` (monorepo)      | Tests d'intégration, orchestration       |
