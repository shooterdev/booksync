# Tasks: Sections Collection

## Phase 1: Infrastructure de navigation

### 1.1 Composant SubNavBar
- [x] Creer `qml/layouts/SubNavBar.qml` — Barre de navigation horizontale avec onglets
- [x] Props: `model` (liste de tabs), `currentIndex`, signal `tabChanged`
- [x] Style: Onglets horizontaux, indicateur actif, tooltip pour onglets desactives

### 1.2 CollectionContainer
- [x] Creer `qml/pages/collection/CollectionContainer.qml`
- [x] Integrer SubNavBar avec les sections: Pile | Collection | Completer | Envies | Stats | Historique
- [x] StackLayout pour charger les pages selon l'onglet actif
- [x] Desactiver les onglets V2+ (grises avec tooltip "Bientot disponible")

### 1.3 Mise a jour MainLayout
- [x] Modifier `MainLayout.qml` pour charger CollectionContainer au lieu de CollectionPage directement

---

## Phase 2: Service Wishlist (Envies)

### 2.1 DTO et entites
- [x] Creer `WishlistItemDTO` — volume souhaite avec infos serie/edition
- [x] Ajouter entite `FollowEdition` dans domain/entities

### 2.2 Cache FollowEditions
- [x] Creer `FollowEditionCache` dans infrastructure/cache
- [x] Methodes: `get_all()`, `save()`, `delete()`, `is_stale()`

### 2.3 API FollowEditions
- [x] Ajouter `get_follow_editions()` dans `MangacollecApi`
- [x] Ajouter `follow_edition()` et `unfollow_edition()`

### 2.4 WishlistService
- [x] Creer `application/services/wishlist_service.py`
- [x] `get_wishlist()` — Calcule les volumes non possedes des editions suivies
- [x] `get_wishlist_count()` — Nombre total de volumes souhaites
- [x] Sync depuis API + cache local

### 2.5 WishlistController et Model
- [x] Creer `WishlistController` — expose wishlist au QML
- [x] Creer `WishlistModel` — QAbstractListModel avec roles

---

## Phase 3: Service Completer (tomes manquants)

### 3.1 MissingVolumeService
- [x] Creer `MissingVolumeService` — Detecte les trous dans les series
- [x] Logique: Pour chaque edition possedee, liste les numeros manquants

### 3.2 MissingVolumeDTO
- [x] Creer DTO avec: volume_id, number, series_title, edition_title, image_url

### 3.3 CompleteController et Model
- [x] Creer `CompleteController` — expose tomes manquants au QML
- [x] Creer `MissingVolumeModel` — QAbstractListModel

---

## Phase 4: Pages QML

### 4.1 Page Envies
- [x] Creer `EnviesPage.qml`
- [x] Grille de VolumeCard avec les volumes souhaites
- [x] Message vide: "Suivez des editions pour voir vos envies"

### 4.2 Page Completer
- [x] Creer `CompletePage.qml`
- [x] Grille avec tomes manquants
- [x] Message vide: "Votre collection est complete !"

---

## Phase 5: Integration et tests

### 5.1 Injection dependances
- [x] Mettre a jour `app.py` avec WishlistService, MissingVolumeService
- [x] Enregistrer les nouveaux controllers dans le contexte QML
- [x] Mettre a jour tous les `__init__.py`

### 5.2 Tests
- [x] Tests unitaires WishlistService (19 tests, 86% coverage)
- [x] Tests unitaires Controllers (46 tests)
- [ ] Test integration navigation entre sections

### 5.3 Validation UI
- [x] Lancer l'app et verifier la navigation SubNavBar
- [x] Verifier l'affichage Envies et Completer
- [x] Verifier les etats vides et de chargement
