## Context
Les prêts doivent être persistés côté serveur (Mangacollec API ou Auth API) ou en base de données locale (TinyDB). Chaque prêt est associé à un volume, un emprunteur, et une date de création. Le retour d'un prêt supprime l'association.

## Goals
- Permet aux collectionneurs de ne pas perdre track de leurs prêts
- Affichage simple et rapide : qui a quoi, depuis quand
- Temps de recherche d'un prêt < 1 seconde

## Non-Goals
- Pas de rappels/notifications pour les prêts expirés (V5+)
- Pas de contrats de prêt ou conditions
- Pas de partage entre utilisateurs (chaque profil a ses emprunteurs)

## Decisions
- Emprunteurs stockés localement (TinyDB) partitionnés par user_id
- Prêts stockés localement avec : user_id, volume_id, borrower_id, loan_date, return_date
- L'onglet "Prêts" remplace ou complète l'onglet "En cours" de la Collection

## Risks
- Suppression d'un emprunteur → migration des prêts ou soft-delete
- Suppression d'un volume prêté → affichage du volume supprimé avec indication
