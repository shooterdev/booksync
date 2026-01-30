# BookSync App — Spécification Technique

Application desktop de gestion de collection manga en **PySide6 / QML**,
inspirée de [mangacollec.com](https://www.mangacollec.com).

> **Note** : Ce document est aligné avec [ARCHITECTURE.md](./ARCHITECTURE.md) qui décrit l'architecture technique déployée.

---

## Table des matières

1. [Vision et objectif](#1-vision-et-objectif)
2. [Fonctionnalités](#2-fonctionnalités)
3. [Modèle de données](#3-modèle-de-données) (§3.7 Store local, §3.8 Schéma relationnel)
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

L'application fonctionne en architecture distribuée : Raspberry Pi (frontend) + API Mangacollec (catalogue) + Serveur/NAS (APIs locales).

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

| #   | Fonctionnalité            | Description                                                                          |
|-----|---------------------------|--------------------------------------------------------------------------------------|
| 2.1 | **Pile à lire**           | Progression de lecture par série (lus/possédés), stats globales, pause/terminer      |
| 2.2 | **Collection**            | Vue de tous les tomes possédés, progression par série, tri (date_ajout/alphabétique) |
| 2.3 | **Compléter**             | Détection automatique des tomes manquants dans les séries incomplètes                |
| 2.4 | **Envies**                | Liste de souhaits pour planifier les achats futurs                                   |
| 2.5 | **Prêts**                 | Suivi des volumes prêtés (à qui, où) avec formulaire de création                     |
| 2.6 | **Statistiques**          | Dashboard : répartition par éditeur/genre (graphiques), derniers ajouts/lectures     |
| 2.7 | **Historique Collection** | Journal chronologique des ajouts (par année/mois)                                    |
| 2.8 | **Historique Lecture**    | Journal des lectures effectuées (par semaine/mois/année)                             |

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
| 4  | **Recherche globale** | Recherche par Titres, Auteurs, Éditeurs, volumes par isbn (sur données api)           |
| 7  | **Fiche Volume**      | Couverture, navigation inter-tomes, actions, stats communautaires, prix, résumé, ISBN |
| 8  | **Fiche Série**       | Type, genres, auteurs avec rôles, liste des éditions                                  |
| 9  | **Fiche Édition**     | Lien série/éditeur, stats (parus/à paraître), statut, grille des tomes                |
| 10 | **Fiche Auteur**      | Nom, grille de toutes ses œuvres avec rôle                                            |
| 11 | **Fiche Publisher**   | Dernières/prochaines sorties, catalogue (Series) complet alphabétique                 |

#### Autres

| #  | Fonctionnalité          | Description                                                             |
|----|-------------------------|-------------------------------------------------------------------------|
| 5  | **Panier**              | Volumes à acheter, calcul du budget, précommandes, redirection marchand |
| 6  | **Paramètres**          | Profil, préférences (thème, images adultes), multi-utilisateur          |
| 23 | **Scanner code-barres** | Douchette Bluetooth (ISBN) + webcam (OpenCV/pyzbar) pour ajout rapide   |
| 24 | **Multi-utilisateur**   | Profils séparés pour usage familial                                     |
| 25 | **Thème clair/sombre**  | Basculement avec charte graphique cohérente                             |

### 2.2 Composants UI détaillés par onglet

Description des widgets et composants principaux utilisés dans chaque section de l'application.

#### Pile à Lire

| Composant                | Description                                                                                           |
|--------------------------|-------------------------------------------------------------------------------------------------------|
| `StatusRead`             | Barre de statut affichant le nombre de volumes lus / possédés et la progression globale               |
| `SerieReadListView`      | Liste des séries en cours de lecture avec progression individuelle                                    |
| `SerieReadCard`          | Ligne d'une série avec couverture, titre, barre de progression (lus/possédés), actions pause/terminer |

#### Collection

| Composant                        | Description                                                                             |
|----------------------------------|-----------------------------------------------------------------------------------------|
| `StatusCollection`               | Barre de statut avec compteurs (volumes possédés, éditions possédées)                   |
| `EditionListView`                | Liste des éditions possédées avec couverture des volumes possédées                      |
| `ItemEditionCollection`          | Carte d'une édition avec couverture, titre série, éditeur, progression (possédés/parus) |

#### Compléter

| Composant                   | Description                                                         |
|-----------------------------|---------------------------------------------------------------------|
| `StatusCollection`          | Réutilisé — compteurs avec focus sur les tomes manquants            |
| `EditionListView`           | Grille des éditions incomplètes avec indication des tomes manquants |

#### Envies

| Composant                   | Description                                                         |
|-----------------------------|---------------------------------------------------------------------|
| `StatusCollection`          | Réutilisé — compteurs des éditions suivies non possédées            |
| `EditionListView`           | Grille des éditions de la wishlist avec tomes disponibles à l'achat |

#### Prêts

| Composant              | Description                                                  |
|------------------------|--------------------------------------------------------------|
| `BorrowerListView`     | Liste des emprunteurs avec nombre de volumes prêtés          |
| `BorrowerRow`          | Ligne emprunteur avec nom, catégorie, nombre de prêts actifs |
| `LoanVolumeRow`        | Ligne d'un volume prêté avec couverture, titre, date de prêt |
| `CreateBorrowerButton` | Bouton d'ajout d'un nouvel emprunteur                        |
| `CreateLoanButton`     | Bouton de création d'un nouveau prêt                         |
| `ReturnLoanButton`     | Bouton de retour d'un volume prêté                           |

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

> **Note** : Cette table est stockée sur le serveur (PostgreSQL) et accessible via la **Data API locale** (port 8001).
> Ces données sont absentes de l'API Mangacollec et alimentées par scraping (BubbleBD).

| Champ        | Type       | Description           |
|--------------|------------|-----------------------|
| `volume_id`  | uuid_v4    | PK, FK → volumes      |
| `object_id`  | String(50) | ID BubbleBD           |
| `nb_pages`   | Integer    | Nombre de pages       |
| `length`     | String(20) | Longueur (cm)         |
| `height`     | String(20) | Hauteur (cm)          |
| `width`      | String(20) | Largeur (cm)          |
| `weight`     | String(20) | Poids (g)             |
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
| `id`                      | uuid_v4     | Clé primaire                |
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
| `id`                           | uuid_v4     | Clé primaire                |
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
| `id`                           | uuid_v4     | Clé primaire                |
| `cart_id`                      | uuid_v4     | FK → carts                  |
| `box_id`                       | uuid_v4     | FK → boxes                  |
| `quantity`                     | Integer     | Quantité                    |
| `created_at`                   | DateTime    | Date d'ajout                |
| `store_cart_item_id`           | String(100) | ID côté boutique (nullable) |
| `store_cart_item_price`        | String(20)  | Prix formaté                |
| `store_cart_item_link`         | Text        | Lien produit boutique       |
| `store_cart_item_availability` | String(100) | Disponibilité               |

### 3.7 Structure du store local

Le cache local (`booksync-store.json`) reproduit les données de l'API Mangacollec dans un format normalisé 
pour un accès rapide et un fonctionnement hors-ligne en lecture seule. 
Le fichier de référence se trouve dans `docs/booksync_store.json`.

> Voir [ARCHITECTURE.md § Structure du store local](./ARCHITECTURE.md#structure-du-store-local-booksync-storejson) 
> pour la documentation technique complète (pattern normalisé, entités, structures spécifiques).

**Correspondance modèle de données ↔ clés du store :**

| Table (PRD §3.1-3.6)                     | Clé store           | Pattern    |
|------------------------------------------|---------------------|------------|
| Volume (`volumes`)                       | `volumes`           | normalisé  |
| Edition (`editions`)                     | `editions`          | normalisé  |
| Series (`series`)                        | `series`            | normalisé  |
| Task (`tasks`)                           | `tasks`             | normalisé  |
| Author (`authors`)                       | `authors`           | normalisé  |
| Publisher (`publishers`)                 | `publishers`        | normalisé  |
| Type (`types`)                           | `types`             | normalisé  |
| Job (`jobs`)                             | `jobs`              | normalisé  |
| Kind (`kinds`)                           | `kinds`             | normalisé  |
| BoxEdition (`box_editions`)              | `boxEditions`       | normalisé  |
| Box (`boxes`)                            | `boxes`             | normalisé  |
| BoxVolume (`box_volumes`)                | `boxVolumes`        | normalisé  |
| FollowEdition (`follow_editions`)        | `followEditions`    | normalisé  |
| Possession (`possessions`)               | `possessions`       | normalisé  |
| BoxFollowEdition (`box_follow_editions`) | `boxFollowEditions` | normalisé  |
| BoxPossession (`box_possessions`)        | `boxPossessions`    | normalisé  |
| ReadEdition (`read_editions`)            | `readEditions`      | normalisé  |
| Read (`reads`)                           | `reads`             | normalisé  |
| Borrower (`borrowers`)                   | `borrowers`         | normalisé  |
| Loan (`loans`)                           | `loans`             | normalisé  |
| User (`users`)                           | `user`              | objet plat |
| Cart (`carts`)                           | `cart`              | objet plat |

> **Note** : Les tables `SeriesKind`, `VolumeExtra`, `CartItem` et `CartBoxItem` n'ont pas de clé store dédiée.
> `SeriesKind` est embarqué dans `kinds.series_ids` et `VolumeExtra` provient de la Data API locale,
> et les items du panier sont inclus dans `cart.items` / `cart.box_items`.

> Voir [ARCHITECTURE.md § Cache local TinyDB](./ARCHITECTURE.md#54-cache-local-tinydb) pour les clés store additionnelles (offres, polls, news, planning, etc.).

### 3.8 Schéma relationnel

```
┌─────────────┐       ┌─────────────┐       ┌─────────────┐
│   Author    │──M:N──│    Series   │──M:N──│    Kind     │
└─────────────┘       └──────┬──────┘       └─────────────┘
                             │ 1:N
                             ▼
┌─────────────┐       ┌─────────────┐
│  Publisher  │──1:N──│   Edition   │◄──────┐ (édition parent)
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

> Voir [COMMON.md § Structure des écrans QML](./COMMON.md#12-structure-des-écrans-qml) pour l'arborescence complète.

### 4.2 Navigation

> Voir [COMMON.md § Navigation](./COMMON.md#13-navigation) pour le schéma complet.

### 4.3 Navigation entre fiches

> Voir [COMMON.md § Navigation](./COMMON.md#13-navigation) pour la navigation inter-fiches.

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
| —   | Cache local (TinyDB)                  | Performance + lecture hors-ligne   |

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

| #   | Fonctionnalité                     | Justification               |
|-----|------------------------------------|-----------------------------|
| 2.1 | Pile à lire                        | Suivre sa progression       |
| 2.6 | Statistiques de collection         | Visualisation (graphiques)  |
| 2.7 | Historique de la collection        | Journal des ajouts          |
| 2.8 | Historique de lecture              | Journal des lectures        |
| 3.3 | Planning nouveautés (T1)           | Découverte nouvelles séries |
| 3.4 | Planning coffrets                  | Éditions spéciales          |
| 5   | Panier d'achats                    | Gestion des achats intégrée |
| 23  | Scanner code-barres ⚠️ Planifié V2 | Ajout rapide                |

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

### 6.1 Architecture Hybride Clean + MVVM

> Voir [COMMON.md § Architecture Hybride Clean + MVVM](./COMMON.md#8-architecture-hybride-clean--mvvm) pour le schéma complet.

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
│   │   │   ├── auth_service.py        # MVP - Authentification
│   │   │   ├── sync_service.py        # MVP - Synchronisation API/Cache
│   │   │   ├── collection_service.py  # MVP - Possessions
│   │   │   ├── search_service.py      # MVP - Recherche (V1: +auteurs/éditeurs)
│   │   │   ├── catalog_service.py     # MVP - Fiches Volume/Série/Édition
│   │   │   ├── planning_service.py    # V1  - Sorties à venir
│   │   │   ├── wishlist_service.py    # V1  - Envies
│   │   │   ├── reading_service.py     # V2  - Pile à lire
│   │   │   ├── stats_service.py       # V2  - Statistiques
│   │   │   ├── history_service.py     # V2  - Historique
│   │   │   ├── cart_service.py        # V2  - Panier
│   │   │   ├── scanner_service.py     # V2  - Code-barres
│   │   │   ├── prediction_service.py  # V3  - Recommandations
│   │   │   ├── loan_service.py        # V4+ - Prêts
│   │   │   └── user_service.py        # V4+ - Multi-utilisateur
│   │   └── dtos/
│   │       ├── volume_dto.py
│   │       └── ...
│   │
│   ├── infrastructure/          # Implémentations concrètes
│   │   ├── api/
│   │   │   ├── http_client.py      # Client httpx
│   │   │   ├── mangacollec_api.py  # API externe (catalogue, OAuth2)
│   │   │   ├── auth_api.py         # API locale (multi-user)
│   │   │   ├── data_api.py         # API locale (volume_extra)
│   │   │   └── prediction_api.py   # API locale V3 (recommandations)
│   │   ├── cache/
│   │   │   ├── store.py            # TinyDB store
│   │   │   └── repositories/
│   │   │       ├── volume_cache.py
│   │   │       ├── series_cache.py
│   │   │       └── ...
│   │   ├── images/
│   │   │   └── image_cache.py      # Cache couvertures
│   │   ├── scanner/
│   │   │   ├── bluetooth_scanner.py  # Douchette Bluetooth
│   │   │   └── webcam_scanner.py     # OpenCV + pyzbar
│   │
│   ├── presentation/            # Interface Qt
│   │   ├── viewmodels/          # QObject/ViewModels exposés au QML
│   │   │   ├── base_viewmodel.py
│   │   │   ├── collection_viewmodel.py
│   │   │   ├── search_viewmodel.py
│   │   │   ├── planning_viewmodel.py
│   │   │   ├── prediction_viewmodel.py  # V3
│   │   │   └── ...
│   │   └── models/              # QAbstractListModel
│   │       ├── volume_list_model.py
│   │       ├── series_list_model.py
│   │       └── ...
│   │
│   ├── qml/                     # Interface utilisateur
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

### 6.4 Exemple ViewModel

```python
from PySide6.QtCore import QObject, Signal, Slot, Property

class CollectionViewModel(QObject):
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

### 6.5 Exemple ListModel

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
    model: collectionViewModel.volumes
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

### 6.7 Mapping Services ↔ Versions

Cette section définit quels services doivent être développés pour chaque version.

#### MVP — Services Core

| Service               | Fichier                 | Responsabilités                                         |
|-----------------------|-------------------------|---------------------------------------------------------|
| **AuthService**       | `auth_service.py`       | Authentification, gestion tokens, connexion Mangacollec |
| **SyncService**       | `sync_service.py`       | Synchronisation API ↔ Cache, gestion hors-ligne         |
| **CollectionService** | `collection_service.py` | Possessions (ajouter/retirer tomes)                     |
| **SearchService**     | `search_service.py`     | Recherche titres (V1 : + auteurs/éditeurs)              |
| **CatalogService**    | `catalog_service.py`    | Lecture fiches Volume/Série/Édition                     |

#### V1 — Services Quotidiens

| Service             | Fichier               | Responsabilités                                  |
|---------------------|-----------------------|--------------------------------------------------|
| **PlanningService** | `planning_service.py` | Sorties à venir, planning personnalisé/global    |
| **WishlistService** | `wishlist_service.py` | Envies (calcul dynamique depuis follow_editions) |
| **SearchService**   | `search_service.py`   | Extension : recherche auteurs + éditeurs         |

#### V2 — Services Lecture & Avancés

| Service            | Fichier              | Responsabilités                                      |
|--------------------|----------------------|------------------------------------------------------|
| **ReadingService** | `reading_service.py` | Pile à lire, progression, marquer comme lu           |
| **StatsService**   | `stats_service.py`   | Statistiques collection (graphiques, répartitions)   |
| **HistoryService** | `history_service.py` | Historique ajouts + lectures (journal chronologique) |
| **CartService**    | `cart_service.py`    | Panier d'achats, calcul budget, sync boutiques       |
| **ScannerService** | `scanner_service.py` | Lecture code-barres ISBN, ajout rapide               |

#### V3 — Services Recommandation

| Service               | Fichier                 | Responsabilités                                          |
|-----------------------|-------------------------|----------------------------------------------------------|
| **PredictionService** | `prediction_service.py` | QCM préférences, sélection humeur, algorithme suggestion |

#### V4+ — Services Hors-périmètre

| Service         | Fichier           | Responsabilités                        |
|-----------------|-------------------|----------------------------------------|
| **LoanService** | `loan_service.py` | Gestion des prêts (emprunteurs, suivi) |
| **UserService** | `user_service.py` | Multi-utilisateur, profils séparés     |

#### Schéma de dépendances

```
MVP:
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ AuthService │────►│ SyncService │────►│ CacheManager│
│ (OAuth2)    │     └──────┬──────┘     │  (TinyDB)   │
└─────────────┘            │            └─────────────┘
                           │
         ┌─────────────────┼─────────────────┐
         ▼                 ▼                 ▼
┌─────────────┐     ┌─────────────┐   ┌─────────────┐
│ Collection  │     │   Search    │   │  Catalog    │
│   Service   │     │   Service   │   │  Service    │
└─────────────┘     └─────────────┘   └─────────────┘

V1:                 V2:                 V3:
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Planning   │     │  Reading    │     │ Prediction  │
│  Service    │     │  Service    │     │  Service    │
├─────────────┤     ├─────────────┤     └─────────────┘
│  Wishlist   │     │   Stats     │
│  Service    │     │  Service    │
└─────────────┘     ├─────────────┤
                    │  History    │
                    │  Service    │
                    ├─────────────┤
                    │   Cart      │
                    │  Service    │
                    ├─────────────┤
                    │  Scanner    │
                    │  Service    │
                    └─────────────┘
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

> Voir [ARCHITECTURE.md § Stratégie de synchronisation](./ARCHITECTURE.md#52-stratégie-de-synchronisation) pour les diagrammes détaillés des flux d'écriture et de lecture.

### 7.4 Exemple Service (API-first)

```python
class CollectionService:
    def __init__(self, api_client, cache):
        self._api = api_client
        self._cache = cache

    async def add_to_collection(self, volume_id: str) -> Possession:
        """Ajoute un tome — POST /v1/possessions_multiple"""
        # 1. Vérifier la connexion
        if not self._api.is_online():
            raise OfflineError("Action impossible hors connexion")

        # 2. Appel API (body = tableau d'objets volume_id)
        response = await self._api.post(
            "v1/possessions_multiple",
            json=[{"volume_id": volume_id}]
        )

        if response.status_code != 200:
            raise ApiError(response.json())

        # 3. Réponse : { possessions: [...], follow_editions: [...] }
        data = response.json()
        possession_data = data["possessions"][0]

        possession = Possession(
            id=possession_data["id"],
            user_id=possession_data["user_id"],
            volume_id=possession_data["volume_id"],
            created_at=possession_data["created_at"]
        )

        # 4. Sauvegarder dans le cache local
        self._cache.possessions.save(possession)

        # 5. Mettre à jour les follow_editions si présentes
        for fe_data in data.get("follow_editions", []):
            self._cache.follow_editions.save(self._map_follow_edition(fe_data))

        return possession

    async def remove_from_collection(self, volume_id: str) -> bool:
        """Retire un tome — DELETE /v1/possessions_multiple"""
        if not self._api.is_online():
            raise OfflineError("Action impossible hors connexion")

        possession = self._cache.possessions.get_by_volume(volume_id)
        if not possession:
            return False

        # Body = tableau d'objets id
        response = await self._api.delete(
            "v1/possessions_multiple",
            json=[{"id": possession.id}]
        )

        # Réponse 200 avec { possessions: [{id, deleted}], follow_editions: [...], loans: [] }
        if response.status_code != 200:
            raise ApiError(response.json())

        self._cache.possessions.delete(possession.id)
        return True

    async def get_collection(self, force_refresh: bool = False) -> Collection:
        """Récupère la collection complète — GET /v2/users/me/collection"""
        if force_refresh or self._cache.is_stale():
            response = await self._api.get("v2/users/me/collection")
            data = response.json()
            # Réponse complète : editions, series, types, kinds, volumes,
            # possessions, follow_editions, reads, borrowers, loans, etc.
            self._cache.replace_all_from_collection(data)

        return self._cache.get_collection()
```

### 7.5 Cache Manager

```python
class CacheManager:
    def __init__(self, store_path: str):
        self._db = TinyDB(config.store_path)
        self.possessions = PossessionCache(self._db.table("possessions"))
        self.volumes = VolumeCache(self._db.table("volumes"))
        self.series = SeriesCache(self._db.table("series"))
        self.editions = EditionCache(self._db.table("editions"))

class PossessionCache:
    def __init__(self, table):
        self._table = table
        self._last_sync = None
        self._stale_after = timedelta(minutes=5)

    def is_stale(self) -> bool:
        if self._last_sync is None:
            return True
        return datetime.now() - self._last_sync > self._stale_after

    def save(self, possession: Possession):
        self._table.upsert(
            possession.model_dump(),
            where("id") == possession.id
        )

    def delete(self, possession_id: str):
        self._table.remove(where("id") == possession_id)

    def replace_all(self, possessions: List[Possession]):
        self._table.truncate()
        for p in possessions:
            self.save(p)
        self._last_sync = datetime.now()

    def get_all(self) -> List[Possession]:
        return [self._map(doc) for doc in self._table.all()]

    def get_by_volume(self, volume_id: str) -> Optional[Possession]:
        docs = self._table.search(where("volume_id") == volume_id)
        return self._map(docs[0]) if docs else None
```

### 7.6 Schéma récapitulatif

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│   QML/UI    │────────►│  ViewModel  │────────►│   Service   │
└─────────────┘         └─────────────┘         └──────┬──────┘
                                                       │
                                        ┌──────────────┴─────────────┐
                                        │ (écriture / lecture)       │ (écriture / lecture)
                                        ▼                            ▼
                               ┌─────────────────┐          ┌─────────────────┐
                               │  API Client     │          │  Cache          │
                               │   - écriture    │          │   - écriture    │
                               │   - lecture     │          │   - lecture     │
                               └────────┬────────┘          └─────────────────┘
                                        │                            ▲
                                        │   Après succès API         │ (écriture)
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
│
├── qml/
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

> Voir [COMMON.md § Configuration du projet](./COMMON.md#14-configuration-du-projet-pyprojecttoml) pour le fichier complet.

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
