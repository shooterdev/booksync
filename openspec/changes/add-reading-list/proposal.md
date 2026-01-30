# Change: Ajout de la Pile à lire (V2)

## Why
Les collectionneurs veulent suivre leur progression de lecture par série pour savoir où ils en sont et ce qu'il leur reste à lire parmi les tomes possédés.

## What Changes
- Ajout de l'onglet "Pile à lire" dans la section Collection (onglet SubNavBar)
- StatusRead : barre de statut avec volumes lus / possédés et progression globale
- SerieReadListView : liste des séries en cours de lecture
- SerieReadCard : ligne par série avec couverture, titre, barre de progression, actions pause/terminer
- ReadingService pour gérer les lectures (marquer lu, pause, terminer)

## Impact
- Affected specs: reading-list (new)
- Affected code: ReadingService, ReadingViewModel, ReadingTab.qml, CollectionContainer.qml (nouvel onglet)
