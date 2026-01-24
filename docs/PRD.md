# BookSync App — Spécification Technique

Application desktop de gestion de collection manga en **PySide6 / QML**,
inspirée de [mangacollec.com](https://www.mangacollec.com).

> **Note** : Ce document est aligné avec [ARCHITECTURE.md](./ARCHITECTURE.md) qui décrit l'architecture technique déployée.

---

## Table des matières

1. [Vision et objectif](#1-vision-et-objectif)
2. [Fonctionnalités](#2-fonctionnalités)
3. [Modèle de données](#3-modèle-de-données)
4. [Structure des écrans QML](#4-structure-des-écrans-qml)
5. [Priorisation MVP / V1 / V2](#5-priorisation-mvp--v1--v2)
6. [Architecture Python / PySide6](#6-architecture-python--pyside6)
7. [Stratégie de synchronisation API](#7-stratégie-de-synchronisation-api)
8. [Configuration du projet](#8-configuration-du-projet)

---

## 1. Vision et objectif

### Problème résolu

Gérer une collection de mangas physiques : éviter les doublons, ne pas rater les sorties, suivre sa progression de lecture.

### Utilisateurs cibles

Collectionneurs de mangas francophones, usage Raspberry Pi (équipement embarqué) avec écran tactile de 1280x720.

### Architecture déployée

> Voir [COMMON.md § Architecture déployée](./COMMON.md#1-architecture-déployée) pour le schéma complet.

L'application fonctionne en architecture distribuée : Raspberry Pi (frontend) + Serveur/NAS (APIs locales) + API Mangacollec (catalogue).

### Sources de données

> Voir [COMMON.md § Sources de données](./COMMON.md#2-sources-de-données) pour le détail complet.

### Dépôts Git

> Voir [COMMON.md § Architecture Git](./COMMON.md#11-architecture-git) pour la structure multi-repos avec submodules.

---

## 2. Fonctionnalités

### 2.1 Liste complète (25 fonctionnalités)

#### Pages principales

| # | Fonctionnalité           | Description                                                                                                  |
|---|--------------------------|--------------------------------------------------------------------------------------------------------------|
| 1 | **Accueil / Nouveautés** | Grille des dernières sorties avec couverture, titre, numéro, badge "Dernier tome", indicateur "déjà possédé" |
| 2 | **Navigation latérale**  | Menu permanent : Nouveautés, Collection, Planning, Recherche, Panier, Compte                                 |

#### Collection (8 sous-sections)

| #   | Fonctionnalité            | Description                                                                      |
|-----|---------------------------|----------------------------------------------------------------------------------|
| 2.1 | **Pile à lire**           | Progression de lecture par série (lus/possédés), stats globales, pause/terminer  |
| 2.2 | **Collection**            | Vue de tous les tomes possédés, progression par série, tri (date/alphabétique)   |
| 2.3 | **Compléter**             | Détection automatique des tomes manquants dans les séries incomplètes            |
| 2.4 | **Envies**                | Liste de souhaits pour planifier les achats futurs                               |
| 2.5 | **Prêts**                 | Suivi des volumes prêtés (à qui, où) avec formulaire de création                 |
| 2.6 | **Statistiques**          | Dashboard : répartition par éditeur/genre (graphiques), derniers ajouts/lectures |
| 2.7 | **Historique Collection** | Journal chronologique des ajouts (par année/mois)                                |
| 2.8 | **Historique Lecture**    | Journal des lectures effectuées (par semaine/mois/année)                         |

#### Planning (4 onglets)

| #   | Fonctionnalité   | Description                                                     |
|-----|------------------|-----------------------------------------------------------------|
| 3.1 | **Personnalisé** | Sorties à venir des séries suivies uniquement                   |
| 3.2 | **Tout**         | Toutes les sorties prévues, filtre par éditeur                  |
| 3.3 | **Nouveautés**   | Uniquement les Tome 1, one-shots, guidebooks (nouvelles séries) |
| 3.4 | **Coffrets**     | Éditions collector, coffrets, intégrales, packs spéciaux        |

#### Recherche & Catalogue

| #  | Fonctionnalité        | Description                                                                           |
|----|-----------------------|---------------------------------------------------------------------------------------|
| 4  | **Recherche globale** | Recherche par Titres, Auteurs, Éditeurs                                               |
| 7  | **Fiche Volume**      | Couverture, navigation inter-tomes, actions, stats communautaires, prix, résumé, ISBN |
| 8  | **Fiche Série**       | Type, genres, auteurs avec rôles, liste des éditions                                  |
| 9  | **Fiche Édition**     | Lien série/éditeur, stats (parus/à paraître), statut, grille des tomes                |
| 10 | **Fiche Auteur**      | Nom, grille de toutes ses œuvres avec rôle                                            |
| 11 | **Fiche Éditeur**     | Dernières/prochaines sorties, catalogue complet alphabétique                          |

#### Autres

| #  | Fonctionnalité          | Description                                                             |
|----|-------------------------|-------------------------------------------------------------------------|
| 5  | **Panier**              | Volumes à acheter, calcul du budget, précommandes, redirection marchand |
| 6  | **Paramètres**          | Profil, préférences (thème, images adultes), multi-utilisateur          |
| 23 | **Scanner code-barres** | Support douchette Bluetooth (ISBN) pour ajout rapide                    |
| 24 | **Multi-utilisateur**   | Profils séparés pour usage familial                                     |
| 25 | **Thème clair/sombre**  | Basculement avec charte graphique cohérente                             |

---

## 3. Modèle de données

### 3.1 Tables Catalogue

#### Type (`types`)

| Champ        | Type        | Description  |
|--------------|-------------|--------------|
| `id`         | uuid_v4     | Clé primaire |
| `title`      | String(255) | Nom du type  |
| `to_display` | Boolean     | Affichage    |

#### Kind (`kinds`)

| Champ   | Type        | Description  |
|---------|-------------|--------------|
| `id`    | uuid_v4     | Clé primaire |
| `title` | String(255) | Nom du genre |

#### Publisher (`publishers`)

| Champ            | Type        | Description       |
|------------------|-------------|-------------------|
| `id`             | uuid_v4     | Clé primaire      |
| `title`          | String(255) | Nom de l'éditeur  |
| `closed`         | Boolean     | Éditeur fermé     |
| `editions_count` | Integer     | Nombre d'éditions |
| `no_amazon`      | Boolean     | Pas sur Amazon    |

#### Job (`jobs`)

| Champ   | Type        | Description   |
|---------|-------------|---------------|
| `id`    | uuid_v4     | Clé primaire  |
| `title` | String(255) | Nom du métier |

#### Author (`authors`)

| Champ         | Type        | Description      |
|---------------|-------------|------------------|
| `id`          | uuid_v4     | Clé primaire     |
| `name`        | String(255) | Nom              |
| `first_name`  | String(255) | Prénom           |
| `tasks_count` | Integer     | Nombre de tâches |

#### Series (`series`)

| Champ            | Type        | Description       |
|------------------|-------------|-------------------|
| `id`             | uuid_v4     | Clé primaire      |
| `title`          | String(255) | Titre de la série |
| `type_id`        | uuid_v4     | FK → types        |
| `adult_content`  | Boolean     | Contenu adulte    |
| `editions_count` | Integer     | Nombre d'éditions |
| `tasks_count`    | Integer     | Nombre de tâches  |

#### SeriesKind (`series_kinds`)

| Champ       | Type    | Description     |
|-------------|---------|-----------------|
| `series_id` | uuid_v4 | PK, FK → series |
| `kind_id`   | uuid_v4 | PK, FK → kinds  |

#### Task (`tasks`)

| Champ       | Type    | Description  |
|-------------|---------|--------------|
| `id`        | uuid_v4 | Clé primaire |
| `series_id` | uuid_v4 | FK → series  |
| `job_id`    | uuid_v4 | FK → jobs    |
| `author_id` | uuid_v4 | FK → authors |

#### Edition (`editions`)

| Champ                   | Type        | Description                    |
|-------------------------|-------------|--------------------------------|
| `id`                    | uuid_v4     | Clé primaire                   |
| `title`                 | String(255) | Titre de l'édition             |
| `series_id`             | uuid_v4     | FK → series                    |
| `publisher_id`          | uuid_v4     | FK → publishers                |
| `parent_edition_id`     | uuid_v4     | FK → editions (self-reference) |
| `volumes_count`         | Integer     | Nombre de volumes              |
| `last_volume_number`    | Integer     | Dernier numéro                 |
| `commercial_stop`       | Boolean     | Arrêt commercial               |
| `not_finished`          | Boolean     | Non terminée                   |
| `follow_editions_count` | Integer     | Nombre de suivis               |

#### Volume (`volumes`)

| Champ               | Type        | Description           |
|---------------------|-------------|-----------------------|
| `id`                | uuid_v4     | Clé primaire          |
| `title`             | String(255) | Titre du volume       |
| `number`            | Integer     | Numéro du tome        |
| `release_date`      | Date        | Date de sortie        |
| `isbn`              | String(20)  | ISBN                  |
| `asin`              | String(20)  | ASIN Amazon           |
| `edition_id`        | uuid_v4     | FK → editions         |
| `possessions_count` | Integer     | Nombre de possessions |
| `not_sold`          | Boolean     | Non vendu             |
| `image_url`         | Text        | URL de l'image        |
| `nb_pages`          | Integer     | Nombre de pages       |
| `content`           | Text        | Description/contenu   |

#### VolumeExtra (`volume_extra`)

> **Note** : Cette table est stockée sur le serveur (PostgreSQL) et accessible via la **Data API locale** (port 8001). Ces données sont absentes de l'API Mangacollec et alimentées par scraping (ex: BubbleBD).

| Champ        | Type       | Description           |
|--------------|------------|-----------------------|
| `volume_id`  | uuid_v4    | PK, FK → volumes      |
| `object_id`  | String(50) | ID BubbleBD           |
| `nb_pages`   | Integer    | Nombre de pages       |
| `length`     | String(20) | Longueur (cm)         |
| `height`     | String(20) | Hauteur (cm)          |
| `width`      | String(20) | Largeur (cm)          |
| `weight`     | String(20) | Poids (kg)            |
| `extra_info` | Text       | Infos supplémentaires |

### 3.2 Tables Coffrets

#### BoxEdition (`box_editions`)

| Champ                       | Type        | Description        |
|-----------------------------|-------------|--------------------|
| `id`                        | uuid_v4     | Clé primaire       |
| `title`                     | String(255) | Titre              |
| `publisher_id`              | uuid_v4     | FK → publishers    |
| `boxes_count`               | Integer     | Nombre de coffrets |
| `adult_content`             | Boolean     | Contenu adulte     |
| `box_follow_editions_count` | Integer     | Nombre de suivis   |

#### Box (`boxes`)

| Champ                   | Type        | Description           |
|-------------------------|-------------|-----------------------|
| `id`                    | uuid_v4     | Clé primaire          |
| `title`                 | String(255) | Titre du coffret      |
| `number`                | Integer     | Numéro                |
| `release_date`          | Date        | Date de sortie        |
| `isbn`                  | String(20)  | ISBN                  |
| `asin`                  | String(20)  | ASIN                  |
| `commercial_stop`       | Boolean     | Arrêt commercial      |
| `box_edition_id`        | uuid_v4     | FK → box_editions     |
| `box_possessions_count` | Integer     | Nombre de possessions |
| `image_url`             | Text        | URL de l'image        |

#### BoxVolume (`box_volumes`)

| Champ       | Type    | Description            |
|-------------|---------|------------------------|
| `id`        | uuid_v4 | Clé primaire           |
| `box_id`    | uuid_v4 | FK → boxes             |
| `volume_id` | uuid_v4 | FK → volumes           |
| `included`  | Boolean | Inclus dans le coffret |

### 3.3 Tables Utilisateurs

#### User (`users`)

| Champ                  | Type        | Description                        |
|------------------------|-------------|------------------------------------|
| `id`                   | uuid_v4     | Clé primaire                       |
| `id_mangacollec`       | uuid_v4     | UUID de mangacollec                |
| `username`             | String(100) | Nom d'utilisateur (unique, indexé) |
| `certify_adult`        | Boolean     | Certify adult                      |
| `email`                | String(255) | Email (unique, indexé)             |
| `password`             | String(255) | Password                           |
| `password_mangacollec` | String(255) | Password mangacollec               |

### 3.4 Collections Utilisateurs

#### FollowEdition (`follow_editions`)

| Champ        | Type     | Description      |
|--------------|----------|------------------|
| `id`         | uuid_v4  | Clé primaire     |
| `user_id`    | uuid_v4  | FK → users       |
| `edition_id` | uuid_v4  | FK → editions    |
| `following`  | Boolean  | Statut de suivi  |
| `created_at` | DateTime | Date création    |
| `updated_at` | DateTime | Date mise à jour |

#### Possession (`possessions`)

| Champ        | Type     | Description   |
|--------------|----------|---------------|
| `id`         | uuid_v4  | Clé primaire  |
| `user_id`    | uuid_v4  | FK → users    |
| `volume_id`  | uuid_v4  | FK → volumes  |
| `created_at` | DateTime | Date création |

#### BoxFollowEdition (`box_follow_editions`)

| Champ            | Type     | Description       |
|------------------|----------|-------------------|
| `id`             | uuid_v4  | Clé primaire      |
| `user_id`        | uuid_v4  | FK → users        |
| `box_edition_id` | uuid_v4  | FK → box_editions |
| `following`      | Boolean  | Statut de suivi   |
| `created_at`     | DateTime | Date création     |
| `updated_at`     | DateTime | Date mise à jour  |

#### BoxPossession (`box_possessions`)

| Champ        | Type     | Description   |
|--------------|----------|---------------|
| `id`         | uuid_v4  | Clé primaire  |
| `user_id`    | uuid_v4  | FK → users    |
| `box_id`     | uuid_v4  | FK → boxes    |
| `created_at` | DateTime | Date création |

#### ReadEdition (`read_editions`)

| Champ        | Type     | Description       |
|--------------|----------|-------------------|
| `id`         | uuid_v4  | Clé primaire      |
| `user_id`    | uuid_v4  | FK → users        |
| `edition_id` | uuid_v4  | FK → editions     |
| `reading`    | Boolean  | Statut de lecture |
| `created_at` | DateTime | Date création     |
| `updated_at` | DateTime | Date mise à jour  |

#### Read (`reads`)

| Champ        | Type     | Description   |
|--------------|----------|---------------|
| `id`         | uuid_v4  | Clé primaire  |
| `user_id`    | uuid_v4  | FK → users    |
| `volume_id`  | uuid_v4  | FK → volumes  |
| `created_at` | DateTime | Date création |

### 3.5 Tables Prêts

#### Borrower (`borrowers`)

| Champ        | Type        | Description         |
|--------------|-------------|---------------------|
| `id`         | uuid_v4     | Clé primaire        |
| `user_id`    | uuid_v4     | FK → users          |
| `title`      | String(255) | Titre               |
| `category`   | String(20)  | Nom de la catégorie |
| `created_at` | DateTime    | Date création       |

#### Loan (`loans`)

| Champ           | Type     | Description      |
|-----------------|----------|------------------|
| `id`            | uuid_v4  | Clé primaire     |
| `possession_id` | uuid_v4  | FK → possessions |
| `borrower_id`   | uuid_v4  | FK → borrowers   |
| `created_at`    | DateTime | Date création    |

### 3.6 Tables Panier

#### Cart (`carts`)

| Champ                     | Type        | Description                 |
|---------------------------|-------------|-----------------------------|
| `id`                      | uuid_v4     | PK (vient de l'API)         |
| `user_id`                 | uuid_v4     | FK → users                  |
| `store`                   | String(50)  | "bdfugue", "amazon", etc.   |
| `store_cart_id`           | String(100) | ID côté boutique (nullable) |
| `store_cart_sub_total`    | String(20)  | Prix total formaté          |
| `store_cart_purchase_url` | Text        | URL d'achat                 |
| `store_cart_active`       | Boolean     | Panier actif                |
| `updated_at`              | DateTime    | Dernière synchronisation    |

#### CartItem (`cart_items`)

| Champ                          | Type        | Description                 |
|--------------------------------|-------------|-----------------------------|
| `id`                           | uuid_v4     | PK (vient de l'API)         |
| `cart_id`                      | uuid_v4     | FK → carts                  |
| `volume_id`                    | uuid_v4     | FK → volumes                |
| `quantity`                     | Integer     | Quantité                    |
| `created_at`                   | DateTime    | Date d'ajout                |
| `store_cart_item_id`           | String(100) | ID côté boutique (nullable) |
| `store_cart_item_price`        | String(20)  | Prix formaté                |
| `store_cart_item_link`         | Text        | Lien produit boutique       |
| `store_cart_item_availability` | String(100) | "En stock !", etc.          |

#### CartBoxItem (`cart_box_items`)

| Champ                          | Type        | Description                 |
|--------------------------------|-------------|-----------------------------|
| `id`                           | uuid_v4     | PK (vient de l'API)         |
| `cart_id`                      | uuid_v4     | FK → carts                  |
| `box_id`                       | uuid_v4     | FK → boxes                  |
| `quantity`                     | Integer     | Quantité                    |
| `created_at`                   | DateTime    | Date d'ajout                |
| `store_cart_item_id`           | String(100) | ID côté boutique (nullable) |
| `store_cart_item_price`        | String(20)  | Prix formaté                |
| `store_cart_item_link`         | Text        | Lien produit boutique       |
| `store_cart_item_availability` | String(100) | Disponibilité               |

### 3.7 Schéma relationnel

```
┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│   Author    │──M:N──│    Series   │──M:N──│    Kind     │
└─────────────┘       └──────┬──────┘       └─────────────┘
                             │ 1:N
                             ▼
┌─────────────┐       ┌─────────────┐
│  Publisher  │──1:N──│   Edition   │◄──────┐ (édition parallèle)
└─────────────┘       └──────┬──────┘───────┘
                             │ 1:N
                             ▼
                      ┌─────────────┐
                      │   Volume    │
                      └──────┬──────┘
                             │
       ┌─────────────────────┼───────────────────┐
       │                     │                   │
       ▼                     ▼                   ▼
┌─────────────┐       ┌─────────────┐     ┌─────────────┐
│ Possession  │       │    Loan     │     │  CartItem   │
└──────┬──────┘       └──────┬──────┘     └──────┬──────┘
       │                     │                   │
       └─────────────────────┼───────────────────┘
                             │
                      ┌──────▼──────┐
                      │    User     │
                      └─────────────┘
```

---

## 4. Structure des écrans QML

### 4.1 Arborescence des fichiers

```
qml/
├── main.qml
├── Theme.qml
├── Color.qml
│
├── components/
│   ├── cards/
│   │   ├── VolumeCard.qml
│   │   ├── SeriesCard.qml
│   │   ├── BoxCard.qml
│   │   └── StatCard.qml
│   ├── badges/
│   │   ├── BadgeLastVolume.qml
│   │   ├── BadgeOwned.qml
│   │   ├── AuthorChip.qml
│   │   └── GenreChip.qml
│   ├── charts/
│   │   ├── PieChart.qml
│   │   └── BarChart.qml
│   ├── inputs/
│   │   ├── SearchBar.qml
│   │   ├── FilterDropdown.qml
│   │   └── DateNavigator.qml
│   ├── ActionButton.qml
│   ├── MenuButton.qml
│   ├── ProgressBar.qml
│   ├── ConfirmDialog.qml
│   └── LoadingSpinner.qml
│
├── layouts/
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
│   │   ├── CollectionContainer.qml
│   │   ├── PileALirePage.qml
│   │   ├── CollectionPage.qml
│   │   ├── CompletePage.qml
│   │   ├── EnviesPage.qml
│   │   ├── LoansPage.qml
│   │   ├── LoanFormDialog.qml
│   │   ├── StatsPage.qml
│   │   ├── HistoryCollectionPage.qml
│   │   └── HistoryReadingPage.qml
│   │
│   ├── prediction/
│   │   ├── PredictionPage.qml
│   │   └── PredictionTab.qml
│   │
│   ├── planning/
│   │   ├── PlanningContainer.qml
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
│   │   ├── PreferencesSection.qml
│   │   └── UserSwitcher.qml
│   │
│   └── Catalogue/
│       ├── VolumeDetailPage.qml
│       ├── SeriesDetailPage.qml
│       ├── EditionDetailPage.qml
│       ├── AuthorDetailPage.qml
│       └── PublisherDetailPage.qml
│
└── utils/
    ├── Scanner.qml
    └── Constants.qml
```

### 4.2 Navigation

```
SideBar (principale)          Sous-navigation (contextuelle)
┌────────────────┐            ┌─────────────────────────────────────┐
│ 1. Accueil     │            │                                     │
│ 2. Collection ─┼──────────► │ Pile│Coll│Compl│Env│Prêts│Stats│Hist│
│ 3. Prediction ─┼──────────► │ Prediction │ Prediction historique  │
│ 4. Planning   ─┼──────────► │ Perso │ Tout │ Nouv │ Coffrets      │
│ 5. Recherche  ─┼──────────► │ Titres │ Auteurs │ Éditeurs         │
│ 6. Panier      │            │                                     │
│ 7. Paramètres  │            │                                     │
└────────────────┘            └─────────────────────────────────────┘
```

### 4.3 Navigation entre fiches

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

## 5. Priorisation MVP / V1 / V2

### 5.1 MVP — App utilisable (Core)

**Objectif** : Gérer sa collection de base, chercher et consulter des mangas

| #   | Fonctionnalité                        | Justification                      |
|-----|---------------------------------------|------------------------------------|
| 2   | Navigation latérale                   | Structure de l'app                 |
| 25  | Thème clair/sombre                    | Confort visuel dès le départ       |
| 2.2 | Collection                            | Cœur de l'app                      |
| 4   | Recherche globale (Titres uniquement) | Trouver un manga                   |
| 7   | Fiche Volume                          | Détail + actions (ajouter/retirer) |
| 8   | Fiche Série                           | Voir les éditions                  |
| 9   | Fiche Édition                         | Voir tous les tomes                |
| —   | Synchronisation API                   | Infrastructure obligatoire         |
| —   | Cache local (SQLite)                  | Performance + lecture hors-ligne   |

**Résultat** : L'utilisateur peut chercher, consulter et gérer sa collection.

### 5.2 V1 — Valeur quotidienne

**Objectif** : Fonctionnalités qui rendent l'app utile au quotidien

| #   | Fonctionnalité                         | Justification             |
|-----|----------------------------------------|---------------------------|
| 1   | Accueil / Nouveautés                   | Page d'entrée, découverte |
| 2.3 | Compléter (Tomes manquants)            | Identifier les trous      |
| 2.4 | Envies (Wishlist)                      | Planifier ses achats      |
| 3.1 | Planning personnalisé                  | Ne plus rater une sortie  |
| 3.2 | Planning global                        | Voir toutes les sorties   |
| 4   | Recherche globale (Auteurs + Éditeurs) | Recherche complète        |
| 10  | Fiche Auteur                           | Navigation enrichie       |
| 11  | Fiche Éditeur                          | Navigation enrichie       |
| 6   | Gestion du compte (basique)            | Profil + connexion        |

**Résultat** : Expérience complète de gestion et découverte.

### 5.3 V2 — Lecture & Avancé

**Objectif** : Suivi de lecture, statistiques, outils avancés

| #   | Fonctionnalité              | Justification               |
|-----|-----------------------------|-----------------------------|
| 2.1 | Pile à lire                 | Suivre sa progression       |
| 2.6 | Statistiques de collection  | Visualisation (graphiques)  |
| 2.7 | Historique de la collection | Journal des ajouts          |
| 2.8 | Historique de lecture       | Journal des lectures        |
| 3.3 | Planning nouveautés (T1)    | Découverte nouvelles séries |
| 3.4 | Planning coffrets           | Éditions spéciales          |
| 5   | Panier d'achats             | Gestion des achats intégrée |
| 23  | Scanner code-barres         | Ajout rapide                |

**Résultat** : Expérience enrichie avec tracking lecture et outils.

### 5.3 V3 — Prediction / Recommandations

**Objectif** : Aider l'utilisateur à choisir sa prochaine lecture quand il ne sait pas quoi lire

| #  | Fonctionnalité            | Description                                                        |
|----|---------------------------|--------------------------------------------------------------------|
| 26 | **QCM de préférences**    | Questionnaire rapide sur les envies du moment (action, romance...) |
| 27 | **Sélection d'humeur**    | Choix d'une humeur (joyeux, mélancolique, stressé, curieux...)     |
| 28 | **Prédiction de lecture** | Algorithme qui suggère un tome de la pile à lire selon QCM/humeur  |
| 29 | **Prédiction historique** | Historique des prédictions.                                        |

**Résultat** : L'utilisateur obtient une recommandation personnalisée de sa prochaine lecture.

### 5.4 Hors-périmètre (V4+)

| #   | Fonctionnalité              | Raison                                |
|-----|-----------------------------|---------------------------------------|
| 2.5 | Gestion des prêts           | Usage minoritaire                     |
| 24  | Multi-utilisateur           | Complexité élevée                     |
| 6   | Gestion du compte (avancée) | Préférences détaillées, liens sociaux |

### 5.5 Résumé visuel

```
┌─────────────────────────────────────────────────────────────────────┐
│                              MVP                                    │
│  Navigation · Thème · Collection · Recherche (Titres)               │
│  Fiche Volume · Fiche Série · Fiche Édition                         │
│  Sync API · Cache local                                             │
└─────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                              V1                                     │
│  Accueil · Compléter · Wishlist · Planning (Perso + Global)         │
│  Recherche (Auteurs + Éditeurs) · Fiche Auteur · Fiche Éditeur      │
│  Compte (basique)                                                   │
└─────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                              V2                                     │
│  Pile à lire · Statistiques · Historique (Collection + Lecture)     │
│  Planning (Nouveautés + Coffrets) · Panier · Scanner                │
└─────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                              V3                                     │
│  Prediction / Recommandations                                       │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         HORS-PÉRIMÈTRE                              │
│  Prêts · Multi-utilisateur · Compte avancé                          │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 6. Architecture Python / PySide6

### 6.1 Clean Architecture (Hexagonale)

> Voir [COMMON.md § Clean Architecture](./COMMON.md#8-clean-architecture) pour le schéma complet.

### 6.2 Structure des dossiers

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
│   │   │   ├── database.py         # SQLite connection
│   │   │   ├── models/             # SQLAlchemy models
│   │   │   └── repositories/
│   │   │       ├── volume_cache.py
│   │   │       ├── series_cache.py
│   │   │       └── ...
│   │   ├── images/
│   │   │   └── image_cache.py      # Cache couvertures
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
│   │   ├── layouts/
│   │   └── pages/
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

### 6.3 Flux de données

> Voir [COMMON.md § Flux de données](./COMMON.md#10-flux-de-données) pour le schéma complet.

### 6.4 Exemple Controller

```python
from PySide6.QtCore import QObject, Signal, Slot, Property

class CollectionController(QObject):
    loadingChanged = Signal()
    errorChanged = Signal()
    collectionChanged = Signal()

    def __init__(self, collection_service, model):
        super().__init__()
        self._service = collection_service
        self._model = model
        self._loading = False
        self._error = ""

    @Property(bool, notify=loadingChanged)
    def loading(self):
        return self._loading

    @Property(str, notify=errorChanged)
    def error(self):
        return self._error

    @Property(QObject, constant=True)
    def volumes(self):
        return self._model

    @Slot(str)
    def addVolume(self, volume_id: str):
        self._set_loading(True)
        asyncio.create_task(self._add_volume_async(volume_id))

    async def _add_volume_async(self, volume_id: str):
        try:
            await self._service.add_to_collection(volume_id)
            self._model.refresh()
            self.collectionChanged.emit()
        except ApiError as e:
            self._set_error(e.message)
        finally:
            self._set_loading(False)

    @Slot(str)
    def removeVolume(self, volume_id: str):
        self._set_loading(True)
        asyncio.create_task(self._remove_volume_async(volume_id))

    async def _remove_volume_async(self, volume_id: str):
        try:
            await self._service.remove_from_collection(volume_id)
            self._model.refresh()
            self.collectionChanged.emit()
        except ApiError as e:
            self._set_error(e.message)
        finally:
            self._set_loading(False)

    def _set_loading(self, value: bool):
        self._loading = value
        self.loadingChanged.emit()

    def _set_error(self, message: str):
        self._error = message
        self.errorChanged.emit()

    def _clear_error(self):
        self._set_error("")
```

### 6.5 Exemple ViewModel

```python
from PySide6.QtCore import QAbstractListModel, Qt, QModelIndex

class VolumeListModel(QAbstractListModel):
    IdRole = Qt.UserRole + 1
    TitleRole = Qt.UserRole + 2
    NumberRole = Qt.UserRole + 3
    ImageUrlRole = Qt.UserRole + 4
    IsOwnedRole = Qt.UserRole + 5

    def __init__(self, repository):
        super().__init__()
        self._repository = repository
        self._items = []

    def roleNames(self):
        return {
            self.IdRole: b"volumeId",
            self.TitleRole: b"title",
            self.NumberRole: b"number",
            self.ImageUrlRole: b"imageUrl",
            self.IsOwnedRole: b"isOwned",
        }

    def rowCount(self, parent=QModelIndex()):
        return len(self._items)

    def data(self, index, role=Qt.DisplayRole):
        if not index.isValid():
            return None
        item = self._items[index.row()]
        if role == self.IdRole:
            return item.id
        elif role == self.TitleRole:
            return item.title
        elif role == self.NumberRole:
            return item.number
        elif role == self.ImageUrlRole:
            return item.image_url
        elif role == self.IsOwnedRole:
            return item.is_owned
        return None

    def refresh(self):
        self.beginResetModel()
        self._items = self._repository.get_all()
        self.endResetModel()
```

### 6.6 Utilisation dans QML

```qml
ListView {
    model: collectionController.volumes
    delegate: VolumeCard {
        volumeId: model.volumeId
        title: model.title
        number: model.number
        imageUrl: model.imageUrl
        isOwned: model.isOwned
        onClicked: stackView.push("details/VolumeDetailPage.qml", { volumeId: volumeId })
    }
}
```

---

## 7. Stratégie de synchronisation API

### 7.1 Principes

| Aspect         | Choix                               | Justification                      |
|----------------|-------------------------------------|------------------------------------|
| **Mode**       | Online-only avec cache lecture      | API Mangacollec = source de vérité |
| **Écriture**   | Toujours via API → puis cache local | L'API génère les IDs               |
| **Lecture**    | Cache local (refresh depuis API)    | Performance                        |
| **Hors-ligne** | Lecture seule (cache)               | Pas d'écriture offline             |

### 7.2 Authentification

> Voir [COMMON.md § Flux d'authentification](./COMMON.md#9-flux-dauthentification) pour les schémas complets.

### 7.3 Flux d'écriture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         ACTION UTILISATEUR                          │
│                    (ex: ajouter un tome)                            │
└─────────────────────────────┬───────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                            SERVICE                                  │
│                                                                     │
│   1. Vérifier connexion → Si offline → Erreur "Mode lecture"        │
│   2. Demander token Mangacollec à Auth API                          │
│   3. Appel API Mangacollec (POST /possessions)                      │
│   4. Attendre réponse avec ID                                       │
│   5. Sauvegarder en cache local (SQLite)                            │
│   6. Retourner succès                                               │
└─────────────────────────────────────────────────────────────────────┘
```

### 7.4 Exemple Service (API-first)

```python
class CollectionService:
    def __init__(self, api_client, cache):
        self._api = api_client
        self._cache = cache

    async def add_to_collection(self, volume_id: str) -> Possession:
        """Ajoute un tome - passe par l'API d'abord"""
        # 1. Vérifier la connexion
        if not self._api.is_online():
            raise OfflineError("Action impossible hors connexion")
        
        # 2. Appel API
        response = await self._api.post(
            "/users/me/possessions",
            json={"volume_id": volume_id}
        )
        
        if response.status_code != 201:
            raise ApiError(response.json())
        
        # 3. Récupérer les données avec l'ID généré par l'API
        data = response.json()
        possession = Possession(
            id=data["id"],
            user_id=data["user_id"],
            volume_id=data["volume_id"],
            created_at=data["created_at"]
        )
        
        # 4. Sauvegarder dans le cache local
        self._cache.possessions.save(possession)
        
        return possession

    async def remove_from_collection(self, volume_id: str) -> bool:
        """Retire un tome - passe par l'API d'abord"""
        if not self._api.is_online():
            raise OfflineError("Action impossible hors connexion")
        
        possession = self._cache.possessions.get_by_volume(volume_id)
        if not possession:
            return False
        
        response = await self._api.delete(
            f"/users/me/possessions/{possession.id}"
        )
        
        if response.status_code != 204:
            raise ApiError(response.json())
        
        self._cache.possessions.delete(possession.id)
        return True

    async def get_collection(self, force_refresh: bool = False) -> List[Possession]:
        """Récupère la collection"""
        if force_refresh or self._cache.possessions.is_stale():
            response = await self._api.get("/users/me/possessions")
            possessions = [self._map(p) for p in response.json()]
            self._cache.possessions.replace_all(possessions)
        
        return self._cache.possessions.get_all()
```

### 7.5 Cache Manager

```python
class CacheManager:
    def __init__(self, db_path: str):
        self._db = Database(db_path)
        self.possessions = PossessionCache(self._db)
        self.volumes = VolumeCache(self._db)
        self.series = SeriesCache(self._db)
        self.editions = EditionCache(self._db)

class PossessionCache:
    def __init__(self, db):
        self._db = db
        self._last_sync = None
        self._stale_after = timedelta(minutes=5)

    def is_stale(self) -> bool:
        if self._last_sync is None:
            return True
        return datetime.now() - self._last_sync > self._stale_after

    def save(self, possession: Possession):
        self._db.execute(
            "INSERT OR REPLACE INTO possessions VALUES (?, ?, ?, ?)",
            (possession.id, possession.user_id, possession.volume_id, possession.created_at)
        )

    def delete(self, possession_id: str):
        self._db.execute("DELETE FROM possessions WHERE id = ?", (possession_id,))

    def replace_all(self, possessions: List[Possession]):
        self._db.execute("DELETE FROM possessions")
        for p in possessions:
            self.save(p)
        self._last_sync = datetime.now()

    def get_all(self) -> List[Possession]:
        rows = self._db.execute("SELECT * FROM possessions").fetchall()
        return [self._map(row) for row in rows]

    def get_by_volume(self, volume_id: str) -> Optional[Possession]:
        row = self._db.execute(
            "SELECT * FROM possessions WHERE volume_id = ?",
            (volume_id,)
        ).fetchone()
        return self._map(row) if row else None
```

### 7.6 Schéma récapitulatif

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│   QML/UI    │────────►│  Controller │────────►│   Service   │
└─────────────┘         └─────────────┘         └──────┬──────┘
                                                       │
                                        ┌──────────────┴─────────────┐
                                        │                            │
                                        ▼                            ▼
                               ┌─────────────────┐          ┌─────────────────┐
                               │   API Client    │          │     Cache       │
                               │  (écriture +    │          │   (lecture)     │
                               │   lecture)      │          │                 │
                               └────────┬────────┘          └─────────────────┘
                                        │                            ▲
                                        │   Après succès API         │
                                        └────────────────────────────┘
                                              Update cache
```

---

## 8. Configuration du projet

### 8.1 Structure racine

```
booksync_app_qt/
├── .venv/
├── pyproject.toml
├── README.md
├── LICENSE
├── .gitignore
├── .env.example
│
├── src/
│   └── booksync_app_qt/
│       ├── __init__.py
│       ├── __main__.py
│       ├── app.py
│       ├── models/
│       ├── api/
│       ├── cache/
│       ├── services/
│       ├── controllers/
│       ├── viewmodels/
│       └── utils/
│
├── qml/
│   ├── main.qml
│   ├── Theme.qml
│   ├── components/
│   ├── layouts/
│   ├── pages/
│   └── utils/
│
├── resources/
│   ├── icons/
│   ├── fonts/
│   └── images/
│
├── tests/
│   ├── __init__.py
│   ├── conftest.py
│   ├── test_services/
│   ├── test_api/
│   └── test_cache/
│
└── scripts/
    ├── build.py
    └── generate_resources.py
```

### 8.2 pyproject.toml

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
requires-python = ">=3.11"
keywords = ["manga", "collection", "pyside6", "qml"]

dependencies = [
    "PySide6>=6.6.0",
    "httpx>=0.27.0",
    "sqlalchemy>=2.0.0",
    "pydantic>=2.5.0",
    "pydantic-settings>=2.1.0",
    "python-dotenv>=1.0.0",
    "aiosqlite>=0.19.0",
    "keyring>=24.3.0",
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
target-version = "py311"
line-length = 100
src = ["src", "tests"]

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "C4", "UP", "ARG", "SIM"]
ignore = ["E501", "B008"]

[tool.ruff.lint.isort]
known-first-party = ["booksync_app_qt"]

[tool.mypy]
python_version = "3.11"
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

### 8.3 .env.example

> Voir [COMMON.md § Variables d'environnement](./COMMON.md#5-variables-denvironnement) pour la liste complète.

### 8.4 Commandes utiles

```bash
# Installation avec uv (recommandé)
cd booksync_app_qt
uv sync

# Lancer l'application
uv run python -m booksync_app_qt
# ou après installation
booksync

# Linter
ruff check src/
ruff format src/

# Type checking
mypy src/

# Tests
pytest

# Build
pip install -e ".[build]"
python scripts/build.py
```

---

## Annexes

### Note sur la Wishlist

La wishlist est calculée dynamiquement à partir des `follow_editions` dont les volumes ne sont pas dans `possessions`. Pas de table dédiée en base de données.
