## 1. Service
- [ ] 1.1 Créer LoanService (loan_service.py) - CRUD emprunteurs + prêts
- [ ] 1.2 Implémenter gestion emprunteurs (create, update, delete)
- [ ] 1.3 Implémenter création prêts avec vérification de disponibilité
- [ ] 1.4 Implémenter retour prêts et mise à jour statut volume
- [ ] 1.5 Implémenter recherche et filtrage par emprunteur

## 2. Présentation
- [ ] 2.1 Créer LoanViewModel
- [ ] 2.2 Créer BorrowerModel (QAbstractListModel)
- [ ] 2.3 Créer LoanModel (QAbstractListModel pour l'historique prêts)
- [ ] 2.4 Créer LoansTab.qml (liste emprunteurs + prêts actifs)
- [ ] 2.5 Créer BorrowerForm.qml (formulaire création/édition)
- [ ] 2.6 Créer LoanForm.qml (création prêt)
- [ ] 2.7 Créer LoanHistoryList.qml (historique des prêts retournés)

## 3. Intégration
- [ ] 3.1 Activer onglet "Prêts" dans CollectionContainer
- [ ] 3.2 Ajouter badge "Prêté à X" dans les fiches volume (VolumeDetailPage)
- [ ] 3.3 Implémenter bouton "Prêter" dans le menu d'actions du volume
- [ ] 3.4 Mettre à jour la liste Collection pour afficher l'indicateur "Prêté"

## 4. Tests & Validation
- [ ] 4.1 Tests unitaires LoanService (CRUD)
- [ ] 4.2 Tests unitaires LoanViewModel
- [ ] 4.3 Tests persistance TinyDB
- [ ] 4.4 Validation UI complète
- [ ] 4.5 Test de suppression emprunteur avec prêts actifs
