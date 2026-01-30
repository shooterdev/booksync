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
6. [Contraintes matérielles](#6-contraintes-matérielles-app-qt)
7. [Glossaire](#7-glossaire)
8. [Architecture Hybride Clean + MVVM](#8-architecture-hybride-clean--mvvm)
9. [Flux d'authentification](#9-flux-dauthentification)
10. [Flux de données](#10-flux-de-données)
11. [Architecture Git](#11-architecture-git)
12. [Structure des écrans QML](#12-structure-des-écrans-qml)
13. [Navigation](#13-navigation)
14. [Configuration du projet (pyproject.toml)](#14-configuration-du-projet-pyprojecttoml)

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
│   │  │                                       │◄───┼─────┼─►│  Multi-user + sessions  │    │ │
│   │  │  ┌─────────────┐  ┌─────────────┐     │    │     │  └─────────────────────────┘    │ │
│   │  │  │ Mangacollec │  │  APIs       │     │    │     │                                 │ │
│   │  │  │ Client      │  │  Locales    │     │    │ HTTP│  ┌───────────────────────────┐  │ │
│   │  │  │ (OAuth2)    │  │  (Token)    │     │◄───┼─────┼─►│  Data API (8001)          │  │ │
│   │  │  └─────────────┘  └─────────────┘     │    │     │  │  Données extra (scraping) │  │ │
│   │  └───────────────────────────────────────┘    │     │  └───────────────────────────┘  │ │
│   │            │                                  │     │            │                    │ │
│   │            ▼                                  │     │            ▼                    │ │
│   │  ┌───────────────────────┐                    │     │  ┌─────────────────────────┐    │ │
│   │  │   Cache TinyDB        │                    │     │  │  PostgreSQL 17          │    │ │
│   │  │   + Images locales    │                    │     │  │  (users, volume_extra,  │    │ │
│   │  └───────────────────────┘                    │     │  │   embeddings)           │    │ │
│   │                                               │     │  └─────────────────────────┘    │ │
│   │  ┌───────────────────────┐                    │     │                                 │ │
│   │  │   Douchette Bluetooth │                    │     │  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐    │ │
│   │  │   + Webcam (Scanner)  │                    │     │     Prediction API (8002)       │ │
│   │  └───────────────────────┘                    │     │  │  Recommandations (V3)   │    │ │
│   │                                               │     │     Embeddings pgvector         │ │
│   └───────────────────────────────────────────────┘     │  └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘    │ │
│                                                         │                                 │ │
│                                                         └─────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Sources de données

| Source              | Responsabilité                                       | Connexion                   |
|---------------------|------------------------------------------------------|-----------------------------|
| **API Mangacollec** | Catalogue, séries, volumes, planning, collection     | OAuth2 directe depuis l'app |
| **Auth API locale** | Multi-utilisateur, sessions, profils familiaux       | Serveur                     |
| **Data API locale** | volume_extra (dimensions, poids, pages) via scraping | Serveur                     |
| **Prediction API**  | Recommandations de lecture (V3, embeddings pgvector) | Serveur                     |
| **Cache TinyDB**    | Performance + mode offline (lecture seule)           | Local RPi                   |

### API Mangacollec (externe)

| Aspect           | Valeur                                                                            |
|------------------|-----------------------------------------------------------------------------------|
| URL              | `https://api.mangacollec.com/`                                                    |
| Rôle             | **Source de vérité** pour le catalogue et la collection                           |
| Authentification | OAuth2 (grant_type: password + refresh token) — connexion directe depuis l'app    |
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
| `GET /v1/users/me`            | Profil utilisateur       |

**Section Prêts (Loans) :**

| Endpoint                         | Description              |
|----------------------------------|--------------------------|
| `GET /v2/users/me/collection`    | Collection utilisateur   |

**Section Statistiques (tous onglets) :**

| Endpoint                      | Description              |
|-------------------------------|--------------------------|
| `GET /v2/publishers`          | Liste des éditeurs       |
| `GET /v1/users/me`            | Profil utilisateur       |
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
| `GET /v2/planning?month=YYYY-MM` | Planning du mois (paramètre optionnel)   |
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
| `GET /v1/users/me`            | Profil utilisateur       |
| `GET /v2/users/me/collection` | Collection utilisateur   |

### APIs locales (Serveur)

| Service        | Port | Description                               |
|----------------|------|-------------------------------------------|
| Auth API       | 8000 | Multi-utilisateur, sessions               |
| Data API       | 8001 | Données extra (volume_extra via scraping) |
| Prediction API | 8002 | Recommandations V3 (embeddings pgvector)  |
| PostgreSQL 17  | 5432 | users, volume_extra, volume_embeddings    |

### Structure des projets

> **Règle obligatoire** : Chaque service doit respecter la structure de dossiers suivante.

| Service        | Dossier                    | Description                               |
|----------------|----------------------------|-------------------------------------------|
| Auth API       | `booksync_api_auth/`       | Multi-utilisateur, sessions (port 8000)   |
| Data API       | `booksync_api_data/`       | Données extra volumes (port 8001)         |
| Prediction API | `booksync_api_prediction/` | Service de recommandations V3 (port 8002) |
| App Frontend   | `booksync_app_qt/`         | Application PySide6/QML                   |

---

## 3. Stack technique

### Frontend (Raspberry Pi)

| Couche       | Technologie         | Version | Justification                                |
|--------------|---------------------|---------|----------------------------------------------|
| Framework UI | **PySide6**         | 6.6+    | Binding Python officiel Qt, performances GPU |
| Langage UI   | **QML**             | Qt 6    | Interface déclarative, animations fluides    |
| Composants   | Qt Quick Controls 2 | -       | Optimisés tactile                            |
| Graphiques   | Qt Charts           | -       | Statistiques collection                      |
| HTTP Client  | httpx               | 0.27+   | Async, moderne                               |
| Cache local  | TinyDB              | 4.x     | Fichier JSON, léger, schemaless              |
| Cache temp.  | diskcache           | 5.x     | Cache disque performant                      |
| Validation   | Pydantic            | 2.x     | DTOs, settings                               |
| Credentials  | keyring             | 24+     | Stockage sécurisé tokens                     |

### Backend (Serveur)

| Service          | Technologie    | Port | Description                               |
|------------------|----------------|------|-------------------------------------------|
| Auth API         | FastAPI        | 8000 | Multi-utilisateur, sessions               |
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

### Tokens sémantiques détaillés

Les tokens suivants complètent les variables de base pour un theming cohérent.

> **Note d'implémentation** : Tous les colors doivent être centralisés dans `Colors.qml` (singleton QML).
> **Note d'implémentation** : Tous les fonts doivent être centralisés dans `Fonts.qml` (singleton QML).

#### Brand

| Token         | Light     | Dark      | Usage                   |
|---------------|-----------|-----------|-------------------------|
| `brand-vivid` | `#E31E24` | `#FF453A` | Accent marque principal |

#### Texte

| Token           | Light     | Dark      | Usage                         |
|-----------------|-----------|-----------|-------------------------------|
| `text-heading`  | `#1c1c1e` | `#FFFFFF` | Titres et en-têtes            |
| `text-on-brand` | `#FFFFFF` | `#FFFFFF` | Texte sur fond brand          |
| `text-subtle`   | `#8e8e93` | `#8e8e93` | Texte tertiaire, placeholders |
| `text-light`    | `#AAAAAA` | `#666666` | Texte désactivé               |

#### Fond

| Token            | Light     | Dark      | Usage                     |
|------------------|-----------|-----------|---------------------------|
| `bg-input`       | `#F2F2F7` | `#1C1C1E` | Champs de saisie          |
| `bg-input-light` | `#E5E5EA` | `#2C2C2E` | Champs de saisie (focus)  |
| `bg-tag`         | `#F0F0F0` | `#2C2C2E` | Tags et chips             |
| `bg-hover`       | `#F5F5F5` | `#1A1A1A` | Survol d'éléments         |
| `bg-section`     | `#F9F9F9` | `#0A0A0A` | Fond de sections          |

#### Accent

| Token               | Light     | Dark      | Usage                    |
|---------------------|-----------|-----------|--------------------------|
| `accent-blue-stats` | `#007AFF` | `#0A84FF` | Statistiques, graphiques |
| `accent-gold`       | `#FFD700` | `#FFD60A` | Récompenses, favoris     |

#### Séparateurs & Bordures

| Token             | Light                | Dark                    | Usage                 |
|-------------------|----------------------|-------------------------|-----------------------|
| `separator`       | `#C6C6C8`            | `#38383A`               | Séparateur principal  |
| `separator-light` | `#E0E0E0`            | `#2C2C2E`               | Séparateur léger      |
| `border-gray`     | `#D1D1D6`            | `#48484A`               | Bordure de composants |
| `chevron`         | `#C7C7CC`            | `#48484A`               | Icônes de navigation  |
| `disabled`        | `rgba(0, 0, 0, 0.3)` | `rgba(255,255,255,0.3)` | Éléments désactivés   |

#### Ombres

| Token            | Valeur                           | Usage              |
|------------------|----------------------------------|--------------------|
| `shadow-card`    | `0 2px 8px rgba(0, 0, 0, 0.1)`   | Ombre des cartes   |
| `shadow-button`  | `0 1px 3px rgba(0, 0, 0, 0.2)`   | Ombre des boutons  |
| `shadow-overlay` | `0 4px 16px rgba(0, 0, 0, 0.25)` | Ombre des overlays |

---

## 5. Variables d'environnement

```bash
# API Mangacollec (source de vérité catalogue)
MANGACOLLEC_API_URL=https://api.mangacollec.com
MANGACOLLEC_AUTH_API_URL=https://api.mangacollec.com/auth/token
MANGACOLLEC_CLIENT_ID=38fee110b53a75af6cc72f6fb66fa504fc6241e566788f4b2f5b21c25ba2fefb
MANGACOLLEC_CLIENT_SECRET=060658630f7d199c19ab1cf34ed4e50935c748267e318a24e048d6ab45871da2
MANGACOLLEC_API_TIMEOUT=30

# Serveur Backend local
BOOKSYNC_AUTH_API_URL=http://192.168.1.100:8000
BOOKSYNC_DATA_API_URL=http://192.168.1.100:8001
BOOKSYNC_PREDICTION_API_URL=http://192.168.1.100:8002  # V3

# Cache local
BOOKSYNC_CACHE_DIR=~/.local/share/booksync # cache directory
BOOKSYNC_STORE_DIR=~/.local/share/booksync # cache directory 
BOOKSYNC_CACHE_TTL_COLLECTION=300 # 5 minutes
BOOKSYNC_CACHE_TTL_CATALOG=86400  # 24 heures

# Logging
BOOKSYNC_LOG_LEVEL=INFO
BOOKSYNC_LOG_FILE=~/.local/share/booksync/app.log

# UI
BOOKSYNC_THEME=light # dark | light
BOOKSYNC_FULLSCREEN=true # true | false

# Debug
DEBUG=false # true | false
```

---

## 6. Contraintes matérielles App QT

| Composant | Spécification                                                           |
|-----------|-------------------------------------------------------------------------|
| Machine   | Raspberry Pi 4/5                                                        |
| Écran     | Tactile 1280 × 720 pixels                                               |
| RAM       | 2-4 GB                                                                  |
| Stockage  | SD Card / SSD USB                                                       |
| Scanner   | Douchette Bluetooth + Webcam (OpenCV/pyzbar) (planifié, non implémenté) |
| Réseau    | Connexion LAN vers serveur/NAS                                          |

---

## 7. Glossaire

| Terme             | Définition                                               |
|-------------------|----------------------------------------------------------|
| **Volume**        | Tome individuel d'une édition                            |
| **Série**         | Œuvre complète (ex: "One Piece")                         |
| **Édition**       | Version publiée chez un éditeur                          |
| **Possession**    | Relation utilisateur ↔ volume possédé                    |
| **Cache**         | Base TinyDB locale pour accès rapide                     |
| **Port**          | Interface abstraite (Clean Architecture)                 |
| **Type**          | Catégorie d'une série (manga, manhwa, manhua, etc.)      |
| **Task**          | Relation auteur ↔ série avec rôle (job)                  |
| **FollowEdition** | Suivi d'une édition par un utilisateur                   |
| **ReadEdition**   | Suivi de lecture d'une édition par un utilisateur        |
| **BoxEdition**    | Édition coffret regroupant plusieurs volumes             |
| **ViewModel**     | QObject exposant des données et actions au QML (MVVM/Qt) |

---

## 8. Architecture Hybride Clean + MVVM

L'application suit une architecture hexagonale où les dépendances pointent vers l'intérieur, 
avec des **ViewModels** (QObject) pour la couche présentation Qt/QML :

```
┌─────────────────────────────────────────────────────────────────────┐
│                         PRESENTATION                                │
│                      (QML + ViewModels)                             │
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
│          (API Client, Cache TinyDB, Scanner, etc...)                │
└─────────────────────────────────────────────────────────────────────┘
```

**Règle d'or** : Le Domain ne dépend de rien. Les dépendances pointent toujours vers l'intérieur.

---

## 9. Flux d'authentification

### 9.1 Flux de login

```
┌─────────────────┐  OAuth2 (password grant)       ┌──────────────────────┐
│   App Qt        │───────────────────────────────►│  API Mangacollec     │
│   (RPi)         │                                │  (Internet)          │
│                 │◄───────────────────────────────│                      │
│                 │  access_token + refresh_token  │                      │
│                 │                                │                      │
└────────┬────────┘                                └──────────────────────┘
         │
         │ Stockage sécurisé
         ▼
┌─────────────────┐
│ keyring (OS)    │
│ access_token    │
│ refresh_token   │
│ credentials     │
└─────────────────┘
```

### 9.2 Flux d'appel API Mangacollec (OAuth2 direct)

```
┌─────────────────┐  1. Auth OAuth2 (password grant)     ┌─────────────────┐
│   App Qt        │─────────────────────────────────────►│ API Mangacollec │
│                 │                                      │   (Internet)    │
│                 │  2. Access + Refresh tokens          │                 │
│                 │◄─────────────────────────────────────│                 │
│                 │                                      │                 │
│                 │  3. Appels API avec access token     │                 │
│                 │─────────────────────────────────────►│                 │
│                 │                                      │                 │
│                 │  4. refresh automatique tout les 1H  │                 │
└─────────────────┘                                      └─────────────────┘
```

### 9.3 Niveaux de gestion des tokens

| Niveau                 | Gestion  | Description                                          |
|------------------------|----------|------------------------------------------------------|
| **Access token**       | App Qt   | Token Mangacollec court terme, stocké dans keyring   |
| **Refresh token**      | App Qt   | Token Mangacollec longue durée, stocké dans keyring  |
| **Credentials**        | keyring  | Email/password Mangacollec pour auto-login           |

---

## 10. Flux de données

```
┌──────────┐    Signal    ┌────────────┐         Async   ┌──────────┐
│   QML    │─────────────►│ ViewModel  │────────────────►│ Services │
│  (View)  │              │ (QObject)  │                 │          │
└──────────┘              └────────────┘                 └────┬─────┘
     ▲                          │                             │
     │                          │              ┌──────────────┼─────────────┼──────────────┐
     │                          ▼              ▼              ▼             ▼              ▼
     │                    ┌───────────┐ ┌─────────────┐ ┌────────────┐ ┌──────────┐ ┌──────────────┐
     │                    │ ListModel │ │ Mangacollec │ │ Auth API   │ │ Data API │ │Prediction API│
     │                    │(QAbstract)│ │    API      │ │  (local)   │ │ (local)  │ │  (V3 local)  │
     │                    └───────────┘ └─────────────┘ └────────────┘ └──────────┘ └──────────────┘
     │                          │              │
     │                          │              │
     │                          ▼              ▼
     │                    ┌───────────┐ ┌─────────────┐
     └────────────────────│  Cache    │ │   Images    │
         Binding          │ (TinyDB)  │ │   (local)   │
                          └───────────┘ └─────────────┘
```

---

## 11. Architecture Git

### 11.1 Structure multi-repos

Le projet utilise une architecture **monorepo + submodules** pour séparer les responsabilités tout en maintenant 
une orchestration centralisée.

```
shooterdev/booksync                    # Monorepo principal (orchestration)
├── shooterdev/booksync-api-auth       # Submodule → Service multi-utilisateur
├── shooterdev/booksync-api-data       # Submodule → Service de données extra
├── shooterdev/booksync-api-prediction # Submodule → Service de recommandations V3
└── shooterdev/booksync-app-qt         # Submodule → Application desktop PySide6/QML
```

### 11.2 Correspondance repos ↔ dossiers

| Repo GitHub                          | Dossier local              | Description                      |
|--------------------------------------|----------------------------|----------------------------------|
| `shooterdev/booksync`                | `/` (racine)               | Monorepo principal               |
| `shooterdev/booksync-api-auth`       | `booksync_api_auth/`       | Auth API - Multi-user (8000)     |
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
│   ├── DOCKER.md                  # Docker
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

Chaque doit avoir ces propre pipeline CI/CD dans son repo :

| Repo                       | CI/CD                                    |
|----------------------------|------------------------------------------|
| `booksync-api-auth`        | Tests unitaires, lint, build Docker      |
| `booksync-api-data`        | Tests unitaires, lint, build Docker      |
| `booksync-api-prediction`  | Tests unitaires, lint, build Docker      |
| `booksync-app-qt`          | Tests unitaires, lint, build PyInstaller |
| `booksync` (monorepo)      | Tests d'intégration, orchestration       |

---

## 12. Structure des écrans QML

```
qml/
├── main.qml
├── Theme.qml
├── Colors.qml
├── Fonts.qml
│
├── components/
│   ├── cards/
│   │   ├── VolumeCard.qml
│   │   ├── SeriesCard.qml
│   │   ├── BoxCard.qml
│   │   └── StatCard.qml
│   │
│   ├── badges/
│   │   ├── BadgeLastVolume.qml
│   │   ├── BadgeOwned.qml
│   │   ├── AuthorChip.qml
│   │   └── GenreChip.qml
│   │
│   ├── charts/
│   │   ├── PieChart.qml
│   │   └── BarChart.qml
│   │
│   ├── inputs/
│   │   ├── SearchBar.qml
│   │   ├── FilterDropdown.qml
│   │   └── DateNavigator.qml
│   │
│   ├── buttons/
│   │   ├── ActionButton.qml
│   │   ├── MenuButton.qml
│   │   ├── AddCollectionButton.qml
│   │   ├── ReadButton.qml
│   │   ├── FollowButton.qml
│   │   ├── CartButton.qml
│   │   └── SortSelector.qml
│   │
│   ├── ProgressBar.qml
│   ├── ConfirmDialog.qml
│   ├── LoadingSpinner.qml
│   ├── ErrorBanner.qml
│   ├── AlertDialog.qml
│   ├── ImageCover.qml
│   ├── FilterBar.qml
│   ├── MainLayout.qml
│   ├── SideBar.qml
│   ├── TopBar.qml
│   └── SubNavBar.qml
│
├── pages/
│   │
│   ├── home/
│   │   └── HomePage.qml
│   │
│   ├── news/
│   │   └── NewsPage.qml
│   │
│   ├── collection/
│   │   ├── CollectionPage.qml
│   │   ├── PileALireTab.qml
│   │   ├── CollectionTab.qml
│   │   ├── CompleteTab.qml
│   │   ├── EnviesTab.qml
│   │   ├── LoansTab.qml
│   │   ├── LoanFormDialog.qml
│   │   ├── StatsPage.qml
│   │   ├── HistoryCollectionPage.qml
│   │   └── HistoryReadingPage.qml
│   │
│   ├── prediction/
│   │   ├── PredictionPage.qml
│   │   ├── PredictionTab.qml
│   │   └── PredictionHistoriqueTab.qml
│   │
│   ├── planning/
│   │   ├── PlanningPage.qml
│   │   ├── PlanningPersonalTab.qml
│   │   ├── PlanningAllTab.qml
│   │   ├── PlanningNewTab.qml
│   │   └── PlanningBoxTab.qml
│   │
│   ├── search/
│   │   ├── SearchPage.qml
│   │   ├── SearchTitlesTab.qml
│   │   ├── SearchAuthorsTab.qml
│   │   └── SearchPublishersTab.qml
│   │
│   ├── cart/
│   │   └── CartPage.qml
│   │
│   ├── settings/
│   │   ├── SettingsPage.qml
│   │   ├── ProfileSection.qml
│   │   └── PreferencesSection.qml
│   │
│   └── Catalogue/
│       ├── VolumeDetailPage.qml
│       ├── SeriesDetailPage.qml
│       ├── EditionDetailPage.qml
│       ├── AuthorDetailPage.qml
│       └── PublisherDetailPage.qml
│
└── utils/
    ├── Scanner.qml                      # Planifié V2 (non implémenté)
    └── Constants.qml
```

---

## 13. Navigation

### Sidebar et sous-navigation

```
SideBar (principale)          Sous-navigation (contextuelle)
┌────────────────┐            ┌─────────────────────────────────────┐
│ 1. Accueil     │            │                                     │
│ 2. News        │            │                                     │
│ 3. Collection ─┼──────────► │ Pile│Coll│Compl│Env│Prêts│Stats│Hist│
│ 4. Prediction ─┼──────────► │ Prediction │ Prediction historique  │
│ 5. Planning   ─┼──────────► │ Perso │ Tout │ Nouv │ Coffrets      │
│ 6. Recherche  ─┼──────────► │ Titres │ Auteurs │ Éditeurs         │
│ 7. Panier      │            │                                     │
│                │            │                                     │
│ 8. Paramètres  │            │                                     │
└────────────────┘            └─────────────────────────────────────┘
```

### Navigation inter-fiches

| Depuis              | Action               | Vers                |
|---------------------|----------------------|---------------------|
| Toute page          | Clic sur VolumeCard  | VolumeDetailPage    |
| Toute page          | Clic sur SeriesCard  | SeriesDetailPage    |
| VolumeDetailPage    | Clic sur "Série"     | SeriesDetailPage    |
| VolumeDetailPage    | Clic sur "Édition"   | EditionDetailPage   |
| SeriesDetailPage    | Clic sur une édition | EditionDetailPage   |
| SeriesDetailPage    | Clic sur un auteur   | AuthorDetailPage    |
| EditionDetailPage   | Clic sur un volume   | VolumeDetailPage    |
| EditionDetailPage   | Clic sur éditeur     | PublisherDetailPage |
| AuthorDetailPage    | Clic sur une œuvre   | SeriesDetailPage    |
| PublisherDetailPage | Clic sur une édition | EditionDetailPage   |

---

## 14. Configuration du projet (pyproject.toml)

```toml
[project]
name = "booksync_app_qt"
version = "0.1.0"
description = "Application de gestion de collection manga"
authors = [
    { name = "Ton Nom", email = "ton@email.com" }
]
readme = "README.md"
license = { text = "MIT" }
requires-python = ">=3.12"
keywords = ["manga", "collection", "pyside6", "qml"]

dependencies = [
    "PySide6>=6.6.0",
    "httpx>=0.27.0",
    "tinydb>=4.8.0",
    "diskcache>=5.6.0",
    "pydantic>=2.5.0",
    "pydantic-settings>=2.1.0",
    "python-dotenv>=1.0.0",
    "keyring>=24.3.0",
    "opencv-python-headless>=4.9.0",
    "pyzbar>=0.1.9",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.4.0",
    "pytest-asyncio>=0.23.0",
    "pytest-cov>=4.1.0",
    "pytest-qt>=4.2.0",
    "ruff>=0.1.0",
    "mypy>=1.7.0",
    "pre-commit>=3.6.0",
]
build = [
    "pyinstaller>=6.3.0",
    "nuitka>=1.9.0",
]

[project.scripts]
booksync = "booksync_app_qt.__main__:main"

[project.gui-scripts]
booksync-gui = "booksync_app_qt.__main__:main"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.hatch.build.targets.wheel]
packages = ["src/booksync_app_qt"]

[tool.hatch.build.targets.sdist]
include = [
    "src/",
    "qml/",
    "resources/",
]

[tool.ruff]
target-version = "py312"
line-length = 100
src = ["src", "tests"]

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "C4", "UP", "ARG", "SIM"]
ignore = ["E501", "B008"]

[tool.ruff.lint.isort]
known-first-party = ["booksync_app_qt"]

[tool.mypy]
python_version = "3.12"
strict = true
warn_return_any = true
warn_unused_ignores = true
disallow_untyped_defs = true
plugins = ["pydantic.mypy"]

[[tool.mypy.overrides]]
module = ["PySide6.*"]
ignore_missing_imports = true

[tool.pytest.ini_options]
testpaths = ["tests"]
asyncio_mode = "auto"
qt_api = "pyside6"
addopts = ["-v", "--tb=short", "--cov=src/booksync_app_qt", "--cov-report=term-missing"]

[tool.coverage.run]
source = ["src/booksync_app_qt"]
branch = true
```
