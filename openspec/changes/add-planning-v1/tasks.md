# Tasks: Planning V1

## 1. Service

- [ ] 1.1 Créer `PlanningService` dans `services/planning_service.py`
  - Méthode `get_upcoming_releases()` — appel API GET `/v2/volumes/releases`
  - Méthode `filter_by_following_editions(releases, user_editions)` — filtrage par éditions suivies
  - Méthode `filter_by_publisher(releases, publisher_id)` — filtrage par éditeur
  - Pagination et gestion des erreurs réseau

- [ ] 1.2 Créer `ReleaseCache` dans `cache/release_cache.py`
  - Implémentation SQLite avec TTL 1 heure
  - Méthode `get_cached_releases()` — retour données en cache si valides
  - Méthode `set_cached_releases(releases)` — stockage avec timestamp
  - Méthode `is_cache_valid()` — vérification TTL

- [ ] 1.3 Intégration API dans `PlanningService`
  - Utiliser `ReleaseCache` pour éviter appels répétés
  - Gestion des erreurs réseau avec fallback au cache

## 2. Présentation (ViewModel)

- [ ] 2.1 Créer `PlanningViewModel` dans `viewmodels/planning_viewmodel.py`
  - Properties: `personalizedReleases` (QObject list model), `allReleases`, `selectedPublisher`, `currentTab` (Personalized|All)
  - Méthode `load_personalized_releases()` — récupération releases des éditions suivies
  - Méthode `load_all_releases()` — récupération releases globales
  - Méthode `set_publisher_filter(publisher_id)` — application filtre
  - Signal `releasesLoaded()`, `errorOccurred(message)`
  - Gestion état chargement et erreurs

- [ ] 2.2 Créer `ReleaseListModel` dans `models/release_list_model.py`
  - Héritage QAbstractListModel
  - Rôles: `id`, `coverUrl`, `seriesTitle`, `volumeNumber`, `releaseDate`, `publisher`, `isPossessed`
  - Tri par date croissante (sorties plus proches en premier)
  - Groupage par semaine à la présentation (non au modèle)

## 3. Interface (QML)

- [ ] 3.1 Créer `PlanningContainer.qml` — conteneur principal
  - SubNavBar avec 2 onglets : "Personnalisé", "Tout"
  - StateGroup pour switcher entre `PlanningPersonalise` et `PlanningAll`
  - Gestion du pull-to-refresh global

- [ ] 3.2 Créer `PlanningPersonalise.qml` — onglet sorties suivies
  - Affichage releases groupées par semaine (sections visuelles)
  - Grille responsive `ReleaseCard`
  - Loader pour `ReleaseCard`
  - Message "Aucune sortie suivie cette semaine" si vide
  - Affichage total de 3-4 semaines prochaines

- [ ] 3.3 Créer `PlanningAll.qml` — onglet sorties globales
  - ComboBox "Filtrer par éditeur" avec liste d'éditeurs
  - Affichage releases groupées par semaine
  - Grille responsive `ReleaseCard`
  - Message "Aucune sortie trouvée pour ce filtre" si vide

- [ ] 3.4 Créer `ReleaseCard.qml` — carte unique release
  - Image couverture (placeholder si indisponible)
  - Texte: `"[Série] - Tome [num]"`
  - Date sortie au format "Semaine du XX/XX"
  - Badge éditeur et statut possession (couleur badge)
  - Click handler: navigation vers VolumeDetail

- [ ] 3.5 Créer ou réutiliser `SubNavBar.qml`
  - 2 boutons horizontaux : "Personnalisé" | "Tout"
  - État sélectionné avec barre inférieure animée
  - Émission signal sur changement d'onglet

## 4. Intégration

- [ ] 4.1 Ajouter entrée "Planning" dans `SideBar.qml`
  - Icône calendrier
  - Navigation vers `PlanningContainer`

- [ ] 4.2 Ajouter `PlanningContainer` dans `MainLayout.qml`
  - Navigation stack correcte (breadcrumb: Home > Planning)

- [ ] 4.3 Connecter clics `ReleaseCard` → `VolumeDetailPage`
  - Passage du `volumeId` en paramètre

## 5. Tests

- [ ] 5.1 Tests unitaires `PlanningService`
  - Test `get_upcoming_releases()` avec mock API
  - Test filtrage par éditions suivies
  - Test filtrage par éditeur
  - Test erreur réseau

- [ ] 5.2 Tests unitaires `ReleaseCache`
  - Test cache valide (TTL non expiré)
  - Test cache expiré (refetch requis)
  - Test stockage et récupération

- [ ] 5.3 Tests unitaires `PlanningViewModel`
  - Test chargement releases personnalisées
  - Test chargement releases globales
  - Test changement filtre éditeur
  - Test signaux d'erreur

- [ ] 5.4 Tests modèle `ReleaseListModel`
  - Test nombre lignes correcte
  - Test rôles retournent données valides

## 6. Validation

- [ ] 6.1 Lancement application et vérification logs console (aucune erreur QML)
- [ ] 6.2 Navigation SideBar → Planning fonctionnelle
- [ ] 6.3 Affichage releases personnalisées avec au moins 1 édition suivie
- [ ] 6.4 Affichage releases globales
- [ ] 6.5 Filtre éditeur change l'affichage
- [ ] 6.6 Pull-to-refresh rafraîchit les données
- [ ] 6.7 Clic sur carte release → navigation vers VolumeDetail
- [ ] 6.8 Responsive design sur 1280x720 (écran tactile Raspberry Pi)
- [ ] 6.9 Capture screenshot pour documentation
