# Tasks: Catalog Extended (Search + Author/Publisher Details) V1

## 1. Service

- [ ] 1.1 Étendre `SearchService` dans `services/search_service.py`
  - Méthode `search_authors(query: str)` — appel API GET `/v2/authors` avec paramètre `name`
  - Méthode `search_publishers(query: str)` — appel API GET `/v2/publishers` avec paramètre `name`
  - Gestion pagination et erreurs réseau

- [ ] 1.2 Étendre `CatalogService` dans `services/catalog_service.py`
  - Méthode `get_author_detail(author_id)` — récupère données complètes auteur (nom, bio, toutes les œuvres)
  - Méthode `get_publisher_detail(publisher_id)` — récupère données complètes éditeur
  - Cache pour détails avec TTL 24h

- [ ] 1.3 Ajouter modèles domain pour `Author` et `Publisher`
  - `Author`: id, name, biography, role_count, series_list (avec rôles)
  - `Publisher`: id, name, status (active/closed), series_count, recent_releases, upcoming_releases

## 2. Présentation (ViewModel)

- [ ] 2.1 Créer `AuthorDetailViewModel` dans `viewmodels/author_detail_viewmodel.py`
  - Properties: `author` (Author object), `authorSeries` (list model des séries)
  - Méthode `load_author(author_id)` — récupère détails auteur
  - Signal `authorLoaded()`, `errorOccurred(message)`

- [ ] 2.2 Créer `PublisherDetailViewModel` dans `viewmodels/publisher_detail_viewmodel.py`
  - Properties: `publisher` (Publisher object), `publisherSeries` (list model), `recentReleases`, `upcomingReleases`
  - Méthode `load_publisher(publisher_id)` — récupère détails éditeur
  - Signal `publisherLoaded()`, `errorOccurred(message)`

- [ ] 2.3 Créer `AuthorListModel` dans `models/author_list_model.py`
  - Héritage QAbstractListModel
  - Rôles: `id`, `name`, `seriesCount`, `biographyExcerpt`

- [ ] 2.4 Créer `PublisherListModel` dans `models/publisher_list_model.py`
  - Héritage QAbstractListModel
  - Rôles: `id`, `name`, `status`, `seriesCount`

- [ ] 2.5 Étendre `SearchViewModel` dans `viewmodels/search_viewmodel.py`
  - Property `currentTab` (Titles|Authors|Publishers)
  - Méthode `search_authors(query)` — délègue au SearchService
  - Méthode `search_publishers(query)` — délègue au SearchService
  - Signal `tabChanged()`
  - Utiliser le même champ de recherche pour tous les onglets

## 3. Interface (QML)

- [ ] 3.1 Refactoriser `SearchPage.qml`
  - Ajouter `SubNavBar` avec 3 onglets : "Titres", "Auteurs", "Éditeurs"
  - Kepper le champ de recherche en haut (partagé pour tous les onglets)
  - Implémenter `StateGroup` ou `Loader` pour switcher entre les 3 vues de résultats
  - Afficher résultats pertinents selon l'onglet sélectionné

- [ ] 3.2 Créer `AuthorDetailPage.qml`
  - En-tête: photo/avatar, nom complet du auteur, biographie
  - Section "Œuvres" avec grille cartes séries
  - Chaque carte affiche: titre, couverture, rôle (scénariste/dessinateur), nombre tomes
  - Click sur série → navigation vers `SerieDetailPage`

- [ ] 3.3 Créer `PublisherDetailPage.qml`
  - En-tête: logo/nom éditeur, statut (Actif/Fermé), nombre de séries
  - Section "Dernières sorties" — grille des 6-8 derniers volumes
  - Section "Prochaines sorties" — grille des 6-8 prochains volumes
  - Section "Catalogue" — liste alphabétique complète de toutes les séries avec scroll vertical
  - Filtre alphabétique (A-Z) dans le catalogue

- [ ] 3.4 Créer `AuthorResultCard.qml` — carte résultat auteur
  - Avatar/photo, nom, nombre d'œuvres
  - Click → navigation vers `AuthorDetailPage`

- [ ] 3.5 Créer `PublisherResultCard.qml` — carte résultat éditeur
  - Logo/nom, statut, nombre de séries
  - Click → navigation vers `PublisherDetailPage`

## 4. Intégration

- [ ] 4.1 Ajouter liens dans `SerieDetailPage.qml`
  - Afficher noms auteurs (clic → `AuthorDetailPage`)
  - Afficher nom éditeur (clic → `PublisherDetailPage`)

- [ ] 4.2 Intégrer `AuthorDetailPage` et `PublisherDetailPage` dans `MainLayout.qml`
  - Navigation stack correcte (breadcrumbs)

- [ ] 4.3 Connecter clics depuis les fiches vers les pages détail (Série, Auteur, Éditeur)

## 5. Tests

- [ ] 5.1 Tests unitaires `SearchService` (extension)
  - Test `search_authors(query)` avec mock API
  - Test `search_publishers(query)` avec mock API
  - Test gestion erreur réseau et pagination

- [ ] 5.2 Tests unitaires `CatalogService` (extension)
  - Test `get_author_detail()` avec cache
  - Test `get_publisher_detail()` avec cache

- [ ] 5.3 Tests unitaires ViewModels
  - Test `AuthorDetailViewModel.load_author()`
  - Test `PublisherDetailViewModel.load_publisher()`
  - Test signaux et changements d'état

- [ ] 5.4 Tests modèles List
  - Test `AuthorListModel` et `PublisherListModel` avec données de test

## 6. Validation

- [ ] 6.1 Lancement application, vérification logs console (aucune erreur QML)
- [ ] 6.2 Recherche dans onglet "Titres" fonctionnelle (existante)
- [ ] 6.3 Recherche dans onglet "Auteurs" — résultats visibles et correctes
- [ ] 6.4 Recherche dans onglet "Éditeurs" — résultats visibles et correctes
- [ ] 6.5 Clic résultat auteur → navigation vers `AuthorDetailPage`
- [ ] 6.6 Clic résultat éditeur → navigation vers `PublisherDetailPage`
- [ ] 6.7 Fiche Auteur affiche toutes les œuvres avec rôles
- [ ] 6.8 Fiche Éditeur affiche dernières/prochaines sorties et catalogue alphabétique
- [ ] 6.9 Navigation cross-links (Série → Auteur/Éditeur) fonctionnelle
- [ ] 6.10 Responsive design sur 1280x720
- [ ] 6.11 Capture screenshot des fiches pour documentation
