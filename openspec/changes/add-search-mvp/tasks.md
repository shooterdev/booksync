# Tasks: Add Search MVP

## 1. Backend Services

- [ ] 1.1 Créer SearchService (search_service.py) - encapsuler les appels à l'API Mangacollec GET /v2/search
- [ ] 1.2 Ajouter la gestion des erreurs de requête (timeouts, 404, 500)
- [ ] 1.3 Implémenter la mise en cache locale des résultats (TinyDB ou mémoire)

## 2. Frontend - ViewModel & Model

- [ ] 2.1 Créer SearchViewModel (search_viewmodel.py) - QObject avec query, results, loading, error_message properties
- [ ] 2.2 Implémenter le signal de recherche et la gestion de l'état de chargement
- [ ] 2.3 Créer SearchResultModel (QAbstractListModel) - modèle de liste pour afficher les résultats
- [ ] 2.4 Définir les rôles (id, title, type, editions_count, cover_url)

## 3. Frontend - UI Components

- [ ] 3.1 Créer SearchPage.qml - page principale avec champ de recherche et liste de résultats
- [ ] 3.2 Implémenter le debounce Timer (300ms) dans SearchPage.qml
- [ ] 3.3 Créer SearchResultItem.qml - composant pour afficher chaque résultat (couverture, titre, infos)
- [ ] 3.4 Ajouter les états visuels (vide, chargement, erreur, résultats)
- [ ] 3.5 Intégrer SearchPage dans MainLayout et SideBar avec navigation

## 4. Testing

- [ ] 4.1 Créer tests unitaires SearchService (mocking API)
- [ ] 4.2 Créer tests unitaires SearchViewModel (signals, state changes)
- [ ] 4.3 Créer tests pour SearchResultModel (pagination, roles)

## 5. Integration & Validation

- [ ] 5.1 Valider la navigation entre SearchPage et SeriesDetailPage
- [ ] 5.2 Validation UI - lancer l'application et vérifier les logs Qt/QML
- [ ] 5.3 Vérifier la performance du debounce et les appels API
- [ ] 5.4 Tester sur écran 1280x720 (Raspberry Pi)
