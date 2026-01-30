# Change: Implémenter la fonctionnalité Collection du MVP

## Why

L'application BookSync n'a actuellement aucune implémentation. La fonctionnalité Collection est le **cœur du MVP** — elle permet aux utilisateurs de visualiser et gérer leur collection de mangas (tomes possédés). Sans cette fonctionnalité, l'application n'a pas de valeur utilisateur.

## What Changes

### Nouveaux fichiers/composants

**Infrastructure de base (prérequis):**
- Configuration projet (`pyproject.toml`, `.env.example`)
- Entités Domain (`Volume`, `Edition`, `Series`, `Publisher`, `Possession`)
- Ports/Interfaces (`VolumeRepository`, `ApiClient`)
- Client HTTP async (`http_client.py`)
- Cache SQLite (`database.py`, modèles SQLAlchemy, repositories)
- Client API Mangacollec (`mangacollec_api.py`)

**Fonctionnalité Collection:**
- `CollectionService` — logique métier pour gérer les possessions
- `CollectionController` — QObject exposé au QML
- `VolumeListModel` — QAbstractListModel pour afficher les volumes
- Composants QML: `CollectionPage.qml`, `VolumeCard.qml`
- Navigation de base: `MainLayout.qml`, `SideBar.qml`

### Dépendances

Cette fonctionnalité nécessite une base d'infrastructure minimale pour fonctionner:
- Connexion à l'API Mangacollec pour récupérer la collection
- Cache local SQLite pour les performances et le mode offline lecture
- Système de thème (clair/sombre) pour la charte graphique

## Impact

- **Affected specs**: Nouvelle capability `collection` (aucune spec existante)
- **Affected code**:
  - `booksync_app_qt/src/` — structure complète à créer
  - `booksync_app_qt/qml/` — structure complète à créer
- **Breaking changes**: Aucun (projet vierge)

## Scope

Cette proposition couvre uniquement la **vue Collection** du MVP:
- Affichage de tous les tomes possédés
- Progression par série (X/Y tomes)
- Tri par date d'ajout ou alphabétique
- Retrait de tomes de la collection (via swipe ou action)

**Hors scope** (autres proposals):
- Recherche et ajout de tomes (nécessite SearchService + CatalogService)
- Fiches détail Volume/Série/Édition
- Authentification complète (simplifiée ici pour le développement)
