# Capability: Loans (Gestion des prêts)

Système de suivi des prêts de mangas auprès d'emprunteurs.

## ADDED Requirements

### Requirement: Borrower Management

Le système SHALL permettre de créer, modifier et supprimer des emprunteurs avec nom et catégorie (ami, famille, collègue).

#### Scenario: Création d'un emprunteur
- **GIVEN** l'utilisateur est sur la LoansTab
- **WHEN** il clique sur "Ajouter un emprunteur"
- **THEN** le système affiche BorrowerForm
- **AND** l'utilisateur remplit : nom, catégorie
- **AND** après confirmation, l'emprunteur est ajouté à la liste

#### Scenario: Édition d'un emprunteur
- **GIVEN** un emprunteur existe dans la liste
- **WHEN** l'utilisateur clique sur l'emprunteur
- **THEN** le système affiche BorrowerForm pré-rempli
- **AND** après modification, l'emprunteur est mis à jour

#### Scenario: Suppression d'un emprunteur
- **GIVEN** un emprunteur existe
- **WHEN** l'utilisateur clique sur le bouton "Supprimer"
- **THEN** le système affiche une confirmation
- **AND** le système affiche : "Cet emprunteur a X prêts actifs. Ils seront conservés à titre informatif."
- **AND** après confirmation, l'emprunteur est supprimé (soft-delete)

---

### Requirement: Loan Creation

Le système SHALL permettre de créer un prêt en associant un volume possédé à un emprunteur avec date.

#### Scenario: Création d'un prêt depuis le formulaire
- **GIVEN** l'utilisateur est sur LoansTab
- **WHEN** il clique sur "Créer un prêt"
- **THEN** le système affiche LoanForm
- **AND** l'utilisateur sélectionne : volume (de sa collection), emprunteur
- **AND** après confirmation, un prêt est créé avec date actuelle

#### Scenario: Création d'un prêt depuis la fiche volume
- **GIVEN** l'utilisateur consulte une VolumeDetailPage
- **WHEN** il clique sur "Prêter ce volume"
- **THEN** le système affiche LoanForm pré-rempli avec ce volume
- **AND** il sélectionne l'emprunteur et confirme

#### Scenario: Volume déjà prêté
- **GIVEN** un volume est déjà prêté
- **WHEN** l'utilisateur essaie de le prêter à nouveau
- **THEN** le système affiche un message d'erreur
- **AND** le message indique le nom de l'emprunteur actuel

#### Scenario: Sélection du volume
- **GIVEN** LoanForm est affichée
- **WHEN** l'utilisateur clique sur le champ "Volume"
- **THEN** le système affiche une liste des volumes possédés et non prêtés
- **AND** il peut chercher par titre ou numéro

---

### Requirement: Loan Return

Le système SHALL permettre de marquer un prêt comme retourné.

#### Scenario: Retour d'un prêt
- **GIVEN** un prêt actif est affiché dans LoansTab
- **WHEN** l'utilisateur clique sur "Marquer comme retourné"
- **THEN** le système affiche une confirmation
- **AND** après confirmation, le prêt est supprimé des prêts actifs
- **AND** le système enregistre la date de retour dans l'historique

#### Scenario: Historique des retours
- **GIVEN** le prêt a été marqué comme retourné
- **WHEN** l'utilisateur consulte "Historique des prêts"
- **THEN** le système affiche le prêt avec date de création et de retour

---

### Requirement: Borrower Loan List

Le système SHALL afficher la liste des emprunteurs avec le nombre de prêts actifs et les volumes prêtés.

#### Scenario: Affichage de la liste emprunteurs
- **GIVEN** LoansTab est affichée
- **WHEN** le système charge les données
- **THEN** le système affiche une liste d'emprunteurs avec :
  - Nom et catégorie de l'emprunteur
  - Nombre de volumes prêtés (ex: "3 volumes")
  - Avatar ou icône de catégorie

#### Scenario: Expansion pour voir les prêts
- **GIVEN** un emprunteur est affiché
- **WHEN** l'utilisateur clique sur l'emprunteur
- **THEN** le système expande la ligne pour afficher les volumes prêtés
- **AND** chaque volume affiche couverture, titre, date du prêt

#### Scenario: Aucun emprunteur
- **GIVEN** aucun emprunteur n'existe
- **WHEN** LoansTab est affichée
- **THEN** le système affiche un message "Aucun emprunteur. Créez un nouveau profil d'emprunteur."

---

### Requirement: Loan Indicator

Le système SHALL afficher un indicateur visuel "Prêté" sur les volumes prêtés dans la collection et la fiche volume.

#### Scenario: Badge dans la collection
- **GIVEN** un volume est prêté
- **WHEN** la liste Collection est affichée
- **THEN** le volume affiche un badge "Prêté à [nom]"

#### Scenario: Indicateur dans la fiche volume
- **GIVEN** l'utilisateur consulte VolumeDetailPage pour un volume prêté
- **WHEN** la page s'affiche
- **THEN** le système affiche en haut un bandeau "Prêté à [nom] depuis [date]"
- **AND** un bouton "Marquer comme retourné" est disponible

#### Scenario: Pas d'indicateur pour volume non prêté
- **GIVEN** un volume n'est pas prêté
- **WHEN** il est affiché
- **THEN** aucun badge ou indicateur n'apparaît

---

### Requirement: Loan Search and Filter

Le système SHALL permettre de chercher et filtrer les prêts par emprunteur ou par volume.

#### Scenario: Recherche par emprunteur
- **GIVEN** LoansTab est affichée
- **WHEN** l'utilisateur tape un nom dans le champ "Chercher un emprunteur"
- **THEN** la liste est filtrée en temps réel

#### Scenario: Recherche par volume
- **GIVEN** LoansTab est affichée
- **WHEN** l'utilisateur tape le titre d'un volume dans le champ "Chercher un volume"
- **THEN** les prêts contenant ce volume sont affichés

#### Scenario: Filtrage par catégorie
- **GIVEN** LoansTab est affichée
- **WHEN** l'utilisateur clique sur le filtre "Catégorie"
- **THEN** il peut sélectionner ami, famille, ou collègue
- **AND** la liste est filtrée selon la catégorie
