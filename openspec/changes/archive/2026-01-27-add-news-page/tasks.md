# Tasks: Implémenter la page News

## 1. Infrastructure API

- [x] 1.1 Ajouter méthode `get_news()` dans `MangacollecApi`
  - Appeler `GET /v2/volumes/news`
  - Parser la réponse normalisée (volumes, editions, series, boxes, box_editions)
  - Retourner les données brutes pour traitement par le service

## 2. Domain & DTOs

- [x] 2.1 Créer `NewsItemDTO` dans `application/dtos/news_dto.py`
  - Champs : item_id, item_type, title, number, image_url, release_date, is_owned, is_last_volume, series_title, edition_title
  - Méthode `display_title` pour le titre formaté

- [x] 2.2 Créer les entités si manquantes dans `domain/entities/`
  - `Box` si non existant → Non nécessaire, traitement des données brutes dans NewsService
  - `BoxEdition` si non existant → Non nécessaire, traitement des données brutes dans NewsService

## 3. Service Layer

- [x] 3.1 Créer `NewsService` dans `application/services/news_service.py`
  - Méthode `get_news(force_refresh: bool = False) -> List[NewsItemDTO]`
  - Dénormaliser les données API en NewsItemDTO
  - Croiser avec les possessions pour `is_owned`
  - Détecter `is_last_volume` depuis edition.last_volume_number
  - Cache mémoire avec TTL 1h

## 4. Presentation Layer - Python

- [x] 4.1 Créer `NewsListModel` dans `presentation/models/news_list_model.py`
  - Hériter de `QAbstractListModel`
  - Rôles : itemId, itemType, title, number, imageUrl, releaseDate, isOwned, isLastVolume, seriesTitle, editionTitle, displayTitle

- [x] 4.2 Créer `NewsController` dans `presentation/controllers/news_controller.py`
  - Hériter de `BaseController`
  - Propriétés : `news` (NewsListModel), `count`, `loading`, `error`
  - Slots : `load()`, `refresh()`
  - Signal : `newsChanged`

## 5. Injection de dépendances

- [x] 5.1 Mettre à jour `app.py`
  - Instancier `NewsService`
  - Instancier `NewsController`
  - Exposer `newsController` au contexte QML

## 6. Presentation Layer - QML

- [x] 6.1 Créer `NewsCard.qml` dans `qml/components/cards/`
  - Propriétés : itemId, itemType, title, number, imageUrl, isOwned, isLastVolume, displayTitle
  - Badge "Dernier tome" conditionnel
  - Indicateur "Possédé" (check mark overlay)
  - Signal `clicked()`

- [x] 6.2 Créer `NewsPage.qml` dans `qml/pages/news/`
  - GridView avec délégué NewsCard
  - Loading state avec spinner
  - Empty state si pas de données
  - Error state avec retry
  - Chargement automatique quand la page devient visible

- [x] 6.3 Mettre à jour `SideBar.qml`
  - Activer l'entrée "Accueil" (index 0)
  - Supprimer le `enabled: false` ou la condition désactivante

- [x] 6.4 Mettre à jour `MainLayout.qml`
  - Importer et ajouter NewsPage au StackLayout (index 0)
  - Appeler `newsController.load()` quand la page devient visible

## 7. Tests

- [x] 7.1 Tests unitaires `NewsService`
  - Test get_news avec données mockées
  - Test détection is_owned
  - Test détection is_last_volume
  - Test cache TTL

- [x] 7.2 Tests unitaires `NewsListModel`
  - Test rowCount avec et sans items
  - Test roleNames
  - Test data pour tous les rôles
  - Test set_items et clear

- [ ] 7.3 Tests d'intégration (futur)
  - Test navigation SideBar → NewsPage
  - Test affichage de la grille

## 8. Validation

- [x] 8.1 Lancer l'application et vérifier
  - Pas d'erreurs QML dans la console ✓
  - Grille s'affiche correctement ✓
  - Badge "Dernier tome" visible sur les bons items ✓
  - Indicateur "Possédé" visible sur les items de la collection ✓
  - Navigation vers fiche détail → Non implémenté (console.log pour l'instant)
  - Pull-to-refresh → Bouton Actualiser disponible

## Dépendances entre tâches

```
1.1 → 3.1 → 4.1 → 4.2 → 5.1 → 6.2
         ↘      ↗
          2.1
                      ↘
6.1 ──────────────────→ 6.2 → 6.3 → 6.4
                              ↓
                            7.x → 8.1
```

## Notes

- Les tâches 2.2 et 7.x peuvent être parallélisées avec sub-agents
- La validation 8.1 nécessite un lancement manuel de l'application
- 81 tests passent (15 pour NewsService + 27 pour NewsListModel + 39 existants)
