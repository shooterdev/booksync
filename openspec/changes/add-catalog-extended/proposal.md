# Change: Ajout de la recherche étendue et fiches Auteur/Éditeur (V1)

## Why

La fonctionnalité de recherche du MVP ne couvre que les titres. Pour offrir une expérience de découverte complète, les utilisateurs doivent pouvoir rechercher par auteurs et éditeurs, et accéder à des fiches détaillées pour explorer toutes les œuvres d'un créateur ou un catalogue d'un éditeur. Cette fonctionnalité enrichit la navigation et aide à la découverte de mangas.

## What Changes

### Nouveaux fichiers/composants

**Services:**
- Extension `SearchService` — nouvelles méthodes `search_authors()` et `search_publishers()`
- Extension `CatalogService` — méthodes `get_author_detail()` et `get_publisher_detail()`

**Présentation:**
- `AuthorDetailViewModel` — gestion données fiche auteur
- `PublisherDetailViewModel` — gestion données fiche éditeur
- `AuthorListModel` — QAbstractListModel pour résultats auteurs
- `PublisherListModel` — QAbstractListModel pour résultats éditeurs
- Composants QML: `SearchPage.qml` (refactorisé avec onglets), `AuthorDetailPage.qml`, `PublisherDetailPage.qml`

**Intégration:**
- Navigation depuis résultats recherche vers fiches Auteur/Éditeur
- Navigation depuis fiches Série vers fiches Auteur et Éditeur (cross-links)

## Impact

- **Affected specs**: Extension de `search` (nouvelle spec `catalog-extended`)
- **Affected code**:
  - `booksync_app_qt/src/services/` — extension SearchService et CatalogService
  - `booksync_app_qt/src/viewmodels/` — AuthorDetailViewModel, PublisherDetailViewModel
  - `booksync_app_qt/src/models/` — AuthorListModel, PublisherListModel
  - `booksync_app_qt/qml/pages/` — SearchPage.qml (refactorisation), AuthorDetailPage.qml, PublisherDetailPage.qml
  - `SerieDetailPage.qml` — ajout liens vers fiches auteur/éditeur
- **Breaking changes**: Léger refactorisation SearchPage.qml pour ajouter onglets

## Scope

Cette proposition couvre:
- Recherche par auteurs (API Mangacollec)
- Recherche par éditeurs (API Mangacollec)
- Onglets dans SearchPage : Titres | Auteurs | Éditeurs
- Fiche Auteur : nom, œuvres avec rôles (scénariste, dessinateur)
- Fiche Éditeur : nom, statut, dernières/prochaines sorties, catalogue complet
- Navigation cross-links (Série→Auteur/Éditeur, résultats→fiches)

**Hors scope** (futures versions):
- Historique recherches
- Recherche avancée (combinaisons filtres)
- Filtres par genre/statut dans les fiches
