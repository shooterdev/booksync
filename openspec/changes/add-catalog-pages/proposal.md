# Change: Ajouter les pages de catalogue détaillées

## Why
Les fiches détaillées (Volume, Série, Édition) sont indispensables pour consulter les informations complètes d'un manga et effectuer des actions (ajouter/retirer de la collection). Sans ces fiches, l'utilisateur ne peut pas explorer en profondeur les séries et gérer sa collection efficacement.

## What Changes
- Ajout de 3 pages de détail navigables via StackView (pile de pages)
- Fiche Volume : couverture plein écran, titre, numéro, date de sortie, ISBN, résumé, prix Amazon, statistiques communautaires, actions (ajouter/retirer), navigation tomes précédent/suivant
- Fiche Série : titre, type (manga/manhwa/etc), genres, auteurs avec rôles (illustrateur, scénariste, etc), liste scrollable des éditions
- Fiche Édition : titre, éditeur, statut (en cours/terminée/arrêt commercial), nombre de volumes parus/à paraître, grille de tous les tomes avec indicateurs de possession
- Navigation inter-fiches : Série ↔ Édition, Édition ↔ Volume, avec bouton retour

## Impact
- Affected specs: catalog (new)
- Affected code: CatalogService, VolumeDetailPage.qml, SeriesDetailPage.qml, EditionDetailPage.qml, MainLayout.qml (StackView navigation), CollectionService
