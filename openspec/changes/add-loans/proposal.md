# Change: Ajout de la Gestion des prêts (V4+)

## Why
Certains collectionneurs prêtent des mangas à leurs proches et veulent suivre qui a emprunté quoi pour ne pas perdre leurs volumes précieux.

## What Changes
- Ajout de l'onglet "Prêts" dans la section Collection
- Gestion des emprunteurs (nom, catégorie : ami/famille/collègue)
- Création/retour de prêts (volume → emprunteur)
- Vue par emprunteur avec nombre de prêts actifs
- Affichage d'un badge "Prêté à X" sur les volumes prêtés

## Impact
- Affected specs: loans (new)
- Affected code: LoanService, LoanViewModel, LoansTab.qml, BorrowerForm.qml, VolumeDetailPage.qml
