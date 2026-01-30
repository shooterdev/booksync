# Tasks: Collection MVP

## 1. Configuration projet

- [x] 1.1 Créer `pyproject.toml` avec dépendances PySide6, httpx, SQLAlchemy, Pydantic
- [x] 1.2 Créer `.env.example` avec variables API et cache
- [x] 1.3 Créer structure dossiers `src/booksync_app_qt/` (domain, application, infrastructure, presentation, utils)
- [x] 1.4 Créer `__init__.py` et `__main__.py` (entry point)

## 2. Domain Layer

- [x] 2.1 Créer entité `Volume` (`domain/entities/volume.py`)
- [x] 2.2 Créer entité `Edition` (`domain/entities/edition.py`)
- [x] 2.3 Créer entité `Series` (`domain/entities/series.py`)
- [x] 2.4 Créer entité `Publisher` (`domain/entities/publisher.py`)
- [x] 2.5 Créer entité `Possession` (`domain/entities/possession.py`)
- [x] 2.6 Créer port `VolumeRepository` (`domain/ports/volume_repository.py`)
- [x] 2.7 Créer port `PossessionRepository` (`domain/ports/possession_repository.py`)
- [x] 2.8 Créer exceptions domain (`domain/exceptions/`)

## 3. Infrastructure - Cache SQLite

- [x] 3.1 Créer connexion database async (`infrastructure/cache/database.py`)
- [x] 3.2 Créer modèles SQLAlchemy pour Volume, Edition, Series, Publisher, Possession
- [x] 3.3 Créer `VolumeCache` repository (`infrastructure/cache/repositories/volume_cache.py`)
- [x] 3.4 Créer `PossessionCache` repository (`infrastructure/cache/repositories/possession_cache.py`)
- [x] 3.5 Créer `CacheManager` pour coordonner les caches

## 4. Infrastructure - API Client

- [x] 4.1 Créer `HttpClient` base avec httpx async (`infrastructure/api/http_client.py`)
- [x] 4.2 Créer `MangacollecApi` client (`infrastructure/api/mangacollec_api.py`)
- [x] 4.3 Implémenter endpoints: GET /users/me/possessions, DELETE /users/me/possessions/{id}
- [x] 4.4 Gérer authentification token (simplifié pour dev: token en env var)

## 5. Application Layer

- [x] 5.1 Créer DTOs collection (`application/dtos/`)
- [x] 5.2 Créer `CollectionService` (`application/services/collection_service.py`)
- [x] 5.3 Implémenter `get_collection()` — récupère possessions avec volumes
- [x] 5.4 Implémenter `remove_from_collection(volume_id)` — supprime possession
- [x] 5.5 Implémenter tri par date/alphabétique
- [x] 5.6 Implémenter calcul progression par série

## 6. Presentation Layer - Controllers

- [x] 6.1 Créer `BaseController` avec gestion loading/error (`presentation/controllers/base_controller.py`)
- [x] 6.2 Créer `CollectionController` (`presentation/controllers/collection_controller.py`)
- [x] 6.3 Implémenter signaux: loadingChanged, errorChanged, collectionChanged
- [x] 6.4 Implémenter slots: refresh(), removeVolume(volumeId), setSortMode(mode)

## 7. Presentation Layer - ViewModels

- [x] 7.1 Créer `VolumeListModel` QAbstractListModel (`presentation/models/volume_list_model.py`)
- [x] 7.2 Définir roles: volumeId, title, number, imageUrl, seriesTitle, seriesProgress
- [x] 7.3 Implémenter méthodes: rowCount, data, roleNames, refresh

## 8. QML - Structure de base

- [x] 8.1 Créer `main.qml` avec QApplication setup
- [x] 8.2 Créer `Theme.qml` avec couleurs dark/light mode
- [x] 8.3 Créer `MainLayout.qml` avec structure principale
- [x] 8.4 Créer `SideBar.qml` avec navigation (Collection active)

## 9. QML - Composants Collection

- [x] 9.1 Créer `VolumeCard.qml` — carte affichant couverture, titre, numéro, progression série
- [x] 9.2 Créer `CollectionPage.qml` — grille de VolumeCards avec tri
- [x] 9.3 Implémenter swipe-to-delete ou bouton retrait
- [x] 9.4 Implémenter indicateur de chargement
- [x] 9.5 Implémenter message d'erreur avec retry

## 10. Application Entry Point

- [x] 10.1 Créer `app.py` avec QApplication, QQmlApplicationEngine
- [x] 10.2 Injecter dépendances (services, controllers, models)
- [x] 10.3 Enregistrer controllers dans le contexte QML
- [x] 10.4 Charger main.qml

## 11. Tests

- [x] 11.1 Tests unitaires `CollectionService`
- [x] 11.2 Tests unitaires `VolumeListModel`
- [x] 11.3 Tests intégration cache SQLite
- [x] 11.4 Créer `conftest.py` avec fixtures

## 12. Validation

- [ ] 12.1 Lancer l'application, vérifier affichage collection
- [ ] 12.2 Tester tri date/alphabétique
- [ ] 12.3 Tester retrait d'un tome
- [ ] 12.4 Tester mode offline (déconnexion réseau)
- [ ] 12.5 Vérifier logs console (pas d'erreurs QML/Qt)
