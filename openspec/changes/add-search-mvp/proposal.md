# Change: Ajouter la recherche MVP

## Why
La recherche est essentielle pour trouver un manga dans le catalogue Mangacollec. Sans recherche, l'utilisateur ne peut pas ajouter de nouveaux mangas à sa collection. C'est une fonctionnalité critique pour rendre l'application utilisable.

## What Changes
- Ajout d'une page de recherche globale par titre de séries via l'API Mangacollec (GET /v2/search)
- Champ de recherche avec debounce de 300ms pour éviter les appels API excessifs
- Affichage des résultats sous forme de liste scrollable avec couverture, titre, type (manga/manhwa/etc), et nombre d'éditions
- Navigation depuis un résultat vers la fiche série détaillée
- Intégration de la page de recherche dans la barre latérale (SideBar) et dans le MainLayout

## Impact
- Affected specs: search (new)
- Affected code: SearchService, SearchViewModel, SearchResultModel, SearchPage.qml, MainLayout.qml, SideBar.qml
