# Change: Ajout des Statistiques de collection (V2)

## Why
Les collectionneurs veulent visualiser leur collection sous forme de graphiques pour comprendre la répartition par éditeur, genre, et voir l'évolution de leurs ajouts.

## What Changes
- Ajout de l'onglet "Statistiques" dans la section Collection
- Dashboard avec graphiques : répartition par éditeur (camembert), par genre (barres), derniers ajouts, dernières lectures
- StatsService pour calculer les statistiques depuis le cache

## Impact
- Affected specs: collection-stats (new)
- Affected code: StatsService, StatsViewModel, StatsTab.qml, Qt Charts
