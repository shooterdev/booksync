# Change: Ajouter les sections Collection avec sous-navigation

## Why

La page Collection actuelle affiche uniquement la liste des volumes possédés. Le PRD définit **8 sous-sections** pour la Collection avec une barre de sous-navigation contextuelle :

- Pile à lire (V2)
- Collection (existant)
- Compléter (V1)
- Envies (V1)
- Prêts (V4+)
- Statistiques (V2)
- Historique Collection (V2)
- Historique Lecture (V2)

Cette proposition implémente l'infrastructure de navigation et les sections **V1** (Compléter, Envies).

## What Changes

### Nouveaux fichiers/composants

**Infrastructure de navigation:**
- `SubNavBar.qml` — Composant réutilisable de sous-navigation contextuelle
- `CollectionContainer.qml` — Conteneur avec sous-navigation pour les sections Collection

**Pages V1:**
- `CompletePage.qml` — Affichage des tomes manquants dans les séries incomplètes
- `EnviesPage.qml` — Liste de souhaits (wishlist) depuis les éditions suivies

**Services:**
- `WishlistService` — Calcul dynamique des envies depuis `follow_editions`
- Extension de `CollectionService` — Détection des tomes manquants

**Controllers:**
- `WishlistController` — Expose les envies au QML
- `CompleteController` — Expose les tomes manquants au QML

**Models:**
- `WishlistModel` — QAbstractListModel pour les envies
- `MissingVolumeModel` — QAbstractListModel pour les tomes manquants

### Dépendances

- API Mangacollec : `GET /users/me/follow_editions` pour les éditions suivies
- Cache local : Tables `follow_editions`, `editions`, `volumes`

## Impact

- **Affected specs**: Nouvelle capability `collection-sections`
- **Affected code**:
  - `booksync_app_qt/qml/layouts/` — Ajout SubNavBar
  - `booksync_app_qt/qml/pages/collection/` — Restructuration avec Container
  - `booksync_app_qt/src/` — Nouveaux services, controllers, models
- **Breaking changes**: La navigation vers Collection passera par CollectionContainer

## Scope

**Inclus (V1):**
- SubNavBar réutilisable
- CollectionContainer avec navigation entre sections
- Page Compléter (tomes manquants)
- Page Envies (wishlist)

**Hors scope (V2+):**
- Pile à lire (nécessite ReadingService)
- Statistiques (nécessite StatsService + graphiques)
- Historiques (nécessite HistoryService)
- Prêts (V4+)
