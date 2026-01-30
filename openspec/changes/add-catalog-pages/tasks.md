# Tasks: Add Catalog Pages

## 1. Backend Services

- [ ] 1.1 Créer CatalogService (catalog_service.py) - encapsuler les appels aux endpoints de détail
- [ ] 1.2 Implémenter get_volume(volume_id) - retourner les infos volume (titre, numéro, ISBN, résumé, ASIN, statistiques)
- [ ] 1.3 Implémenter get_series(series_id) - retourner infos série (type, genres, auteurs avec rôles, liste éditions)
- [ ] 1.4 Implémenter get_edition(edition_id) - retourner infos édition (éditeur, statut, nombre tomes, liste tomes)
- [ ] 1.5 Ajouter la logique de cache TinyDB pour les 3 méthodes (charger cache en premier, API secondaire)
- [ ] 1.6 Ajouter la gestion des erreurs (timeouts, 404, données manquantes)

## 2. Frontend - ViewModels

- [ ] 2.1 Créer VolumeDetailViewModel (volume_detail_viewmodel.py)
  - Properties: volume_id, title, number, isbn, summary, asin, possessions_count, is_owned, loading, error_message
  - Signals: on_volume_loaded, on_error
  - Methods: load_volume(), add_to_collection(), remove_from_collection()

- [ ] 2.2 Créer SeriesDetailViewModel (series_detail_viewmodel.py)
  - Properties: series_id, title, type, genres_list, authors_list, editions_list, loading
  - Signals: on_series_loaded, on_editions_loaded
  - Methods: load_series(), get_editions_model()

- [ ] 2.3 Créer EditionDetailViewModel (edition_detail_viewmodel.py)
  - Properties: edition_id, title, publisher, status, total_volumes, volumes_released, tomes_model, loading
  - Signals: on_edition_loaded
  - Methods: load_edition(), get_tomes_grid_model()

- [ ] 2.4 Créer AuthorsModel (QAbstractListModel) pour afficher auteurs avec rôles (nom, rôle comme "scénariste", "illustrateur")
- [ ] 2.5 Créer TomesGridModel (QAbstractListModel) pour grille des tomes avec badges de possession

## 3. Frontend - UI Components

- [ ] 3.1 Créer VolumeDetailPage.qml
  - Afficher couverture plein écran (ou placeholder)
  - Afficher titre, numéro, date sortie, ISBN, résumé scrollable
  - Afficher prix Amazon (ASIN) et statistiques communautaires
  - Boutons "Ajouter à ma collection" / "Retirer de ma collection"
  - Flèches "Tome précédent" / "Tome suivant" pour navigation inter-tomes
  - Bouton "Retour"

- [ ] 3.2 Créer SeriesDetailPage.qml
  - Afficher titre, type (manga/manhwa/etc)
  - Afficher genres sous forme de tags
  - Afficher liste des auteurs avec rôles (vertical ou horizontal)
  - Afficher liste scrollable des éditions (avec éditeur, statut, nombre tomes)
  - Clic sur édition → navigation vers EditionDetailPage
  - Bouton "Retour"

- [ ] 3.3 Créer EditionDetailPage.qml
  - Afficher titre édition, éditeur, statut
  - Afficher statistiques "X volumes parus, Y à paraître"
  - Afficher grille des tomes (3-4 colonnes max) avec numéros
  - Badges verts "Possédé" sur les tomes de l'utilisateur
  - Clic sur tome → navigation vers VolumeDetailPage
  - Bouton "Retour"

- [ ] 3.4 Créer composants réutilisables:
  - VolumeCard.qml (affichage compact d'un volume)
  - AuthorRow.qml (affichage auteur + rôle)
  - EditionRow.qml (affichage édition dans liste)
  - TomeGridCell.qml (affichage tome dans grille)

## 4. Navigation & Integration

- [ ] 4.1 Configurer StackView dans MainLayout.qml pour gérer la pile des pages de détail
- [ ] 4.2 Implémenter les transitions push/pop avec bouton retour automatique
- [ ] 4.3 Créer les signaux de navigation (navigate_to_series(id), navigate_to_edition(id), navigate_to_volume(id))
- [ ] 4.4 Intégrer CatalogPages dans le flux de navigation (SearchPage → SeriesDetailPage, etc)

## 5. Collection Management Integration

- [ ] 5.1 Intégrer CollectionService pour les actions ajouter/retirer volume
- [ ] 5.2 Implémenter le feedback visuel (confirmation, loader, message erreur)
- [ ] 5.3 Synchroniser l'état de possession avec le backend

## 6. Testing

- [ ] 6.1 Créer tests unitaires CatalogService (mocking API, cache TinyDB)
- [ ] 6.2 Créer tests unitaires VolumeDetailViewModel (load, add/remove collection)
- [ ] 6.3 Créer tests unitaires SeriesDetailViewModel (load, éditions)
- [ ] 6.4 Créer tests unitaires EditionDetailViewModel (load, grille tomes)
- [ ] 6.5 Créer tests pour les models (AuthorsModel, TomesGridModel)

## 7. Integration & Validation

- [ ] 7.1 Tester la navigation complète (Série → Édition → Volume → back → Édition)
- [ ] 7.2 Tester la préservation de l'état lors du retour
- [ ] 7.3 Tester les actions ajouter/retirer avec feedback
- [ ] 7.4 Validation UI - lancer l'application et vérifier les logs Qt/QML
- [ ] 7.5 Performance test sur RPi 4 (scroll, navigation, temps chargement)
- [ ] 7.6 Vérifier l'affichage sur écran 1280x720 (responsive design)
