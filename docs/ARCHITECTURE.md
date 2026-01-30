# Architecture Technique — BookSync

Application desktop de gestion de collection manga pour Raspberry Pi avec écran tactile.
Architecture hybride Clean + MVVM.

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
| Multi-utilisateur       | Profils familiaux avec sessions séparées     |
| Gestion des profils     | Création, sélection et suppression de profils |

> **Note** : L'authentification Mangacollec est gérée directement par l'app via OAuth2 (grant_type: password). L'Auth API ne sert plus de proxy pour les tokens Mangacollec.

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
│   ├── booksync-store.json # TinyDB (cache données)
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

### 4.1 Architecture Hybride Clean + MVVM

> Voir [COMMON.md § Architecture Hybride Clean + MVVM](./COMMON.md#8-architecture-hybride-clean--mvvm) pour le schéma complet.

L'application utilise la Clean Architecture (Domain → Application → Infrastructure) avec des **ViewModels** (QObject) au lieu de Controllers pour la couche présentation Qt/QML.

### 4.2 Structure du projet frontend

> Voir [COMMON.md § Structure des écrans QML](./COMMON.md#12-structure-des-écrans-qml) pour l'arborescence complète du dossier `qml/`.

La structure Python suit l'architecture Clean + MVVM : `domain/` → `application/` → `infrastructure/` → `presentation/`.

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
│   │ • Catalogue      │  │ • Multi-user     │  │ • volume_extra   │   │
│   │ • Séries         │  │   (profils       │  │   - dimensions   │   │
│   │ • Éditions       │  │    familiaux)    │  │   - Etc ...      │   │
│   │ • Volumes        │  │                  │  │                  │   │
│   │ • Planning       │  │ • Sessions       │  │ • Embeddings V3  │   │
│   │ • Collection     │  │   JWT locales    │  │   (predictions)  │   │
│   │ • Possessions    │  │                  │  │                  │   │
│   │ • Lectures       │  │ (Ne stocke PAS   │  │ Alimenté par     │   │
│   │ • Ect ...        │  │  les creds MC)   │  │ Scraping (Scrapy)│   │
│   └──────────────────┘  └──────────────────┘  └──────────────────┘   │
│            │                     │                     │             │
│            └─────────────────────┼─────────────────────┘             │
│                                  │                                   │
│                                  ▼                                   │
│                       ┌──────────────────────┐                       │
│                       │  Cache TinyDB local  │                       │
│                       │  + Images couvertures│                       │
│                       │  (Mode offline: R/O) │                       │
│                       └──────────────────────┘                       │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

**Répartition des responsabilités :**

> Voir [COMMON.md § Sources de données](./COMMON.md#2-sources-de-données) pour le tableau complet.

### 5.2 Stratégie de synchronisation

```
┌─────────────────────────────────────────────────────────────────────┐
│                     FLUX D'ÉCRITURE (Online requis)                 │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   1. Action utilisateur (ex: ajouter un tome)                       │
│                        │                                            │
│                        ▼                                            │
│   2. ViewModel → Service.add_volume(volume_id)                      │
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

### 5.3 Gestion des erreurs réseau

| Erreur | Comportement | Détails |
|--------|-------------|---------|
| **Timeout** | Retry avec backoff exponentiel | 3 tentatives max (1s → 2s → 4s) |
| **Pas de connexion** | Banner d'erreur non-bloquant | Message : "Connexion impossible — mode lecture seule" |
| **Erreur serveur (5xx)** | Message utilisateur | "Le serveur est temporairement indisponible, réessayez plus tard" |
| **Erreur client (4xx)** | Log + message contextuel | Détail de l'erreur API si disponible |

> **Principe** : L'application ne crash jamais sur une erreur réseau. Le mode offline (lecture seule) prend le relais automatiquement.

### 5.4 Cache local TinyDB

**Durée de validité par type de données :**

| Données                     | TTL       | Justification                              |
|-----------------------------|-----------|--------------------------------------------|
| Collection utilisateur      | 5 min     | Données personnelles fréquemment modifiées |
| Catalogue (séries, volumes) | 24h       | Rarement modifié                           |
| Planning sorties            | 1h        | Mises à jour quotidiennes                  |
| Images couvertures          | Permanent | Stockage définitif                         |

#### Structure du store local (`booksync-store.json`)

Le cache local est un fichier JSON unique qui reproduit les données de l'API Mangacollec dans un format normalisé. Le fichier de référence se trouve dans `docs/booksync_store.json`.

**Pattern normalisé standard :**

La majorité des entités suivent le même pattern :

```json
{
  "nomEntite": {
    "data": {
      "<uuid>": { /* objet entité */ }
    },
    "ordered": ["<uuid>", ...] | null
  }
}
```

- `data` : dictionnaire clé=UUID, valeur=objet entité (accès O(1) par ID)
- `ordered` : liste ordonnée d'UUIDs pour l'affichage (ou `null` si pas d'ordre défini)

**Entités suivant le pattern normalisé :**

| Clé store          | Entité          | Champs ordered | Description                              |
|---------------------|-----------------|----------------|------------------------------------------|
| `volumes`          | Volume          | `null`         | Tomes individuels (isbn, image, date...) |
| `editions`         | Edition         | `null`         | Éditions liées à une série et éditeur    |
| `series`           | Series          | ✅ liste UUID  | Séries manga (titre, type, genres)       |
| `tasks`            | Task            | `null`         | Relations auteur-métier-série            |
| `authors`          | Author          | ✅ liste UUID  | Auteurs (nom, prénom)                    |
| `publishers`       | Publisher       | ✅ liste UUID  | Éditeurs                                 |
| `types`            | Type            | `null`         | Types de contenu (Manga, Comics...)      |
| `jobs`             | Job             | `null`         | Métiers (Dessin, Scénario...)            |
| `kinds`            | Kind            | `null`         | Genres/tags avec `series_ids`            |
| `boxes`            | Box             | `null`         | Coffrets / éditions limitées             |
| `boxEditions`      | BoxEdition      | `null`         | Éditions de coffrets                     |
| `boxVolumes`       | BoxVolume       | `null`         | Liaisons coffret ↔ volume                |
| `followEditions`   | FollowEdition   | `null`         | Suivi d'éditions par l'utilisateur       |
| `possessions`      | Possession      | `null`         | Volumes possédés par l'utilisateur       |
| `boxFollowEditions`| BoxFollowEdition| `null`         | Suivi de box éditions par l'utilisateur  |
| `boxPossessions`   | BoxPossession   | `null`         | Coffrets possédés par l'utilisateur      |
| `readEditions`     | ReadEdition     | `null`         | Éditions en cours de lecture             |
| `reads`            | Read            | `null`         | Volumes lus par l'utilisateur            |
| `borrowers`        | Borrower        | `null`         | Emprunteurs / lieux de stockage          |
| `loans`            | Loan            | `null`         | Prêts actifs                             |
| `amazonOffers`     | AmazonOffer     | `null`         | Offres marchandes Amazon                 |
| `bdfugueOffers`    | BdfugueOffer    | `null`         | Offres marchandes BDfugue                |
| `polls`            | Poll            | `null`         | Sondages                                 |
| `pollQuestions`    | PollQuestion    | `null`         | Questions de sondage                     |
| `pollChoices`      | PollChoice      | `null`         | Choix de sondage                         |
| `pollAnswers`      | PollAnswer      | `null`         | Réponses de sondage                      |

**Entités avec structure spécifique (hors pattern normalisé) :**

| Clé store           | Structure                                                                 |
|----------------------|---------------------------------------------------------------------------|
| `user`              | Objet plat — profil utilisateur courant avec `subscriptions` (tableau)    |
| `news`              | `{ volumes: [uuid], boxes: [uuid] }` — IDs des dernières sorties         |
| `planning`          | `{ "YYYY-MM": { volumes: [uuid], boxes: [uuid] } }` — planning par mois |
| `recommendation`    | `{ series_id: [edition_id, [series_ids]] }` — recommandations            |
| `nativeAd`          | Objet — publicités natives (bannière, planning perso)                    |
| `cart`              | Objet — panier actif avec `items` (tableau) et `box_items` (tableau)     |
| `publicCollection`  | Objet — collection publique d'un autre utilisateur                       |
| `publicUser`        | Objet — profil public d'un autre utilisateur                             |

### 5.5 Cache des images

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

> Voir [COMMON.md § Structure des écrans QML](./COMMON.md#12-structure-des-écrans-qml) pour la structure complète du dossier `qml/components/`.

### 6.3 Navigation

> Voir [COMMON.md § Navigation](./COMMON.md#13-navigation) pour le schéma complet de la navigation.

---

## 7. Sécurité

### 7.1 Authentification

> Voir [COMMON.md § Flux d'authentification](./COMMON.md#9-flux-dauthentification) pour les schémas complets.

**Détails d'implémentation :**

| Aspect                  | Implémentation                                          |
|-------------------------|---------------------------------------------------------|
| Tokens Mangacollec      | OAuth2 direct (access + refresh), stockés dans keyring  |
| Refresh automatique     | L'app refresh le token si expiré (réponse 401)          |
| Credentials Mangacollec | Email/password stockés dans keyring (OS)                |
| Multi-utilisateur       | Profils séparés via Auth API locale                     |

### 7.2 Gestion des erreurs token

| État | Action automatique | Fallback |
|------|-------------------|----------|
| **TokenOutdated** | Refresh automatique via Auth API | — |
| **TokenRefreshFailed** | Ré-authentification complète (login) | Écran de connexion |
| **RequestUnauthorized** (401) | Refresh token + retry de la requête | Ré-authentification si retry échoue |

> **Flux** : Toute réponse 401 déclenche un refresh transparent. Si le refresh échoue, l'utilisateur est redirigé vers l'écran de connexion.

### 7.3 Communication

| Aspect     | Implémentation                             |
|------------|--------------------------------------------|
| Protocole  | HTTPS (TLS 1.3)                            |
| Réseau     | LAN uniquement (pas d'exposition Internet) |
| Validation | Pydantic côté client et serveur            |

### 7.4 Données locales

| Donnée              | Protection                |
|---------------------|---------------------------|
| Tokens Mangacollec  | keyring système           |
| Credentials         | keyring système           |
| Cache TinyDB        | Permissions fichier (600) |
| Images              | Permissions fichier (644) |

---

## 8. Configuration et déploiement

### 8.1 Dépôts Git

> Voir [COMMON.md § Architecture Git](./COMMON.md#11-architecture-git) pour la structure multi-repos avec submodules.

Le projet est organisé en monorepo avec submodules. Voir les commandes dans [COMMON.md § Architecture Git](./COMMON.md#11-architecture-git).

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

> **Documentation complète** : Voir [DOCKER.md](./DOCKER.md) pour le guide détaillé des images, commandes et configurations.

#### Résumé des services

| Service         | Image                            | Port | Base           | Utilisateur BDD      |
|-----------------|----------------------------------|------|----------------|----------------------|
| PostgreSQL      | `pgvector/pgvector:pg17`         | 5432 | PostgreSQL 17  | `postgres` (admin)   |
| Auth API        | `booksync-api-auth:latest`       | 8000 | Python 3.12    | `booksync_auth`      |
| Data API        | `booksync-api-data:latest`       | 8001 | Python 3.12    | `booksync_data`      |
| Prediction API  | `booksync-api-prediction:latest` | 8002 | Python 3.12    | `booksync_prediction`|

> **Architecture** : Base de données PostgreSQL unique partagée par tous les services, avec utilisateurs distincts pour la sécurité.
> **Architectures supportées** : `linux/amd64` et `linux/arm64` (Raspberry Pi).

#### Commandes rapides

```bash
# Build local (architecture courante)
docker build -t booksync-api-auth:latest ./booksync_api_auth
docker build -t booksync-api-data:latest ./booksync_api_data
docker build -t booksync-api-prediction:latest ./booksync_api_prediction

# Build multi-architecture (arm64 + amd64)
docker buildx build --platform linux/amd64,linux/arm64 -t booksync-api-auth:latest --push ./booksync_api_auth
docker buildx build --platform linux/amd64,linux/arm64 -t booksync-api-data:latest --push ./booksync_api_data
docker buildx build --platform linux/amd64,linux/arm64 -t booksync-api-prediction:latest --push ./booksync_api_prediction

# Démarrage des services
docker-compose up -d

# Avec Prediction API (V3)
docker-compose --profile v3 up -d

# Logs
docker-compose logs -f

# Arrêt
docker-compose down
```

> Pour les commandes avancées (debugging, réseau, volumes, production), consulter [DOCKER.md](./DOCKER.md).

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

### A. Configuration du projet (pyproject.toml)

> Voir [COMMON.md § Configuration du projet](./COMMON.md#14-configuration-du-projet-pyprojecttoml) pour le fichier complet.

### B. Compatibilité Raspberry Pi

| Composant   | Raspberry Pi 4   | Raspberry Pi 5    |
|-------------|------------------|-------------------|
| PySide6     | ✅ (via pip)      | ✅ (via pip)       |
| Qt 6        | ✅                | ✅                 |
| OpenGL ES   | ✅ (VideoCore VI) | ✅ (VideoCore VII) |
| SQLite      | ✅                | ✅                 |
| Python 3.12 | ✅                | ✅                 |

### C. Glossaire

> Voir [COMMON.md § Glossaire](./COMMON.md#7-glossaire).
