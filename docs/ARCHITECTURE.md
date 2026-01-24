# Architecture Technique — BookSync

Application desktop de gestion de collection manga pour Raspberry Pi avec écran tactile.

> **Note** : Les informations communes avec [PRD.md](./PRD.md) sont centralisées dans [COMMON.md](./COMMON.md).

---

## Table des matières

1. [Vue d'ensemble](#1-vue-densemble)
2. [Stack technique](#2-stack-technique)
3. [Architecture déployée](#3-architecture-déployée)
4. [Architecture applicative](#4-architecture-applicative)
5. [Gestion des données](#5-gestion-des-données)
6. [Interface utilisateur](#6-interface-utilisateur)
7. [Sécurité](#7-sécurité)
8. [Configuration et déploiement](#8-configuration-et-déploiement)

---

## 1. Vue d'ensemble

### 1.1 Schéma global

> Voir [COMMON.md § Architecture déployée](./COMMON.md#1-architecture-déployée) pour le schéma complet.

### 1.2 Contraintes matérielles

> Voir [COMMON.md § Contraintes matérielles](./COMMON.md#6-contraintes-matérielles).

---

## 2. Stack technique

> Voir [COMMON.md § Stack technique](./COMMON.md#3-stack-technique) pour les tableaux complets.

### 2.1 Détails Auth API

| Fonction                | Description                                  |
|-------------------------|----------------------------------------------|
| Authentification locale | JWT (access + refresh tokens) pour l'app     |
| Credentials Mangacollec | Stockage sécurisé email/password Mangacollec |
| Multi-utilisateur       | Profils familiaux avec sessions séparées     |
| Proxy auth Mangacollec  | Obtention des tokens Mangacollec via OAuth2  |

### 2.2 Détails Data API — Table `volume_extra`

| Champ        | Type       | Description                |
|--------------|------------|----------------------------|
| `volume_id`  | UUID       | FK → volumes (Mangacollec) |
| `object_id`  | String(50) | ID BubbleBD                |
| `nb_pages`   | Integer    | Nombre de pages            |
| `length`     | String(20) | Longueur (cm)              |
| `height`     | String(20) | Hauteur (cm)               |
| `width`      | String(20) | Largeur (cm)               |
| `weight`     | String(20) | Poids (g)                  |
| `extra_info` | Text       | Infos supplémentaires      |

> Ces données sont absentes de l'API Mangacollec et récupérées par scraping (ex: BubbleBD).

### 2.3 API Mangacollec — Endpoints

> Voir [COMMON.md § Sources de données](./COMMON.md#2-sources-de-données) pour le détail complet des endpoints.

---

## 3. Architecture déployée

### 3.1 Raspberry Pi (Client)

```
/opt/booksync/
├── app/                    # Application PySide6
├── cache/
│   ├── booksync.db        # SQLite (cache données)
│   └── images/            # Couvertures manga
├── config/
│   └── settings.toml      # Configuration
└── logs/
    └── app.log
```

### 3.2 Serveur/NAS (Backend)

```
/srv/booksync/
├── docker-compose.yml
├── auth-api/
│   └── Dockerfile
├── data-api/
│   └── Dockerfile
├── prediction-api/            # V3 - Recommandations
│   └── Dockerfile
├── scraping/
│   └── Dockerfile
├── postgres/
│   └── data/                  # Volumes PostgreSQL (+ pgvector)
└── nginx/                     # Reverse proxy (optionnel)
```

### 3.3 Mode de fonctionnement

| Mode        | Réseau              | Lecture          | Écriture           |
|-------------|---------------------|------------------|--------------------|
| **Online**  | Connecté au serveur | Cache + API      | Via API uniquement |
| **Offline** | Déconnecté          | Cache local seul | **Désactivé**      |

> **Note** : Le mode offline est en lecture seule. Toutes les modifications nécessitent une connexion au serveur.

---

## 4. Architecture applicative

### 4.1 Clean Architecture (Hexagonale)

> Voir [COMMON.md § Clean Architecture](./COMMON.md#8-clean-architecture) pour le schéma complet.

### 4.2 Structure du projet frontend

```
booksync_app_qt/
├── pyproject.toml
├── src/booksync_app_qt/
│   ├── __init__.py
│   ├── __main__.py              # Entry point
│   ├── app.py                   # QApplication setup
│   │
│   ├── domain/                  # Coeur métier (aucune dépendance)
│   │   ├── entities/
│   │   │   ├── volume.py
│   │   │   ├── series.py
│   │   │   ├── edition.py
│   │   │   ├── publisher.py
│   │   │   ├── author.py
│   │   │   ├── user.py
│   │   │   ├── possession.py
│   │   │   └── ...
│   │   ├── exceptions/
│   │   │   ├── domain_error.py
│   │   │   └── ...
│   │   └── ports/               # Interfaces abstraites
│   │       ├── volume_repository.py
│   │       ├── api_client.py
│   │       ├── image_cache.py
│   │       └── ...
│   │
│   ├── application/             # Cas d'usage
│   │   ├── services/
│   │   │   ├── collection_service.py
│   │   │   ├── reading_service.py
│   │   │   ├── search_service.py
│   │   │   ├── planning_service.py
│   │   │   ├── sync_service.py
│   │   │   ├── prediction_service.py  # V3
│   │   │   └── ...
│   │   └── dtos/
│   │       ├── volume_dto.py
│   │       └── ...
│   │
│   ├── infrastructure/          # Implémentations concrètes
│   │   ├── api/
│   │   │   ├── http_client.py      # Client httpx
│   │   │   ├── mangacollec_api.py  # API externe (catalogue)
│   │   │   ├── auth_api.py         # API locale (auth + credentials)
│   │   │   ├── data_api.py         # API locale (volume_extra)
│   │   │   └── prediction_api.py   # API locale V3 (recommandations)
│   │   ├── cache/
│   │   │   ├── database.py      # SQLite connection
│   │   │   ├── models/          # SQLAlchemy models
│   │   │   └── repositories/
│   │   │       ├── volume_cache.py
│   │   │       ├── series_cache.py
│   │   │       └── ...
│   │   ├── images/
│   │   │   └── image_cache.py   # Cache couvertures
│   │   └── scanner/
│   │       └── barcode_scanner.py
│   │
│   ├── presentation/            # Interface Qt
│   │   ├── controllers/         # QObject exposés au QML
│   │   │   ├── base_controller.py
│   │   │   ├── collection_controller.py
│   │   │   ├── search_controller.py
│   │   │   ├── planning_controller.py
│   │   │   ├── prediction_controller.py  # V3
│   │   │   └── ...
│   │   └── models/              # QAbstractListModel
│   │       ├── volume_list_model.py
│   │       ├── series_list_model.py
│   │       └── ...
│   │
│   ├── qml/                     # Interface utilisateur
│   │   ├── main.qml
│   │   ├── Theme.qml
│   │   ├── components/
│   │   │   ├── cards/
│   │   │   ├── badges/
│   │   │   ├── inputs/
│   │   │   └── ...
│   │   ├── layouts/
│   │   │   ├── MainLayout.qml
│   │   │   ├── SideBar.qml
│   │   │   └── ...
│   │   └── pages/
│   │       ├── home/
│   │       ├── news/
│   │       ├── collection/
│   │       ├── planning/
│   │       ├── search/
│   │       ├── cart/
│   │       ├── settings/
│   │       ├── prediction/     # V3
│   │       └── catalogue/
│   │
│   └── utils/
│       ├── config.py
│       ├── logger.py
│       └── constants.py
│
└── tests/
    ├── unit/
    ├── integration/
    └── conftest.py
```

### 4.3 Flux de données

> Voir [COMMON.md § Flux de données](./COMMON.md#10-flux-de-données) pour le schéma complet.

---

## 5. Gestion des données

### 5.1 Sources de données

```
┌──────────────────────────────────────────────────────────────────────┐
│                      ARCHITECTURE DES DONNÉES                        │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│                      ┌─────────────────────────┐                     │
│                      │    BookSync App Qt      │                     │
│                      │    (Raspberry Pi)       │                     │
│                      └───────────┬─────────────┘                     │
│                                  │                                   │
│              ┌───────────────────┼─────────────────────┐             │
│              │                   │                     │             │
│              ▼                   ▼                     ▼             │
│   ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐   │
│   │  API Mangacollec │  │  Auth API        │  │  Data API        │   │
│   │  (Internet)      │  │  (Serveur local) │  │  (Serveur local) │   │
│   └────────┬─────────┘  └────────┬─────────┘  └────────┬─────────┘   │
│            │                     │                     │             │
│            ▼                     ▼                     ▼             │
│   ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐   │
│   │ • Catalogue      │  │ • Auth locale    │  │ • volume_extra   │   │
│   │ • Séries         │  │   (JWT)          │  │   - dimensions   │   │
│   │ • Éditions       │  │                  │  │   - Etc ...      │   │
│   │ • Volumes        │  │ • Credentials    │  │                  │   │
│   │ • Planning       │  │   Mangacollec    │  │ • Embeddings V3  │   │
│   │ • Collection     │  │   (email/pwd)    │  │   (predictions)  │   │
│   │ • Possessions    │  │                  │  │                  │   │
│   │ • Lectures       │  │ • Multi-user     │  │ Alimenté par     │   │
│   │ • Ect ...        │  │   (profils)      │  │ Scraping (Scrapy)│   │
│   └──────────────────┘  └──────────────────┘  └──────────────────┘   │
│            │                     │                     │             │
│            └─────────────────────┼─────────────────────┘             │
│                                  │                                   │
│                                  ▼                                   │
│                       ┌──────────────────────┐                       │
│                       │  Cache SQLite local  │                       │
│                       │  + Images couvertures│                       │
│                       │  (Mode offline: R/O) │                       │
│                       └──────────────────────┘                       │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

**Répartition des responsabilités :**

| Source              | Responsabilité                                                 | Connexion            |
|---------------------|----------------------------------------------------------------|----------------------|
| **API Mangacollec** | Catalogue complet, collection, planning, possessions, lectures | Directe depuis l'app |
| **Auth API locale** | JWT local, credentials Mangacollec, profils multi-utilisateur  | Serveur/NAS          |
| **Data API locale** | volume_extra (dimensions, poids, pages) via scraping           | Serveur/NAS          |
| **Prediction API**  | Recommandations de lecture (V3, embeddings pgvector)           | Serveur/NAS          |
| **Cache SQLite**    | Performance + mode offline (lecture seule)                     | Local RPi            |

### 5.2 Stratégie de synchronisation

```
┌─────────────────────────────────────────────────────────────────────┐
│                     FLUX D'ÉCRITURE (Online requis)                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   1. Action utilisateur (ex: ajouter un tome)                       │
│                        │                                            │
│                        ▼                                            │
│   2. Controller → Service.add_volume(volume_id)                     │
│                        │                                            │
│                        ▼                                            │
│   3. Vérifier connexion ────► Si offline → Erreur "Mode lecture"    │
│                        │                                            │
│                        ▼                                            │
│   4. POST /api/possessions → API Backend                            │
│                        │                                            │
│                        ▼                                            │
│   5. Réponse OK → Mettre à jour cache local                         │
│                        │                                            │
│                        ▼                                            │
│   6. Émettre signal collectionChanged                               │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

```
┌─────────────────────────────────────────────────────────────────────┐
│                     FLUX DE LECTURE                                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   1. Afficher la collection                                         │
│                        │                                            │
│                        ▼                                            │
│   2. Service.get_collection()                                       │
│                        │                                            │
│          ┌─────────────┴─────────────┐                              │
│          ▼                           ▼                              │
│   Cache valide?              Cache expiré?                          │
│          │                           │                              │
│          ▼                           ▼                              │
│   Retourner cache           Online? ─────► Non → Retourner cache    │
│                                      │                              │
│                                      ▼ Oui                          │
│                              Fetch API + Update cache               │
│                                      │                              │
│                                      ▼                              │
│                              Retourner données                      │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 5.3 Cache local SQLite

**Durée de validité par type de données :**

| Données                     | TTL       | Justification                              |
|-----------------------------|-----------|--------------------------------------------|
| Collection utilisateur      | 5 min     | Données personnelles fréquemment modifiées |
| Catalogue (séries, volumes) | 24h       | Rarement modifié                           |
| Planning sorties            | 1h        | Mises à jour quotidiennes                  |
| Images couvertures          | Permanent | Stockage définitif                         |

### 5.4 Cache des images

```
cache/images/
├── volumes/
│   ├── {volume_id}.jpg
│   └── ...
├── series/
│   ├── {series_id}.jpg
│   └── ...
└── thumbnails/           # Versions réduites pour grilles
    ├── {volume_id}_thumb.jpg
    └── ...
```

**Stratégie de téléchargement :**
1. Au premier affichage d'une couverture
2. Téléchargement asynchrone en arrière-plan
3. Stockage local permanent
4. Placeholder pendant le chargement

---

## 6. Interface utilisateur

### 6.1 Charte graphique

> Voir [COMMON.md § Charte graphique](./COMMON.md#4-charte-graphique) pour les couleurs complètes (mode sombre et clair).

### 6.2 Composants réutilisables

```
qml/components/
├── cards/
│   ├── VolumeCard.qml      # Carte tome avec couverture
│   ├── SeriesCard.qml      # Carte série
│   ├── BoxCard.qml         # Carte coffret
│   └── StatCard.qml        # Carte statistique
├── badges/
│   ├── BadgeOwned.qml      # Indicateur "Possédé"
│   ├── BadgeRead.qml       # Indicateur "Lu"
│   ├── BadgeLast.qml       # "Dernier tome"
│   └── GenreChip.qml       # Tag genre
├── inputs/
│   ├── SearchBar.qml       # Barre de recherche
│   ├── FilterDropdown.qml  # Menu déroulant filtres
│   └── DateNavigator.qml   # Navigation temporelle
└── common/
    ├── ActionButton.qml    # Bouton action
    ├── ProgressBar.qml     # Barre de progression
    ├── LoadingSpinner.qml  # Indicateur chargement
    └── ConfirmDialog.qml   # Dialogue confirmation
```

### 6.3 Navigation

```
┌────────────────────────────────────────────────────────────────────┐
│  SideBar                    │           Contenu principal          │
│  (permanente)               │                                      │
├─────────────────────────────┼──────────────────────────────────────┤
│                             │                                      │
│  ┌─────────────────────┐    │   ┌───────────────────────────────┐  │
│  │ 🏠 Accueil          │    │   │      SubNavBar (optionnel)    │  │
│  ├─────────────────────┤    │   └───────────────────────────────┘  │
│  │ 📚 Collection       │────┼──► Pile | Coll | Compléter | ...     │
│  ├─────────────────────┤    │                                      │
│  │ 🎯 Prediction (V3)  │────┼──► Recommandation | Historique       │
│  ├─────────────────────┤    │                                      │
│  │ 📅 Planning         │────┼──► Perso | Tout | Nouveautés | ...   │
│  ├─────────────────────┤    │                                      │
│  │ 🔍 Recherche        │────┼──► Titres | Auteurs | Éditeurs       │
│  ├─────────────────────┤    │                                      │
│  │ 🛒 Panier           │    │                                      │
│  ├─────────────────────┤    │                                      │
│  │ ⚙️ Paramètres       │    │                                      │
│  └─────────────────────┘    │                                      │
│                             │                                       │
└─────────────────────────────┴───────────────────────────────────────┘
```

---

## 7. Sécurité

### 7.1 Authentification

> Voir [COMMON.md § Flux d'authentification](./COMMON.md#9-flux-dauthentification) pour les schémas complets.

**Détails d'implémentation :**

| Aspect                  | Implémentation                                     |
|-------------------------|----------------------------------------------------|
| Token local             | Sans expiration (valide tant que non révoqué)      |
| Stockage token local    | keyring (système OS sur RPi)                       |
| Tokens Mangacollec      | Demandés à Auth API avant chaque appel Mangacollec |
| Refresh automatique     | Auth API refresh le token si expiré                |
| Credentials Mangacollec | Chiffrés en BDD PostgreSQL                         |
| Hachage mots de passe   | bcrypt (côté Auth API)                             |
| Multi-utilisateur       | Profils séparés avec sessions isolées              |

### 7.2 Communication

| Aspect     | Implémentation                             |
|------------|--------------------------------------------|
| Protocole  | HTTPS (TLS 1.3)                            |
| Réseau     | LAN uniquement (pas d'exposition Internet) |
| Validation | Pydantic côté client et serveur            |

### 7.3 Données locales

| Donnée       | Protection                |
|--------------|---------------------------|
| Tokens JWT   | keyring système           |
| Cache SQLite | Permissions fichier (600) |
| Images       | Permissions fichier (644) |

---

## 8. Configuration et déploiement

### 8.1 Dépôts Git

> Voir [COMMON.md § Architecture Git](./COMMON.md#11-architecture-git) pour la structure multi-repos avec submodules.

Le projet est organisé en monorepo avec submodules :

```bash
# Cloner le projet complet
git clone --recurse-submodules git@github.com:shooterdev/booksync.git
```

### 8.2 Variables d'environnement

> Voir [COMMON.md § Variables d'environnement](./COMMON.md#5-variables-denvironnement) pour la liste complète.

### 8.3 Déploiement Raspberry Pi

```bash
# Cloner le dépôt
git clone https://github.com/shooterdev/booksync_app_qt.git
cd booksync/booksync_app_qt

# Installer les dépendances avec uv
uv sync

# Lancement
uv run python -m booksync_app_qt
```

### 8.4 Déploiement Backend (Docker)

> Voir le fichier [`docker_compose.yml`](../docker_compose.yml) à la racine du projet.

```bash
# Démarrage des services (Auth API + Data API)
docker-compose up -d

# Avec Prediction API (V3)
docker-compose --profile v3 up -d

# Logs
docker-compose logs -f

# Arrêt
docker-compose down
```

**Services déployés :**

| Service        | Port | Image                    |
|----------------|------|--------------------------|
| PostgreSQL 17  | 5432 | `pgvector/pgvector:pg17` |
| Auth API       | 8000 | Build local              |
| Data API       | 8001 | Build local              |
| Prediction API | 8002 | Build local (profile v3) |

### 8.5 Auto-démarrage Raspberry Pi

```bash
# /etc/systemd/system/booksync.service
[Unit]
Description=BookSync Manga Collection Manager
After=network.target

[Service]
Type=simple
User=pi
Environment=DISPLAY=:0
ExecStart=/usr/local/bin/booksync-app
Restart=on-failure

[Install]
WantedBy=graphical.target
```

---

## Annexes

### A. Dépendances Python (pyproject.toml)

```toml
[project]
name = "booksync-app-qt"
version = "0.1.0"
requires-python = ">=3.11"

dependencies = [
    "PySide6>=6.6.0",
    "httpx>=0.27.0",
    "sqlalchemy>=2.0.0",
    "aiosqlite>=0.19.0",
    "pydantic>=2.5.0",
    "pydantic-settings>=2.1.0",
    "keyring>=24.3.0",
    "python-dotenv>=1.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.4.0",
    "pytest-asyncio>=0.23.0",
    "pytest-qt>=4.2.0",
    "pytest-cov>=4.1.0",
    "ruff>=0.1.0",
    "black>=24.0.0",
    "mypy>=1.7.0",
]
```

### B. Compatibilité Raspberry Pi

| Composant   | Raspberry Pi 4   | Raspberry Pi 5    |
|-------------|------------------|-------------------|
| PySide6     | ✅ (via pip)      | ✅ (via pip)       |
| Qt 6        | ✅                | ✅                 |
| OpenGL ES   | ✅ (VideoCore VI) | ✅ (VideoCore VII) |
| SQLite      | ✅                | ✅                 |
| Python 3.11 | ✅                | ✅                 |

### C. Glossaire

> Voir [COMMON.md § Glossaire](./COMMON.md#7-glossaire).
