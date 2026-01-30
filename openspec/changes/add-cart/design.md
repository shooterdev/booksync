# Design: Panier d'achats

## Context
Le panier se synchronise avec les boutiques en ligne (BDFugue principalement) via l'API Mangacollec. Les prix et la disponibilité sont récupérés côté serveur. L'application s'exécute sur Raspberry Pi avec écran tactile 1280x720.

## Decisions
- Le panier est stocké côté API Mangacollec (source de vérité)
- Le cache local ne stocke qu'une copie pour affichage offline
- Les actions (ajout/retrait) passent toujours par l'API
- Badge compteur panier sur le bouton SideBar pour visibilité rapide

## Non-Goals
- Pas de gestion de plusieurs panier (un seul panier par utilisateur)
- Pas de coupons de réduction (V3+)
- Pas de historique d'achats (V3+)

## Risks
- Indisponibilité de la boutique → afficher prix "N/A" et message informatif
- Changement de prix → refresh à chaque ouverture du panier
- Stock limité → vérifier disponibilité via API avant achat
