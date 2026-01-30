# Change: Implémenter la page News (Nouveautés)

## Why

La page News est la page d'accueil de l'application, permettant aux utilisateurs de découvrir les dernières sorties de mangas et coffrets. C'est une fonctionnalité V1 essentielle pour la découverte et l'engagement quotidien des utilisateurs.

## What Changes

- **Nouvelle page QML** : `NewsPage.qml` affichant une grille des dernières sorties
- **Nouveau controller** : `NewsController` pour gérer les données et actions
- **Nouveau service** : `NewsService` pour récupérer les nouveautés depuis l'API
- **Nouveaux DTOs** : `VolumeNewsDTO`, `BoxNewsDTO` pour les données de présentation
- **Nouveau modèle** : `NewsListModel` pour alimenter la grille QML
- **Extension du cache** : Support pour les données de news avec TTL approprié
- **Navigation** : Activation de l'entrée "Accueil" dans la SideBar

## Impact

- Affected specs: `news-page` (nouvelle capability)
- Affected code:
  - `qml/pages/news/NewsPage.qml` (nouveau)
  - `qml/layouts/SideBar.qml` (activation bouton Accueil)
  - `src/booksync_app_qt/presentation/controllers/news_controller.py` (nouveau)
  - `src/booksync_app_qt/presentation/models/news_list_model.py` (nouveau)
  - `src/booksync_app_qt/application/services/news_service.py` (nouveau)
  - `src/booksync_app_qt/application/dtos/news_dto.py` (nouveau)
  - `src/booksync_app_qt/infrastructure/api/mangacollec_api.py` (extension)
  - `src/booksync_app_qt/infrastructure/cache/` (extension)
  - `src/booksync_app_qt/app.py` (injection dépendances)

## Dependencies

- Endpoint API : `GET /v2/volumes/news` (documenté dans `docs/api/mangacollec/volumes.md`)
- Endpoint API : `GET /v2/publishers` (pour le filtre éditeur)
- Endpoint API : `GET /v2/users/me/collection` (pour indicateur "déjà possédé")

## Out of Scope

- Filtre par éditeur (sera ajouté dans une proposition ultérieure)
- Publicité native (`native_ad_volume_home_first`) - ignorée pour BookSync
