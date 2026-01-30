# Change: Ajout de l'Historique Collection et Lecture (V2)

## Why
L'utilisateur veut consulter un journal chronologique de ses ajouts à la collection et de ses lectures pour suivre son activité au fil du temps.

## What Changes
- Ajout de 2 onglets dans la section Collection : "Historique Collection" et "Historique Lecture"
- Journal chronologique par année/mois (ajouts) et par semaine/mois/année (lectures)
- HistoryService pour agrégation temporelle des données

## Impact
- Affected specs: collection-history (new)
- Affected code: HistoryService, HistoryViewModel, HistoryCollectionTab.qml, HistoryReadingTab.qml
